---

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---
# Delete a backend group

To delete a backend group:

{% list tabs %}

- Management console

  1. In the [management console]({{ link-console-main }}), select the folder that the backend group belongs to.

  1. Select **{{ alb-name }}**.

  1. In the left menu, select **Backend groups**.

  1. Select the backend group and click ![image](../../_assets/dots.svg).

  1. In the menu that opens, select **Delete**.

      To do this with multiple groups, select the groups to delete from the list and click **Delete** at the bottom of the screen.

  1. Confirm the deletion.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. View a description of the CLI command for deleting a backend group:

     ```
     yc alb backend-group delete --help
     ```

  1. Run the command, specifying the backend group name you want to delete:

     ```
     yc alb backend-group delete --name <backend group name>
     ```

     To check the deletion, get a list of the backend group by running the command:

     ```
     yc alb backend-group list
     ```

{% list tabs %}

