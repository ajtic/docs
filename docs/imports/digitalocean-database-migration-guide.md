---
title: DigitalOcean database migration guide
subtitle: Learn how to migrate a database from a DigitalOcean MySQL cluster into PlanetScale using the database Import tool.
date: '2022-08-01'
---

## Introduction

In this article, we’ll walk through migrating a MySQL database from DigitalOcean to PlanetScale using the [Import tool](/docs/imports/database-imports).

{% callout %}
This guide assumes you are using MySQL on DigitalOcean. Other database systems available through DigitalOcean will not
work with the PlanetScale import tool.
{% /callout %}

## Prerequisites

Before you can start migrating your database, you’ll also need to collect the following information from your DigitalOcean cluster:

- The admin username and password
- The database host
- The port
- Database name

Most of this information is located on the landing page of your cluster.

![The Overview of the database cluster.](/assets/docs/imports/digitalocean-database-migration-guide/the-overview-of-the-database-cluster.png)

You can view the list of databases in the "**Users & Databases**" tab. This article will use the default database created when the cluster was initialized, named `defaultdb`.

![The Users & Databases view of the cluster with the Databases section highlighted.](/assets/docs/imports/digitalocean-database-migration-guide/the-users-and-databases-view-of-the-cluster-with-the-databases-section-highlighted.png)

{% callout %}
If you don’t know the admin password, you can create a new set of credentials using the information on the [Import
tool user permissions page](/docs/imports/import-tool-user-requirements) to create an account that can be used to
import your database.
{% /callout %}

## Update trusted sources

In order for PlanetScale to connect to your DigitalOcean database, you must allow network traffic into the database by adding the necessary IP addresses to the trusted sources list in DigitalOcean. The specific IP addresses you will need to allow depend on the region you plan to host your PlanetScale database. Check the [Import tool public IP addresses page](/docs/imports/import-tool-migration-addresses) to determine the IP addresses to allow before continuing. This guide will use the **AWS us-east-1 (North Virginia)** region so we’ll allow the following addresses:

```
3.209.149.66
3.215.97.46
34.193.111.15
23.23.187.137
52.6.141.108
52.70.2.89
50.17.188.76
52.2.251.189
52.72.234.74
35.174.68.24
52.5.253.172
54.156.81.4
34.200.24.255
35.174.79.154
44.199.177.24
```

In the DigitalOcean dashboard, navigate to the “**Settings”** tab of your database and locate **Trusted sources** in the list of configuration items. Click “**Edit”** and the row should change to allow edits to the setting.

![The Settings tab of the database cluster in Digital Ocean.](/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digital-ocean.png)

When you enter an IP address from the list, a message will appear below the input box asking if you want to add that IP as an address. Click that message to add it to the list. Repeat this step for each IP address that needs to be added, then click “**Save**” once you are done.

![The Trusted sources section of the Settings tab.](/assets/docs/imports/digitalocean-database-migration-guide/the-trusted-sources-section-of-the-settings-tab.png)

### Disable ANSI_QUOTES setting

The default settings for MySQL databases on DigitalOcean is to have `ANSI_QUOTES` enabled in the global MySQL settings, which is not supported by PlanetScale. To remove this setting, navigate to the "**Settings**" tab of your cluster and locate the section titled **Global SQL mode**. Click “**Edit**” in that section to change the configuration settings.

![The Settings tab of the database cluster in DigitalOcean with the Global SQL mode section highlighted.](/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digitalocean-with-the-global-sql-mode-section-highlighted.png)

To remove the `ANSI_QUOTES` setting, click the “**x**” next to the tag and click “**Save**.” The change should apply immediately.

![An example of removing the ANSI_QUOTES setting from the Global SQL mode settings.](/assets/docs/imports/digitalocean-database-migration-guide/an-example-of-removing-the-ansi_quotes-setting-from-the-global-sql-mode-settings.png)

## Importing your database

In the PlanetScale dashboard, click “**New database”**, then “**Import database”**.

![The default view of all databases in the PlanetScale organization.](/assets/docs/imports/digitalocean-database-migration-guide/the-default-view-of-all-databases-in-the-planetscale-organization.png)

The Import database feature is in beta at the time of this writing. If this is your first time accessing this feature, you will be prompted to opt into using the feature. Click “**Join beta”** to proceed.

![The Join beta view.](/assets/docs/imports/digitalocean-database-migration-guide/the-join-beta-view.png)

Complete the form using the information gathered in the previous section. Click “**Connect to database”,** and the import tool will attempt to connect to your DigitalOcean database.

![The Import external database form.](/assets/docs/imports/digitalocean-database-migration-guide/the-import-external-database-form.png)

The “**Connect to database**” button will update with the status of the connection. If the connection is successful, click “**Begin database import**” to migrate your data to PlanetScale.

![The notice shown when PlanetScale has successfully connected to the external database.](/assets/docs/imports/digitalocean-database-migration-guide/the-notice-shown-when-planetscale-has-successfully-connected-to-the-external-database.png)

You’ll then be moved to a page where you can monitor the import status of the database from DigitalOcean.

![The default view of the database in PlanetScale while the import is in progress.](/assets/docs/imports/digitalocean-database-migration-guide/the-default-view-of-the-database-in-planetscale-while-the-import-is-in-progress.png)

Once the initial import has been completed, the first card in the Database import section will update to reflect the status of the import. If it is successful, you’ll have the option to “**Enable primary mode**,” which will prepare the PlanetScale database to be used as the primary data source. Click “**Enable primary mode**” & you’ll be presented with a confirmation modal to proceed.

![The view of the database in PlanetScale once the initial import has completed.](/assets/docs/imports/digitalocean-database-migration-guide/the-view-of-the-database-in-planetscale-once-the-initial-import-has-completed.png)

Once Primary mode is enabled, you can complete the migration by clicking “**Finish import**” and confirming on the modal that will display. This will remove any synchronization occurring between DigitalOcean and PlanetScale.

![The view of the database in PlanetScale once the database is set to primary mode.](/assets/docs/imports/digitalocean-database-migration-guide/the-view-of-the-database-in-planetscale-once-the-database-is-set-to-primary-mode.png)

This concludes the guide to migrating a database from DigitalOcean to PlanetScale.
