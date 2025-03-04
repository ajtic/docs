---
title: 'Connect a Symfony application to PlanetScale'
subtitle: 'Spin up a PlanetScale MySQL serverless database in seconds and connect to a Symfony application'
className: 'ignore-img-borders'
date: '2023-04-05'
---

## Introduction

In this tutorial, you'll learn how to connect a Symfony application to a PlanetScale MySQL database using a sample Symfony starter app.

## Prerequisites

- [PHP](https://www.php.net/manual/en/install.php) &mdash; This tutorial uses `v8.1`
- [Composer](https://getcomposer.org/)
- A [free PlanetScale account](https://auth.planetscale.com/sign-up)
- [PlanetScale CLI](https://github.com/planetscale/cli) &mdash; You can also follow this tutorial using just the PlanetScale admin dashboard, but the CLI will make setup quicker.

## Set up the Symfony app

This guide will integrate [a simple Symfony app](https://github.com/planetscale/symfony-example) with PlanetScale that will display a list of products stored in the database. If you have an existing application, you can also use that.

1. Clone the starter Symfony application:

   ```bash
   git clone https://github.com/planetscale/symfony-example.git
   ```

2. Enter into the folder and install the dependencies:

   ```bash
   cd symfony-example
   composer install
   ```

3. Rename the `.env.example` file to `.env.local`:

   ```bash
   mv .env.example .env.local
   ```

   Once you deploy to production, don't forget to update `.env.local` to `.env`.

4. Start the application:

   ```bash
   symfony serve
   ```

You can view the application at [http://localhost:8000](http://localhost:8000).

## Set up the database

Next, you need to set up your PlanetScale database and connect to it in the Symfony application.

You can create a database either in the [PlanetScale dashboard](https://app.planetscale.com) or from the PlanetScale CLI. This guide will use the CLI, but you can follow the database setup instructions in the [PlanetScale quickstart guide](/docs/tutorials/planetscale-quick-start-guide) if you prefer the dashboard.

1. Authenticate the CLI with the following command:

   ```bash
   pscale auth login
   ```

2. Create a new database with a default `main` branch with the following command:

   ```bash
   pscale database create <DATABASE_NAME> --region <REGION_SLUG>
   ```

This tutorial uses `symfony_example` for `DATABASE_NAME`, but you can use any name with lowercase, alphanumeric characters, or underscores. You can also use dashes, but we don't recommend them, as they may need to be escaped in some instances.

For `REGION_SLUG`, choose a region closest to you from the [available regions](/docs/concepts/regions#available-regions) or leave it blank.

That's it! Your database is ready to use. Next, let's connect it to the Symfony application and then add some data.

## Connect to the Symfony app

There are **two ways to connect** to PlanetScale:

- With an auto-generated username and password
- Using the PlanetScale proxy with the CLI

Both options are covered below.

### Option 1: Connect with username and password (Recommended)

These instructions show you how to use the [PlanetScale CLI](/docs/concepts/planetscale-environment-setup) to generate a set of credentials.

You can also get these exact values to to copy/paste from your [PlanetScale dashboard](https://app.planetscale.com). In the dashboard, click on the database > "**Connect**" > "**Generate new password**" > "**General**" dropdown > "**Symfony**". If the password is blurred, click "**New password**". Skip to step 3 once you have these credentials.

1. Create a username and password with the PlanetScale CLI by running:

   ```bash
   pscale password create <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_NAME>
   ```

   A default branch, `main`, is created when you create the database, so you can use that for `BRANCH_NAME`.

   {% callout %}
   The `PASSWORD_NAME` value represents the name of the username and password being generated. You can have multiple
   credentials for a branch, so this gives you a way to categorize them. To manage your passwords in the dashboard, go to
   your database overview page, click "Settings", and then click "Passwords".
   {% /callout %}

   Take note of the values returned to you, as you won't be able to see this password again.

2. Open the `.env.local` file in your Symfony app, find the database connection section, and replace the existing `DATABASE_URL` value with:

   ```bash
   DATABASE_URL="mysql://<USERNAME>:<PASSWORD>@<ACCESS_HOST_URL>:3306/<DATABASE_NAME>?serverVersion=8.0"
   ```

Fill in `USERNAME`, `PASSWORD`, `ACCESS HOST URL` and `DATABASE_NAME` with the appropriate values from the CLI output above.

Refresh your Symfony homepage and you should see the message that you're connected to your database!

### Option 2: Connect with the PlanetScale proxy

To connect with the PlanetScale proxy, you'll need the [PlanetScale CLI](https://github.com/planetscale/cli).

1. Open a connection by running the following:

   ```bash
   pscale connect <DATABASE_NAME> <BRANCH_NAME>
   ```

   If you're following this guide exactly and haven't created any branches, you can use the default branch, `main`.

2. A secure connection to your database will be established and you'll see a local address you can use to connect to your application.

3. Open the `.env.local` file in your Symfony app and update it as follows:

   ```bash
   DB_HOST=127.0.0.1
   DB_PORT=3306 # Get this from the output of the previous step
   DB_NAME=
   DATABASE_URL=mysql://${DB_HOST}:${DB_PORT}/${DB_NAME}?serverVersion=5.7
   ```

   The connection uses port `3306` by default, but if that's being used, it will pick a random port. Make sure you paste in whatever port is returned in the terminal. Fill in the database name as well.

4. Open up `config/packages/doctrine.yaml`. Under `option`, you'll see a line for the SSL certificate that was used to connect with username and password:

   ```php
   !php/const:PDO::MYSQL_ATTR_SSL_CA: /etc/ssl/cert.pem
   ```

Delete that line and save.

Refresh your Symfony homepage and you should see the message that you're connected to your database!

## Run migrations and seeder

Now that you're connected, let's add some data to see it in action. The sample application comes with a migration file at `migrations/Version20220120102247.php` that will create `category` and `product` tables in the database.

There are also two seeder files, `src/DataFixtures/CategoryFixtures.php` and `src/DataFixtures/ProductFixtures.php`, that will add ten random categories and products to the `category` and `product` tables, respectively. Let's run those now.

1. Make sure your database connection has been established. You'll see the message "You are connected to your-database-name" on the [Symfony app homepage](http://localhost:8000/) if everything is configured properly.

2. In your terminal in the root of the Symfony project, run the following to run the migrations:

   ```bash
   symfony console doctrine:migrations:migrate
   ```

   You will get a message asking you to confirm. Type "yes" and hit enter to proceed.

3. Next, seed the database by running:

   ```bash
   symfony console doctrine:fixtures:load
   ```

   This will purge your database and load the placeholder data into it.

4. Refresh your Symfony homepage and you'll see a list of products and their category printed out.

The `templates/product/index.html.twig` file pulls this data from the `product` table with the help of the `src/Controller/ProductController.php` file.

![Symfony PlanetScale starter app homepage](/assets/docs/tutorials/connect-symfony-app/example.png)

## Add data manually

If you want to continue to play around with adding data on the fly, you have a few options:

- PlanetScale CLI shell
- PlanetScale dashboard console
- Your favorite MySQL client (for a list of tested MySQL clients, review our article on [how to connect MySQL GUI applications](/docs/tutorials/connect-mysql-gui))

The first two options are covered below.

### Add data with PlanetScale CLI

You can use the PlanetScale CLI to open a MySQL shell to interact with your database.

You may need to [install the MySQL command line client](/docs/concepts/planetscale-environment-setup) if you haven't already.

Run the following command in your terminal:

```bash
pscale shell <DATABASE_NAME> <BRANCH_NAME>
```

This will open up a MySQL shell connected to the specified database and branch.

{% callout %}
A branch, `main`, was automatically created when you created your database, so you can use that for `BRANCH_NAME`.
{% /callout %}

Add a record to the `product` table:

```sql
INSERT INTO `store_product` (name, description, image, category_id)
VALUES  ('Spaceship', 'Get ready for the trip of a lifetime', 'https://via.placeholder.com/150.png', 2);
```

The value `id` will be filled with a default value.

You can verify it was added in the PlanetScale CLI MySQL shell with:

```sql
select * from product;
```

Type `exit` to exit the shell.

You can now refresh the [Symfony homepage](http://localhost:8000) to see the new record.

### Add data with PlanetScale dashboard console

If you don't care to install MySQL client or the PlanetScale CLI, another quick option using the MySQL console built into the PlanetScale dashboard.

1. Go to your [PlanetScale dashboard](https://app.planetscale.com) and select your Symfony database.
2. Click on the "**Branches** and select the `main` branch.
3. Click on "**Console**"
4. Add a new record to the `product` table with:

   ```sql
   INSERT INTO `store_product` (name, description, image, category_id)
   VALUES  ('Spaceship', 'Get ready for the trip of a lifetime', 'https://via.placeholder.com/150.png', 2);
   ```

5. You can confirm that it was added by running:

   ```sql
   select * from product;
   ```

You can now refresh the [Symfony homepage](http://localhost:8000) to see the new record.

## What's next?

Once you're done with development, you can [promote your `main` branch to production](/docs/concepts/branching#promote-a-branch-to-production) and enable [safe migrations](/docs/concepts/safe-migrations) to get a highly available branch protected by direct schema changes.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](/docs/concepts/nonblocking-schema-changes) to your database tables without locking or causing downtime for production databases.
