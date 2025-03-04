---
title: 'Billing'
subtitle: 'PlanetScale pricing was designed for transparency and scalability.'
date: '2022-08-17'
---

## Overview

PlanetScale offers a scalable, pay-as-you-grow plan model, which means you'll never have to over-provision resources while building your application. We offer three base plans: `Hobby`, `Scaler`, and `Team`, as well as an additional customizable `Enterprise` option.

You can find the complete list of plan offerings [in the next section](#planetscale-plans).

{% callout %}
We used **[gibibytes, otherwise known as binary gigabytes](https://simple.wikipedia.org/wiki/Gibibyte)**, to calculate storage and usage limits. For reference, 1 binary gigabyte is equivalent to 2^30 bytes.
{% /callout %}

### PlanetScale charges on three factors

- **Reads**: Retrieving or inspecting rows during a query or mutation of any kind to your PlanetScale databases.
- **Writes**: Adding new information or changing existing information in your PlanetScale databases.
- **Storage**: Data saved in the form of tables, columns, rows, and their corresponding relationships.

See the ["Understanding rows read" section](#understanding-rows-read) for a more in-depth look at the rows read calculation.

## PlanetScale plans

PlanetScale applies billing plans at the **database level**. You can create several databases under one Organization. The usage charges will be scoped to each database, which you can find all together in the [billing section of your Organization](/docs/concepts/billing#organization-usage-and-billing-page). Some limitations apply to the free tier. See below for more information.

We offer four plan options, summarized below:

|                                                             | **Hobby**      | **Scaler**             | **Team**                        | **Enterprise**                                |
| ----------------------------------------------------------- | -------------- | ---------------------- | ------------------------------- | --------------------------------------------- |
| **Storage/month**                                           | 5 GB           | 10 GB\*                | 100 GB\*                        | Configurable                                  |
| **Row reads/month**                                         | 1 billion      | 100 billion\*          | 500 billion\*                   | Configurable                                  |
| **Row writes/month**                                        | 10 million     | 50 million\*           | 100 million\*                   | Configurable                                  |
| **Production branches**                                     | 1 per database | 2 per database         | 3 per database                  | Configurable                                  |
| **Development branches**                                    | 1 per database | 5 per database         | 10 per database                 | Configurable                                  |
| **Audit log retention**                                     | 5 days         | 15 days                | 60 days                         | _Unlimited_                                   |
| **Concurrent Connections**                                  | 1,000          | 10,000                 | 10,000                          | _Unlimited_                                   |
| **Automated Backups**                                       | Once daily     | Every 12 hours         | Every 12 hours                  | Configurable                                  |
| **Query Insights retention**                                | 24 hours       | 7 days                 | 7 days                          | Configurable                                  |
| **SSO**                                                     | Not included   | Available as an add-on | Included                        | Included                                      |
| **Support**                                                 | Community      | Standard               | Standard, upgrade available\*\* | Business, upgrade available\*\*\*             |
| [**Deployment options**](/docs/concepts/deployment-options) | Cloud          | Cloud                  | Cloud                           | Cloud, Single-tenant Cloud, and Managed Cloud |
| **Monthly fee**                                             | $0             | $29 per database       | $599 per database               | Custom                                        |

\* For the Scaler and Team plans, any extra storage over the included amount is billed at $2.50 per additional 1 GB, extra rows read over the included amount are billed at $1 per additional 1 billion, and extra rows written over the included amount are billed at $1.50 per additional 1 million.

\*\* Business support is available on the Team plan for an additional fee.

\*\*\* Enterprise support is available on the Enterprise plan for an additional fee.

{% callout %}
You are limited to **one free database per organization**. Free databases may be
[slept](/docs/concepts/database-sleeping) after a 7-day period of inactivity.
{% /callout %}

Scaler and Team plans are billed on a **monthly basis**.

For more information about [our `Enterprise` plan](/enterprise), please [reach out to us](/contact). [On our pricing page](/pricing), you can find additional information about each plan's offerings, as well as a calculator to help you decide what plan is right for you.

{% callout %}
Plans are priced **per database**. The total charge for a PlanetScale _database_ includes the charge for
usage on **database branches**. In other words, the total `reads`, `writes`, and storage used by each branch of
your database will determine the final monthly price.
{% /callout %}

## Plan add-ons

To make sure our plans work for your unique use case, we also offer options to customize your Scaler and Team plans.

### Development branch packs

You can add additional development branches to Scaler and Team plans in **packs of 5** for an extra $25/mo per pack.

**To add a branch pack to a database:**

1. Select the database you want to add branches to
2. Click on the "**Settings**" tab in the top nav
3. Click "Add-ons" from the side nav
4. Select the number of branch packs you'd like to add
5. Click "Save".

![Billing page branch pack add-ons](/assets/docs/concepts/billing/addons.png)

### User scheduled backups

We run automatic daily backups for every branch for free. On the Scaler and Team plans, we run automated backups every 12 hours. Disk space for backups is not counted against your plan's storage limit.

You can also [schedule additional backups yourself](/docs/concepts/back-up-and-restore#create-manual-backups) as needed. For these additional user-scheduled backups, storage is billed at **$0.023 per GB** per month. Backups include system tables as well as your data and start at around 140MB.

### Single Sign-on (SSO)

SSO is included on our Team and Enterprise plans. You can add SSO for your organization under the Scaler plan for an additional fee. Please [contact us](/contact) to enable SSO.

## Organization usage and billing page

Each organization has its own billing page, from which you can:

- View your current and previous usage per database
- Upgrade a free database to the Scaler or Hobby plan
- Upgrade a Scaler database to the Hobby plan
- Enter/update your credit card information
- Download current and previous invoices

**To find your billing page:**

1. Go to your [PlanetScale dashboard](https://app.planetscale.com)
2. Select the organization whose billing page you want to view
3. Click on "Settings" in the top nav
4. Click on "Usage and billing" in the side nav
5. Click on the "Billing" tab in the top nav

### PlanetScale invoice details

Invoices provide line items for both usage and discounts received. Each line item shows both **metric and database branch level** granularity.

For example, you may have the following line items:

- Rows _read_ for `main` branch
- Rows _read_ for `your-test-branch`
- Rows _read_ for `main` branch read-only region

In addition, storage per branch may have the following line items:

- Storage usage per GB
- Prorated discounts, if the branch existed for a smaller time period than the billing period
- Storage totals for each read-only region

Storage is prorated by a percentage equal to the existence of a branch's hours/billing period in hours.

### Download an invoice

To download an invoice, go to [your billing page](#find-your-database-billing-page) (`Organization > Settings > Usage and billing > Billing`).

You'll see a table of current and previous monthly invoices. You can download an invoice by month by clicking the "**Download**" button. This will send you to a Stripe invoice page, where you'll have the option to download the complete invoice in PDF format, see invoice details, or download your receipt.

To see more details about your billing from the PlanetScale dashboard, click the "**View details**" button on the Billing page next to the month you want to view. This will show you an overview of the charges for all of the databases in your organization.

{% callout %}
PlanetScale generates both current and past invoices. Even for the **free** plan! You can see the cost had you not
been on the Hobby plan.
{% /callout %}

## Understanding rows read

It can be difficult to decipher what counts as a row read. Nobody wants to run a seemingly simple query only to be hit with an unexpected bill. This section will attempt to break down what goes into the rows read calculation and how you can test your queries to _estimate_ the potential rows read before running them.

Rows read is a **measure of the work that the database engine does**, not a measure of the number of rows returned. Every row read from a table during execution adds to the rows read count, regardless of how many rows are returned. For this reason, it's important to optimize and index your queries.

### Caveats

While, in most cases, the work done is a direct reflection of the number of rows read, there are some caveats.

The `SELECT count(*)` query is a special case. The database engine optimizes this query and doesn't read row data itself to return a count, so this doesn't actually increment rows read. In other words, if you run the query `SELECT count(*)` on a table with 10m rows, your rows read will be 0.

### Testing queries for rows read

With our in-dashboard [Insights tool](/docs/concepts/query-insights), you can explore active queries running against your database. The "**Queries during the last 24 hours**" list has a column that shows the total rows read for that particular query. The "rows read" surfaced here is the same number we use to calculate your total rows read for your billing calculation. In addition, you can click on a particular query to see more information about its performance.

![PlanetScale Insights recent queries list](/assets/docs/concepts/query-insights/queries.png)

If you'd prefer to test a query on your own, you can calculate the **approximate** rows read using the `EXPLAIN` statement. Running `EXPLAIN` with your query will return information about the query execution plan.

For example, if you want to estimate how many rows will be read from a `SELECT`, you could run:

```sql
EXPLAIN SELECT * from posts;
```

This table has 15 rows. All of them are read, which is reflected in the `rows` output returned:

```
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table   | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | posts   | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   15 |   100.00 | NULL  |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
```

It's important to remember the rows read returned with `EXPLAIN` will not always be the exact number of actual rows read, as it is **just an estimation**. You should not rely on this as an accurate way to determine billing ahead of time but rather as a starting point to see the potential impact of queries. It can be a great starting point for optimization.

To see the exact rows read, you will need to run the query. You can use the `EXPLAIN ANALYZE` statement to do this. It will return the estimated information about how it will run the query, run the query, and then return the actual impact of running the query.

```sql
EXPLAIN ANALYZE select * from posts
```

In this case, the estimated count shown in the first set of parentheses does match the actual count. But again, this will not always be the case.

```
+-------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                   |
+-------------------------------------------------------------------------------------------+
| -> Table scan on posts  (cost=1.75 rows=15) (actual time=0.024..0.038 rows=15 loops=1)    |
|                                                                                           |
+-------------------------------------------------------------------------------------------+
```

### Checking rows read

Another useful way to check rows read is using `innodb_rows_read`. This server status variable will show you the number of rows read across all tables. You can run it before and after queries to calculate how many rows were read. Keep in mind, if you have an active running database, this number may not be an accurate reflection of single query impact, as you may have other queries running in the background that affect the rows read.

```sql
SHOW GLOBAL STATUS LIKE 'Innodb_rows_read';
```

This returned output indicates that 4586 total rows have been read.

```
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| Innodb_rows_read | 4586  |
+------------------+-------+
```

{% callout title="Next steps" %}

- [Branching](/docs/concepts/branching)

{% /callout %}
