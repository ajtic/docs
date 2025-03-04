---
title: 'PlanetScale CLI commands - branch'
subtitle: 'Use the PlanetScale CLI to create development branches, open deploy requests, and make non-blocking schema changes directly from your terminal.'
date: '2023-04-05'
meta:
  title: 'CLI reference - branch'
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](/docs/concepts/planetscale-environment-setup). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The `branch` command

This command allows you to create, delete, diff, and manage [branches](/docs/concepts/branching).

**Usage:**

```bash
pscale branch <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command**                                         | **Sub-command flags**                                                                                            | **Description**                                                          |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `create <DATABASE_NAME> <BRANCH_NAME>`                  | `--from <SOURCE_BRANCH>`, `--region <BRANCH_REGION>`, `--restore <BACKUP_NAME>`, `--seed-data` `--web`, `--wait` | Create a new branch on the specified database                            |
| `delete <DATABASE_NAME> <BRANCH_NAME>`                  | `--force`                                                                                                        | Delete the specified branch from the a database                          |
| `diff <DATABASE_NAME> <BRANCH_NAME>`                    | `--web`                                                                                                          | Show the diff of the specified branch against the parent branch.         |
| `keyspaces <DATABASE_NAME> <BRANCH_NAME>`               |                                                                                                                  | Show information for sharded keyspaces.                                  |
| `list <DATABASE_NAME>`                                  | `--web`                                                                                                          | List all branches of a database                                          |
| `promote <DATABASE_NAME> <BRANCH_NAME>`                 |                                                                                                                  | Promote a database branch to production                                  |
| `refresh-schema <DATABASE_NAME> <BRANCH_NAME>`          |                                                                                                                  | Refresh the schema for a database branch                                 |
| `safe-migrations enable <DATABASE_NAME> <BRANCH_NAME>`  |                                                                                                                  | Enables safe migrations for a database branch                            |
| `safe-migrations disable <DATABASE_NAME> <BRANCH_NAME>` |                                                                                                                  | Disables safe migrations for a database branch                           |
| `schema <DATABASE_NAME> <BRANCH_NAME>`                  | `--web`                                                                                                          | Show the schema of a branch                                              |
| `show <DATABASE_NAME> <BRANCH_NAME>`                    | `--web`                                                                                                          | Show a specific backup of a branch                                       |
| `switch <BRANCH_NAME> --database <DATABASE_NAME>`       | `--database <DATABASE_NAME>`\*, `--create`, `parent-branch <BRANCH_NAME>`                                        | Switch to the specified branch                                           |
| `vschema <DATABASE_NAME> <BRANCH_NAME>`                 |                                                                                                                  | Show the vschema for a sharded keyspace. Empty on non-sharded keyspaces. |

> \* _Flag is required_

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag**            | **Description**                                                                                      | **Applicable sub-commands**                |
| ------------------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `--from <SOURCE_BRANCH>`        | Parent branch that you want to create a new branch off of                                            | `create`                                   |
| `--region <BRANCH_REGION>`      | Region where database should be created                                                              | `create`                                   |
| `--restore <BACKUP_NAME>`       | Create a new branch from a specified backup                                                          | `create`                                   |
| `--seed-data`                   | Create a new branch and seed data using the [Data Branching® feature](/docs/concepts/data-branching) | `create`                                   |
| `--web`                         | Perform the action in your web browser                                                               | `create`, `diff`, `list`, `schema`, `show` |
| `--wait`                        | Wait until the branch is ready                                                                       | `create`                                   |
| `--database <DATABASE_NAME>`    | Specify the database name                                                                            | `switch`                                   |
| `--create`                      | Create a new branch if it does not exist                                                             | `switch`                                   |
| `--parent-branch <BRANCH_NAME>` | If a new branch is being created, use this to specify a parent branch. Default is `main`.            | `switch`                                   |

### Available flags

| **Flag**                    | **Description**                       |
| --------------------------- | ------------------------------------- |
| `-h`, `--help`              | View help for auth command            |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

### Global flags

| **Command**                     | **Description**                                                                      |
| ------------------------------- | ------------------------------------------------------------------------------------ |
| `--api-token <TOKEN>`           | The API token to use for authenticating against the PlanetScale API.                 |
| `--api-url <URL>`               | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`.     |
| `--config <CONFIG_FILE>`        | Config file. Default is `$HOME/.config/planetscale/pscale.yml`.                      |
| `--debug`                       | Enable debug mode.                                                                   |
| `-f`, `--format <FORMAT>`       | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color`                    | Disable color output.                                                                |
| `--service-token <TOKEN>`       | The service token for authenticating.                                                |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating.                                             |

## Examples

### The `list` sub-command with `--web` flag

**Command:**

```bash
pscale branch list <DATABASE_NAME> --web
```

**Output:**

Opens the Branches page, `<https://app.planetscale.com/org/database/branches>`, in browser.

### The `diff` sub-command

**Command:**

```bash
pscale branch diff <DATABASE_NAME> <BRANCH_NAME>
```

**Output:**

```
-- users --
+CREATE TABLE `users` (
+  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
+  `name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
+  PRIMARY KEY (`id`)
+) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

This will return the diff against the parent branch.
