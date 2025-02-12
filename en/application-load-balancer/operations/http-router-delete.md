---

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---
# Delete an HTTP router

To delete an HTTP router:

{% list tabs %}

- Management console

  1. In the [management console]({{ link-console-main }}), select the folder that the HTTP router belongs to.

  1. Select **{{ alb-name }}**.

  1. Select the VM and click ![image](../../_assets/dots.svg).

  1. In the menu that opens, select **Delete**.

      To do this with multiple HTTP routers, select the routers to delete from the list and click **Delete** at the bottom of the screen.

  1. Confirm the deletion.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. View a description of the CLI command for deleting an HTTP router:

     ```
     yc alb http-router delete --help
     ```

  1. Run the command, specifying the HTTP router name:

     ```
     yc alb http-router delete --name <HTTP router name>
     ```

     To check the deletion, get a list of HTTP routers by running the command:

     ```
     yc alb http-router list
     ```

{% list tabs %}

