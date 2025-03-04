---
title: 'Connect a Django application to PlanetScale'
subtitle: 'Spin up a PlanetScale MySQL serverless database in seconds and connect to a Django application'
date: '2023-04-05'
---

## Introduction

In this tutorial, you'll learn how to connect a Django application to a PlanetScale MySQL database using a pre-built Django application.

{% callout type="tip" %}
Already have a Django application and just want to connect to PlanetScale? Check out the [Django quick connect repo](https://github.com/planetscale/connection-examples/tree/main/python).
{% /callout %}

## Prerequisites

- Python &mdash; This tutorial uses `v3.6`
- A [free PlanetScale account](https://auth.planetscale.com/sign-up)
- (Optional) [PlanetScale CLI](https://github.com/planetscale/cli) &mdash; This isn't required, but it can make setup much faster

## Set up the Django application

This guide integrates a simple Django application with PlanetScale. The application has one endpoint that displays a list of products and categories pulled from the database. If you have an existing application, you can also use that.

1. Clone the starter Django application and switch into the project folder:

   ```bash
   git clone https://github.com/planetscale/django-example.git
   cd django-example
   ```

2. Start the virtual environment:

   ```bash
   python3 -m venv env
   source env/bin/activate
   ```

   For Windows, use `env/Scripts/activate`.

3. Install the required packages:

   ```bash
   pip install -r ./requirements.txt
   ```

## Set up the database

Next, you need to set up your PlanetScale database and connect to it in the Django application.

You can create a database either in the [PlanetScale dashboard](https://app.planetscale.com) or from the PlanetScale CLI. This guide will use the CLI, but you can follow the database setup instructions in the [PlanetScale quickstart guide](/docs/tutorials/planetscale-quick-start-guide#getting-started-planetscale-dashboard) if you prefer the dashboard.

Authenticate the CLI with the following command:

```bash
pscale auth login
```

Create a new database with a default `main` branch with the following command:

```bash
pscale database create <DATABASE_NAME> --region <REGION_SLUG>
```

This tutorial uses `django_example` for `DATABASE_NAME`, but you can use any name with lowercase, alphanumeric characters, or underscores. You can also use dashes, but we don't recommend them, as they may need to be escaped in some instances.

For `REGION_SLUG`, choose a region closest to you from the [available regions](/docs/concepts/regions#available-regions) or leave it blank.

That's it! Your database is ready to use. Next, let's connect it to the Django application and then add some data.

## Connect to the Django application

There are **two ways to connect** your Django application to PlanetScale:

- With an auto-generated username and password
- Using the PlanetScale proxy with the CLI

Both options are covered below.

### Option 1: Connect with username and password (Recommended)

1. Create a username and password with the PlanetScale CLI by running:

   ```bash
   pscale password create <DATABASE_NAME> <BRANCH_NAME> <PASSWORD_NAME>
   ```

   {% callout %}
   The `PASSWORD_NAME` value represents the name of the username and password being generated. You can have multiple credentials for a branch, so this gives you a way to categorize them. To manage your passwords in the dashboard, go to your database overview page, click "Settings", and then click "Passwords".
   {% /callout %}

   You can also get these exact values to copy/paste from your [PlanetScale dashboard](https://app.planetscale.com). In the dashboard, click on the database > "**Connect**" > "**Connect with**" language dropdown > "**Django**". If the password is blurred, click "**New password**".

   Take note of the values returned to you, as you won't be able to see this password again.

2. Open the .env file in your Django app, find the database connection section, and fill it in as follows:

   ```bash
   DB_HOST=<ACCESS HOST URL>
   DB_PORT=3306
   DB_NAME=<DATABASE_NAME>
   DB_USER=<USERNAME>
   DB_PASSWORD=<PLAIN TEXT>
   MYSQL_ATTR_SSL_CA=/etc/ssl/cert.pem
   ```

   The value for `MYSQL_ATTR_SSL_CA` may differ [depending on your operating system](/docs/concepts/secure-connections#ca-root-configuration).

3. Next, in the `mysite/settings.py` file, scroll down and look for the `DATABASES` object. Replace it with the following:

   ```python
   DATABASES = {
     'default': {
       'ENGINE': 'django_psdb_engine',
       'NAME': os.environ.get('DB_NAME'),
       'HOST': os.environ.get('DB_HOST'),
       'PORT': os.environ.get('DB_PORT'),
       'USER': os.environ.get('DB_USER'),
       'PASSWORD': os.environ.get('DB_PASSWORD'),
       'OPTIONS': {'ssl': {'ca': os.environ.get('MYSQL_ATTR_SSL_CA')}}
     }
   }
   ```

### Option 2: Connect with PlanetScale proxy

To connect with the PlanetScale proxy, you'll need the [PlanetScale CLI](https://github.com/planetscale/cli).

1. Open a connection by running the following:

   ```bash
   pscale connect <DATABASE_NAME> <BRANCH_NAME>
   ```

   If you're following this guide exactly and haven't created any new branches, you'll use the default branch, `main`, for `BRANCH_NAME`.

2. A secure connection to your database will be established and you'll see a local address you can use to connect to your application.

3. Open the `.env` file in your Django app and update it as follows:

   ```bash
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_NAME=<DATABASE_NAME>
   ```

   The connection uses port `3306` by default, but if that's being used, it will pick a random port. Make sure you paste in whatever port is returned in the terminal.

## Optional &mdash; Bring in PlanetScale custom database wrapper

This next step is only necessary if you're using your own application to go through this guide. If you cloned the sample app, this already exists in the repo.

Because PlanetScale doesn't support foreign key constraints, you need to pull in the PlanetScale database wrapper for Django to disable foreign key syntax in the Django migrations.

1. Run the following to pull it in:

   ```bash
   git clone https://github.com/planetscale/django_psdb_engine.git
   ```

2. In your `settings.py` file, add `django_psdb_engine` as the database engine.

   ```python
   DATABASES = {
       'default': {
           'ENGINE': 'django_psdb_engine',
       }
   }
   ```

## Run migrations and seeder

Now that you're connected, let's add some data to see everything in action.

You can find the migrations file in `mysite/store/migrations/0002_auto_20220919_0058.py` that references the `Category` and `Product` models to create the schema. It also contains seed data to create two products and two categories. Run this migration with:

```bash
python manage.py migrate
```

This will also run the default Django migrations.

## Display the data

Finally, let's display the data to confirm that everything worked correctly.

This Django starter application has a pre-built endpoint, `/products`, that will grab and display all of the product data.

To view the data, start the server:

```bash
python manage.py runserver
```

Then go to [`localhost:8000/products`](http://localhost:8000/products) and you'll see a list of the data from the products table.

## Add data manually

If you want to continue playing around with adding data on the fly, you have a few options:

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

Add a record to the `store_product` table:

```sql
INSERT INTO `store_product` (name, description, image, category_id)
VALUES  ('Product 3', 'Product 3 description', 'https://via.placeholder.com/300.png?text=Product1', 1);
```

The value `id` will be filled with a default value.

Type `exit` to exit the shell.

Refresh the Django homepage to see the new record. You can also verify it was added in the PlanetScale CLI MySQL shell with:

```sql
select * from store_product;
```

### Add data with PlanetScale dashboard console

If you don't care to install MySQL client or the PlanetScale CLI, another quick option using the MySQL console built into the PlanetScale dashboard.

1. Go to your [PlanetScale dashboard](https://app.planetscale.com) and select your Django database.
2. Click on the "**Branches** and select the `main` branch.
3. Click on "**Console**"
4. Add a new record to the `store_product` table with:

   ```sql
   INSERT INTO `store_product` (name, description, image, category_id)
   VALUES  ('Product 3', 'Product 3 description', 'https://via.placeholder.com/300.png?text=Product1', 1);
   ```

5. You can confirm that it was added by running:

   ```sql
   select * from store_product;
   ```

You can also head to the [`/products`](http://localhost:8000/products) endpoint in your Django application to see the new data.

## Disabling foreign key constraints in Django

PlanetScale [does not support foreign key constraints](/docs/learn/operating-without-foreign-key-constraints). You can disable foreign key constraint checks at the model level in Django, but if you're running the default migrations, you'll need to turn them off globally.

The [PlanetScale custom database backend](https://github.com/planetscale/django_psdb_engine) manages this, but if you want to do it manually in each model. For example, in the `models.py` file for the example in this document, we define the foreign key on the `category` table with the following:

```python
category = models.ForeignKey(Category, on_delete=models.DO_NOTHING, db_constraint=False)
```

This isn't necessary to do in every model if you're pulling in the `django_psdb_engine` because it overrides the setting globally anyway, but this will work if you want to do it per model.

## What's next?

Once you're done with development, you can [promote your `main` branch to production](/docs/concepts/branching#promote-a-branch-to-production) and enable [safe migrations](/docs/concepts/safe-migrations) to get a highly available branch protected by direct schema changes.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](/docs/concepts/nonblocking-schema-changes) to your database tables without locking or causing downtime for production databases.
