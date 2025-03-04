---
title: 'Deploy requests'
subtitle: 'Learn how to create and revert non-blocking schema changes with PlanetScale deploy requests.'
date: '2023-04-05'
---

## Overview

Deploy requests are an integral part of the [PlanetScale workflow](/docs/concepts/planetscale-workflow). Database branching, coupled with deploy requests, allows you to **deploy non-blocking schema changes to your production database with zero downtime**. You can also [undo deployments](#revert-a-schema-change) without losing any data that was written during that time.

{% vimeo src="https://player.vimeo.com/video/763914107" caption="Demonstration of how to make schema changes with deploy requests" /%}

## Create a deploy request

**Note**: Your database must have a **production branch with [safe migrations](/docs/concepts/safe-migrations) enabled** before you can create a deploy request.

1. Click on "**Branches**".
2. Select the development branch you want to deploy to the production branch.
3. This page shows you a diff of the schema against its parent branch.
4. To the right of the page, you'll see a dropdown that says "**Deploy to**".
5. Select the branch you want to.
6. Optionally, add a comment about the deploy request.
7. Click "**Create deploy request**".

![Example of deploy request on branch page](/assets/docs/concepts/deploy-requests/deploy-request-page.png)

## Review and deploy a deploy request

Once you create a deploy request, you or your team can review it and, optionally, approve it before deploying it to production.

PlanetScale will check if the request is deployable. This process includes checking for issues like [foreign key constraints](/docs/learn/operating-without-foreign-key-constraints), [missing unique keys](/docs/learn/change-single-unique-key), and more. We also check if there are any known conflicts with the production schema that could prevent a clean merge. While we attempt to find all possible conflicts, it is ultimately up to you to confirm merge details.

1. Click the "**Deploy requests**" tab on the database overview page.
2. Select the open deploy request you want to review.
3. Under "**Summary**", you'll see if the request is deployable.
4. To review the schema changes, click the "**Schema changes**" tab.
5. You'll see the proposed changes here. New additions are highlighted in green, and deletions are highlighted in red.
6. If you have required deploy requests to be approved before deployment, other users in your Organization will see the option to "**Approve changes**" or "**Leave a comment**" on the "**Schema changes**" tab.

   ![PlanetScale deploy request - approve changes](/assets/docs/concepts/deploy-requests/approve.png)

7. Once the request is approved, if required, it's ready to be added to the deploy queue. Click on the "Summary" tab, and you'll see the option to deploy.
8. Here, you also have the option to enable [**Gated Deployments**](#gated-deployments), which gives you the power to control exactly when the migration cuts over. You'll see an "**Auto-apply changes**" checkbox, which is checked by default. If you uncheck this, you will get the option to apply the changes once the schema changes are complete. If you leave it checked, it will auto-deploy as soon as it's ready.
9. When you're ready to deploy, click "**Add changes to deploy queue**". The deployment will begin or be queued if there are other pending deployments. You also have a chance to enable [Gated Deployments](#gated-deployments) in this step.
10. If you enabled Gated Deployments (step 8), you can click "**Apply changes**" to merge the deployment to production once it completes.
11. After you deploy, you have **30 minutes to "undo"** it using our [schema revert feature](#revert-a-schema-change).

If you would like to require an administrator's approval before a request can be deployed, go to the "**Settings**" page for your database and check the "**Require administrator approval for deploy requests**" box. You must be an Organization Administrator to enable this restriction. Please note you will not be able to approve your own deploy requests.

## Close a deploy request

If you decide you don't want to proceed with a deploy request, you can easily close it.

1. Click the "**Deploy requests**" tab on the database overview page.
2. Select the request you want to close.
3. Click on the "**Close deploy request**" button on the right-hand side.

## Revert a schema change

If you ever merge a deploy request, only to realize you need to undo it, PlanetScale can handle that! You have the option to revert a recently deployed schema change while maintaining data that was written to the original schema during that time.

{% callout %}
The schema revert feature is currently in limited beta. You must enroll your database and opt-in to the terms to access this
feature.
{% /callout %}

### How to revert a schema change

You can revert a deployment for **up to 30 minutes** after the deploying. After the 30 minute period is up, the deployment becomes permanent, and you will no longer have the option to revert.

You must first agree to the beta terms before you can use the schema revert feature. Go to the database Settings page, click "**Beta features**", find the "**Revert schema changes**" section, and click "**Enroll**" to opt-in.

{% vimeo src="https://player.vimeo.com/video/763913760" caption="Demonstration of how to revert a schema change" /%}

1. Select the deploy request you want to revert.
2. If you're not already enrolled, you'll see a Beta banner underneath the comment box on the deploy request page. Click "**Enroll database**" to opt-in to the limited beta terms.
3. To revert the schema changes made with the deploy request, click "**Revert changes**" and confirm.
4. We will immediately revert your production database back to its previous schema.
5. Any data that was written to the original schema in the time between deploying and reverting will remain in your database after the revert.
6. The Deploy Request will be closed, but the branch will remain for you to continue development on if you choose.

### When is data not retained

There are some scenarios where some data is not retained when you revert your changes.

1. You add a table or column to your schema and then revert it. If any data was written to those newly introduced fields between deployment and reverting, that data will not be retained upon revert, as the fields will no longer exist.

### When are you unable to revert a schema change

There are also some edge cases where reverting a schema change is not possible. We will always attempt to revert, but if there are scenarios where your data integrity is at risk, we will not proceed with the revert. The following are some cases where a revert will fail:

1. If you deploy a schema change that expands the length of some column, such as changing from `VARCHAR(10)` to `VARCHAR(50)`, and add new data larger than 10 characters to it, a revert attempt may fail. This is to protect your data. You may have written data to the `VARCHAR(50)` field in that time that will not fit in the smaller 10 character space. If no data is added between deployment and revert, the revert process can proceed.
2. Some examples of other similar scenarios where revert won't be possible (again, only if larger sized data is added between deployment and revert) are:
   - `INT` to `BIGINT`
   - `NOT NULL` to `NULL`
   - `TIMESTAMP` to `TIMESTAMP(6)`
   - `utf8` to `utf8mb4`
   - Any other operation that expands the size of a field
3. If you deploy a schema change that removes a unique key or relaxes a unique constraint, and in the time between deployment and attempting to revert, you insert rows that would otherwise conflict with that constraint, the revert may fail.
4. Another uncommon but possible scenario: you deploy a schema change that has a `NOT NULL` column without a `DEFAULT` value, combined with an `ALTER TABLE DROP COLUMN` statement for that column. If you insert some rows between the deployment and the revert attempt, the revert will fail. We will not be able to re-add that column for the newly inserted rows and will not know how to populate it.

For an in-depth look at how this process works, check out our [Behind the scenes: how schema reverts work](/blog/behind-the-scenes-how-schema-reverts-work) blog post.

### Billing considerations

The process of reverting a schema change will not count toward your `read`, `write`, or storage limits. You will not be charged for the background processes we run, like creating a copy of the affected schema and data.

You may see some temporary `_vt` tables in your database. These are used to facilitate the deployment and revert process and do not count toward your storage.

## Gated Deployments

Gated Deployments give you more control over when a migration goes live after the deployment process completes.

As part of our non-blocking schema change process, instead of directly modifying table(s) when you deploy a deploy request, we make a copy of the affected table(s) and apply changes to the copy. We get the data from the original table and the copy table in sync, and once complete, initiate a quick cutover where we swap the tables.

With Gated Deployments, you can initiate the deployment, but once the table syncing is complete, we'll hold off on the cutover and let you click a button to swap the tables and complete the deployment. Gated Deployments can be enabled on each deploy request by unchecking the "Auto-apply changes box" before you deploy.

This feature is helpful if you have long-running migrations. For very large or complex databases, deploying a schema change can take several hours to complete. In those scenarios, you don't want the cutover to happen while you're offline. With Gated Deployments, you can start the deployment process by adding your deploy request to the queue, and once it's done, you'll be able to click a button to merge it in and complete the deployment while you're there to monitor it.

### Enable Gated Deployments

1. When you open a deploy request, uncheck the "**Auto-apply changes**" box.

   ![PlanetScale deploy request - Auto-apply changes checkbox unchecked](/assets/docs/concepts/deploy-requests/gated-deployments.png)

2. Once your deploy requests begins running, you'll also have the option to uncheck the box here.
3. When your deploy request has completed and is ready for cutover, the "**Apply changes**" button will appear. You can now complete the deployment at any time by clicking this button.

![PlanetScale deploy request - Apply changes from deployment button](/assets/docs/concepts/deploy-requests/apply-changes.png)

{% callout %}
If you have an open Gated Deployment, you cannot deploy another deploy request until the current one has been merged in.
{% /callout %}

For more information about this process and why we built it, check out the [Gated Deployments: Addressing the complexity of schema deployments at scale](/blog/gated-deployments-addressing-the-complexity-of-schema-deployments-at-scale) blog post.
