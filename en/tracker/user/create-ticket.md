---

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---
# Creating issues

You can create issues in any of the available [queues](../queue-intro.md). Select the queue that best matches the topic of the issue. If you don't know which queue to create the issue in, check with the project manager or your organization's admin.


## Create an issue {#section_bh1_44f_mz}

To create an issue:

1. On the top panel in {{ tracker-name }}, click **Create issue**.

1. Select the **Queue** to create the issue in.


1. Select the issue **Type**.
Each queue can have its own [set of issue types](../manager/add-ticket-type.md). If you don&apos;t see an appropriate type in the list, contact the queue owner.

1. Enter a title in **Summary** field.

1. Fill in the **Description**. Use [wiki markup](wiki-markup.md) to format text.

1. Click **Choose files** to attach files or images to the issue.
To insert images into the text, click ![](../../_assets/tracker/add-image.png).

1. Enter the username or name of the assignee, or click **Me** to assign the issue to yourself.
You can also leave this field empty to assign someone later.

1. Set the issue **Priority**.

1. If your issue is related to other issues, list them in **Links**.

1. You can specify [additional settings](create-param.md#section_ymd_ycj_1gb) if necessary. To do this, add fields using the ![](../../_assets/tracker/task-params-btn.png) button and fill them in.

1. Click **Create** at the bottom of the page.

    {% note tip %}

    If you need to create a lot of similar issues, make a [template](ticket-template.md) for them.

    {% endnote %}

## Issue key {#key}

Each new issue is automatically assigned a unique key that identifies it. The issue key consists of the [queue key](../manager/create-queue.md#key) and the issue number (for example, `TEST-1234`). The key is displayed under the issue's title on the issue page.

Here is what you can do with issue keys:

- [Create links to other issues](ticket-links.md).
- Paste the queue key into an issue's description or comment body to put a link to another issue there. It will be converted into a clickable link.
- To directly access an issue at: `({{ link-tracker-example-1 }})`.

## Create a sub-issue {#section_dqs_34n_jz}

Complex tasks can be divided into simpler sub-issues and tracked separately.

To create a sub-issue:

1. Open the issue page to create a sub-issue for.

1. Choose **Actions** → **Create sub-issue**.

1. Fill in the fields the same way as when creating a new issue.

1. Click **Create**.

## Share issue templates {#section_fhz_pn4_xgb}

You can share an issue template with a coworker before finalizing it. This could come in handy in some situations (e.g. quickly creating a batch of similar issues).

Follow these steps to share an issue template:

1. Set up issue details as described in [{#T}](#section_bh1_44f_mz).

1. Click **Get a link to a filled-out form** at the bottom of the issue creation page.

1. Copy the link and send it to your coworker however you find convenient.

Clicking this link sends you to an issue creation window with its fields filled out. You can save the link and use it as a template for quick issue creation.

#### Next step

[View your issues](my-tickets.md)
