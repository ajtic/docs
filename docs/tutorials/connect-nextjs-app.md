---
title: 'Connect a Next.js application to PlanetScale'
subtitle: 'Spin up a PlanetScale MySQL serverless database in seconds and connect to a Next.js application'
date: '2023-04-05'
---

In this tutorial, you'll create a [Next.js](https://nextjs.org/) application that uses [Tailwind CSS](https://tailwindcss.com/) for styling and [Prisma](https://www.prisma.io/) to connect to a [PlanetScale](/) database.

## Prerequisites

- [Node.js](https://nodejs.org/en/download/)
- A [free PlanetScale account](https://auth.planetscale.com/sign-up)

## Set up the database

If this is your first time in the dashboard, you'll be prompted to go through a database creation walkthrough where you'll create a new database. Otherwise, click "**New database**" > "**Create new database**".

- **Name** &mdash; You can use any name with lowercase, alphanumeric characters, or underscores. We also permit dashes, but don't recommend them, as they may need to be escaped in some instances.
- **Region** &mdash; Choose the [region](/docs/concepts/regions#available-regions) closest to you or your application. It's important to note if you intend to make this branch a production branch, you will not be able to change the region later, so choose the region with this in mind.

Finally, click "**Create database**".

![Create a new database modal](/assets/docs/tutorials/connect-nextjs-app/database.png)

{% callout %}
If you have an existing cloud-hosted database, you can also choose the "Import" option to import your database to PlanetScale using our Import tool. If you go this route, we recommend using our [Database Imports documentation](/docs/imports/database-imports).
{% /callout %}

A [development branch](/docs/concepts/branching), `main`, is automatically created when you create your database. You can use this branch to develop on, and once you're happy with any schema changes, you can promote it to production, where it becomes a highly available, protected database that you can connect your production application to.

## Set up the starter Next.js app

Now that you have your database, clone the [Next.js starter repository](https://github.com/planetscale/nextjs-starter), or grab your own project.

```sh
git clone https://github.com/planetscale/nextjs-starter
```

Install the dependencies with:

```sh
cd nextjs-starter
npm install
```

Create your `.env` file by renaming the `.env.example` file to `.env`:

```sh
mv .env.example .env
```

## Generate a connection string

Next, you need to generate a database username and password so that you can use it to connect to your application.

In your PlanetScale dashboard, select your database, click "**Connect**", and select "**Prisma**" from the "**Connect with**" dropdown.

As long as you're an organization administrator, this will generate a username and password that has administrator privileges to the database.

Copy the `DATABASE_URL` string from the `.env` tab and paste it into your own `.env` file. The structure will look like this:

```sh
DATABASE_URL='mysql://<USERNAME>:<PASSWORD>@<HOST>/<DATABASE_NAME>?sslaccept=strict'
```

Your PlanetScale database should now be connected to your application.

## Add the schema and data

With the database connected, you're now ready to add a schema, and read/write data.

The sample repo we shared above includes a pre-built schema and some seed data built with Prisma. Push the database schema to your PlanetScale database with:

```sh
npx prisma db push
```

Run the seed script. This will populate your database with `Product` and `Category` data:

```sh
npm run seed
```

## Run the app

Run the app with following command:

```sh
npm run dev
```

Open your browser at [localhost:3000](http://localhost:3000) to see the running application.

## Deploy your app

After you have your application running locally, you may want to deploy it to production. To do so, promote your database branch (`main` by default) to be the production branch ([read the branching documentation for more information](/docs/concepts/branching)).

You can do this in the PlanetScale dashboard by clicking the "**Branches**" tab, and then clicking the "**Promote a branch to production**" button either in the banner or under the "**...**".

![Promote a branch to production](/assets/docs/tutorials/connect-nextjs-app/production.png)

You should also enable [safe migrations](/docs/concepts/safe-migrations), which protects your production branch from accidental schema changes. This can be done from the PlanetScale dashboard by clicking the **"cog"** in the upper right of the production branch UI card.

![Production branch UI card](/assets/docs/tutorials/connect-nextjs-app/production-branch-card-with-sm-disabled.png)

In the modal, toggle the option labelled **"Enable safe migrations"** then click the **"Enable safe migrations"**. This will close the modal and save the setting.

![Enable safe migrations](/assets/docs/tutorials/connect-nextjs-app/prod-branch-options-modal.png)

### Deploy to Vercel

If you'd like to deploy to Vercel, check out our [Deploy to Vercel documentation](/docs/tutorials/deploy-to-vercel).

### Deploy to Netlify

If you'd like to deploy to Netlify, check out our [Deploy to Netlify documentation](/docs/tutorials/deploy-to-netlify).

{% callout %}
If you are deploying the `nextjs-starter` repo, the `Netlify.toml` file in this repository includes the configuration for you to customize the `PLANETSCALE_PRISMA_DATABASE_URL` property on the initial deploy.
{% /callout %}

## What's next?

Once you're done with development, you can [promote your `main` branch to production](/docs/concepts/branching#promote-a-branch-to-production) to get a highly available branch with an additional replica. You should also enable [safe migrations](/docs/concepts/safe-migrations), which protects the branch from accidental schema changes.

To learn more about PlanetScale, take a look at the following resources:

- [PlanetScale workflow](/docs/concepts/planetscale-workflow) &mdash; Quick overview of the PlanetScale workflow: branching, non-blocking schema changes, deploy requests, and reverting a schema change.
- [PlanetScale branching](/docs/concepts/branching) &mdash; Learn how to utilize branching to ship schema changes with no locking or downtime.
- [PlanetScale CLI](/docs/reference/planetscale-cli) &mdash; Power up your workflow with the PlanetScale CLI. Every single action you just performed in this quickstart (and much more) can also be done with the CLI.
