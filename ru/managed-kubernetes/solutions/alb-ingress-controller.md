---

__system: {"dislikeVariants":["Нет ответа на мой вопрос","Рекомендации не помогли","Содержание не соответствует заголовку","Другое"]}
---
# Настройка {{ alb-name }} Ingress-контроллера

Сервис [{{ alb-full-name }}](../../application-load-balancer/) используется для балансировки нагрузки и распределения трафика между приложениями. Чтобы с его помощью управлять трафиком к приложениям, запущенным в кластере {{ managed-k8s-name }}, необходим [Ingress-контроллер](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/).

Чтобы настроить доступ к запущенным в кластере приложениям через {{ alb-name }}:

1. [{#T}](#create-namespace-and-secret).
1. [{#T}](#install-alb).
1. [{#T}](#create-ingress-and-apps).
1. [{#T}](#verify-setup).

## Перед началом работы {#before-you-begin}

1. {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

1. [Создайте](../../iam/operations/sa/create.md) сервисный аккаунт, необходимый для работы Ingress-контроллера.

    1. [Назначьте ему роли](../../iam/operations/sa/assign-role-for-sa.md):

       * `editor` — для создания необходимых ресурсов;
       * `certificate-manager.certificates.downloader` — для работы с сертификатами, зарегистрированными в сервисе [{{ certificate-manager-full-name }}](../../certificate-manager/).

    1. Создайте для него [авторизованный ключ](../../iam/operations/sa/create-access-key.md) и сохраните в файл `sa-key.json`:

        ```bash
        yc iam key create \
           --service-account-name <имя сервисного аккаунта для Ingress-контроллера> \
           --output sa-key.json
        ```

1. [Зарегистрируйте публичную доменную зону и делегируйте домен](../../dns/operations/zone-create-public.md).

1. Если у вас уже есть сертификат для доменной зоны, [добавьте сведения о нем](../../certificate-manager/operations/import/cert-create.md) в сервис {{ certificate-manager-name }}. В противном случае [создайте новый сертификат от Let's Encrypt](../../certificate-manager/operations/managed/cert-create.md).

1. [Создайте кластер {{ managed-k8s-short-name }} ](../operations/kubernetes-cluster/kubernetes-cluster-create.md) со следующими настройками:

    * **Версия {{ k8s }}**: не ниже 1.19.
    * **Публичный адрес**: `Автоматически`.

1. [Создайте группу узлов](../operations/node-group/node-group-create.md) любой подходящей конфигурации с версией {{ k8s }} не ниже 1.19.

1. Настройте [группы безопасности](../../vpc/concepts/security-groups.md) кластера.

    [Добавьте правила](../../vpc/operations/security-group-update.md#add-rule):

    {% cut "Для входящего трафика" %}

    * Для балансировщика нагрузки:

        * диапазон портов: `{{ port-any }}`;
        * протокол: `TCP`;
        * тип источника: `CIDR`;
        * назначение: `198.18.235.0/24` и `198.18.248.0/24`.

    * Для передачи служебного трафика между [мастером](../concepts/index.md#master) и узлами:

        * диапазон портов: `{{ port-any }}`;
        * протокол: любой (`Any`);
        * тип источника: `Группа безопасности`;
        * Группа безопасности — текущая (`Self`).

    * Для передачи трафика между [подами](../concepts/index.md#pod) и [сервисами](../concepts/index.md#service):

        * диапазон портов: `{{ port-any }}`;
        * протокол: любой (`Any`);
        * тип источника: `CIDR`;
        * назначение: укажите диапазоны адресов подсетей, созданных вместе с кластером, например:
            * `10.96.0.0/16`;
            * `10.112.0.0/16`.

    * Для проверки работоспособности узлов с помощью ICMP-запросов из подсетей внутри {{ yandex-cloud }}:

        * протокол: `ICMP`;
        * тип источника: `CIDR`;
        * назначение: диапазоны адресов подсетей внутри {{ yandex-cloud }}, из которых будет осуществляться диагностика кластера, например:
            * `10.0.0.0/8`;
            * `192.168.0.0/16`;
            * `172.16.0.0/12`.

    * Для доступа к API {{ k8s }} и управления кластером через `kubectl`:

        * порты: `{{ port-https }}`, `6443`;
        * протокол: `TCP`;
        * тип источника: `CIDR`;
        * назначение: укажите диапазон адресов подсетей, из которых будете управлять кластером, например:
            * `85.23.23.22/32` — для внешней сети;
            * `192.168.0.0/24` — для внутренней сети.

    * Для доступа к запущенным на узлах сервисам из интернета и подсетей внутри {{ yandex-cloud }}:

        * диапазон портов: `30000-32767`;
        * протокол: `TCP`;
        * тип источника: `CIDR`;
        * назначение: `0.0.0.0/0`.

    {% endcut %}

    {% cut "Для исходящего трафика" %}

    Для подключения хостов кластера к внешним ресурсам (например, для скачивания образов с Docker Hub, работы с [{{ objstorage-full-name }}](../solutions/backup.md) и т. д.):

    * диапазон портов: `{{ port-any }}`;
    * протокол: любой (`Any`);
    * тип источника: `CIDR`;
    * назначение: `0.0.0.0/0`.

    {% endcut %}

1. [Установите менеджер пакетов Helm](https://helm.sh/ru/docs/intro/install/).

1. [Установите kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) и [настройте его на работу с созданным кластером](../operations/kubernetes-cluster/kubernetes-cluster-get-credetials.md).

1. Убедитесь, что вы можете подключиться к кластеру с помощью `kubectl`:

    ```bash
    kubectl cluster-info
    ```

## Создайте пространство имен и секрет для {{ alb-name }} Ingress-контроллера {#create-namespace-and-secret}

1. Создайте [пространство имен](../concepts/index.md#namespace):

    ```bash
    kubectl create namespace yc-alb-ingress
    ```

1. Создайте секрет:

    ```bash
    kubectl create secret generic yc-alb-ingress-controller-sa-key \
            --namespace yc-alb-ingress \
            --from-file=sa-key.json
    ```

    Подробнее о секретах см. в [документации {{ k8s }}](https://kubernetes.io/docs/concepts/configuration/secret/).

## Установите {{ alb-name }} Ingress-контроллер {#install-alb}

Для установки [Helm chart](https://helm.sh/docs/topics/charts/) с [Ingress-контроллером](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) выполните команды:

```bash
export HELM_EXPERIMENTAL_OCI=1
helm chart pull cr.yandex/crpsjg1coh47p81vh2lc/yc-alb-ingress-controller-chart:v0.0.4
helm chart export cr.yandex/crpsjg1coh47p81vh2lc/yc-alb-ingress-controller-chart:v0.0.4
helm install \
     --namespace yc-alb-ingress \
     --set folderId=<идентификатор каталога> \
     --set "subnetIds={разделенные запятой идентификаторы подсетей, в которых размещены узлы кластера}" \
     --set clusterId=<идентификатор кластера> \
     yc-alb-ingress-controller ./yc-alb-ingress-controller/
```

Идентификатор кластера можно [получить со списком кластеров в каталоге](../operations/kubernetes-cluster/kubernetes-cluster-list.md).

## Создайте Ingress-контроллер и тестовые приложения {#create-ingress-and-apps}

1. Получите идентификатор [добавленного ранее](#before-you-begin) TLS-сертификата:

    {% list tabs %}

    * Консоль управления

        1. Перейдите на страницу каталога и выберите сервис **{{ certificate-manager-name }}**.
        1. Нажмите на имя нужного сертификата.

    * CLI

        Выполните команду:

        ```bash
        yc certificate-manager certificate list
        ```

        Результат выполнения команды:

        ```text
        +----------------------+-----------------+--------------------------------+---------------------+----------+--------+
        |          ID          |      NAME       |            DOMAINS             |      NOT AFTER      |   TYPE   | STATUS |
        +----------------------+-----------------+--------------------------------+---------------------+----------+--------+
        | <идентификатор>      | <имя>           | <доменное имя>                 | 2022-01-06 17:19:37 | IMPORTED | ISSUED |
        +----------------------+-----------------+--------------------------------+---------------------+----------+--------+
        ```

    * API

        Воспользуйтесь методом API [list](../../certificate-manager/api-ref/Certificate/list.md): передайте значение идентификатора требуемого каталога в параметре `folderId` запроса.

    {% endlist %}

1. В отдельном каталоге создайте файл `ingress.yaml` и укажите в нем [делегированное ранее доменное имя](#before-you-begin) и идентификатор сертификата:

    ```yaml
    ---
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: alb-demo-tls
    spec:
      tls:
        - hosts:
            - <доменное имя>
          secretName: yc-certmgr-cert-id-<идентификатор TLS-сертификата>
      rules:
        - host: <доменное имя>
          http:
            paths:
              - path: /app1
                pathType: Prefix
                backend:
                  service:
                    name: alb-demo-1
                    port:
                      number: 80
              - path: /app2
                pathType: Prefix
                backend:
                  service:
                    name: alb-demo-2
                    port:
                      number: 80
              - pathType: Prefix
                path: "/"
                backend:
                  service:
                    name: alb-demo-2
                    port:
                      number: 80
    ```

1. В том же каталоге создайте файлы приложений `demo-app-1.yaml` и `demo-app-2.yaml`:

    {% cut "demo-app1.yaml" %}

    ```yaml
    ---
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: alb-demo-1
    data:
      nginx.conf: |
        worker_processes auto;
        events {
        }

        http {
          server {
            listen 80 ;
            location = /_healthz {
              add_header Content-Type text/plain;
              return 200 'ok';
            }
            location / {
              add_header Content-Type text/plain;
              return 200 'Index';
            }
            location = /app1 {
              add_header Content-Type text/plain;
              return 200 'This is APP#1';
            }
          }
        }

    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: alb-demo-1
      labels:
        app: alb-demo-1
        version: v1
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: alb-demo-1
      strategy:
        type: RollingUpdate
        rollingUpdate:
          maxSurge: 1
          maxUnavailable: 0
      template:
        metadata:
          labels:
            app: alb-demo-1
            version: v1
        spec:
          terminationGracePeriodSeconds: 5
          volumes:
            - name: alb-demo-1
              configMap:
                name: alb-demo-1

          containers:
            - name: alb-demo-1
              image: nginx:latest
              ports:
                - name: http
                  containerPort: 80
              livenessProbe:
                httpGet:
                  path: /_healthz
                  port: 80
                initialDelaySeconds: 3
                timeoutSeconds: 2
                failureThreshold: 2
              volumeMounts:
                - name: alb-demo-1
                  mountPath: /etc/nginx
                  readOnly: true
              resources:
                limits:
                  cpu: 250m
                  memory: 128Mi
                requests:
                  cpu: 100m
                  memory: 64Mi
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: alb-demo-1
    spec:
      selector:
        app: alb-demo-1
      type: NodePort
      ports:
        - name: http
          port: 80
          targetPort: 80
          protocol: TCP
          nodePort: 30081
    ```

    {% endcut %}

    {% cut "demo-app2.yaml" %}

    ```yaml
    ---
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: alb-demo-2
    data:
      nginx.conf: |
        worker_processes auto;
        events {
        }

        http {
          server {
            listen 80 ;
            location = /_healthz {
              add_header Content-Type text/plain;
              return 200 'ok';
            }
            location / {
              add_header Content-Type text/plain;
              return 200 'Add app#';
            }
            location = /app2 {
              add_header Content-Type text/plain;
              return 200 'This is APP#2';
            }
          }
        }

    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: alb-demo-2
      labels:
        app: alb-demo-2
        version: v1
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: alb-demo-2
      strategy:
        type: RollingUpdate
        rollingUpdate:
          maxSurge: 1
          maxUnavailable: 0
      template:
        metadata:
          labels:
            app: alb-demo-2
            version: v1
        spec:
          terminationGracePeriodSeconds: 5
          volumes:
            - name: alb-demo-2
              configMap:
                name: alb-demo-2

          containers:
            - name: alb-demo-2
              image: nginx:latest
              ports:
                - name: http
                  containerPort: 80
              livenessProbe:
                httpGet:
                  path: /_healthz
                  port: 80
                initialDelaySeconds: 3
                timeoutSeconds: 2
                failureThreshold: 2
              volumeMounts:
                - name: alb-demo-2
                  mountPath: /etc/nginx
                  readOnly: true
              resources:
                limits:
                  cpu: 250m
                  memory: 128Mi
                requests:
                  cpu: 100m
                  memory: 64Mi
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: alb-demo-2
    spec:
      selector:
        app: alb-demo-2
      type: NodePort
      ports:
        - name: http
          port: 80
          targetPort: 80
          protocol: TCP
          nodePort: 30082
    ```

    {% endcut %}

1. Чтобы создать [Ingress-контроллер](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) и приложения, выполните команду:

    ```bash
    kubectl apply -f .
    ```

1. Дождитесь создания Ingress-контроллера и получения им публичного IP-адреса, это может занять несколько минут:

    ```bash
    kubectl get ingress alb-demo-tls
    ```

    Ожидаемый результат — непустое значение в поле `ADDRESS` для созданного Ingress-контроллера:

    ```bash
    NAME           CLASS    HOSTS                  ADDRESS         PORTS     AGE
    alb-demo-tls   <none>   <доменное имя>         <IP-адрес>      80, 443   15h
    ```

    По конфигурации Ingress-контроллера будет автоматически развернут [L7-балансировщик](../../application-load-balancer/concepts/application-load-balancer.md).

## Убедитесь в доступности приложений кластера {{ k8s }} через {{ alb-name }} {#verify-setup}

1. [Добавьте A-запись в зону](../../dns/operations/resource-record-create.md) вашего домена. В поле **Значение** укажите публичный IP-адрес Ingress-контроллера.
1. [Настройте группы безопасности балансировщика](../../application-load-balancer/concepts/application-load-balancer.md#security-groups).
1. Откройте в браузере URI приложений:

    ```http
    https://<ваш домен>/app1
    https://<ваш домен>/app2
    ```

Убедитесь, что приложения доступны через {{ alb-name }} и возвращают страницы с текстом `This is APP#1` и `This is APP#2` соответственно.
