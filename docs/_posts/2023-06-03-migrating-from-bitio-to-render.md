---
layout: post
title:  Migrating database from Bitio to Render
date:   2023-06-03 06:15:37
categories: database
post_id: 4
---

On the 30th of May, 2023 I got an email from [bit.io](https://bit.io/) (database as a service company) that they will be joining Databricks, a fancy way of saying that they are changing their business and I should take my stuff (databases) somewhere else. Bit.io offers a free database (PostgreSQL) in the cloud for hobbyists and beginners to test things out, and I have been using it to store databases for my test projects. In this post, I will walk you through how I will migrate the database from Bitio to [Render](https://render.com/) (another cloud provider).

## Migration

Bitio provides several options for migrating your database, but the most flexible option for me was using `pg_dump` and `pg_restore`. The following commands are all you need to dump and restore your database:

```bash
pg_dump -F c -d <bitio_pg_string> -f <dump_file_name>
```

where `<bitio_pg_string>` is your bitio postgres connection string and `<dump_file_name>` is whatever name you choose.

To restore the database to a new server, we use the following command:

```bash
pg_restore -d <destination_pg_string> <dump_file_name>
```

where `<destination_pg_string>` is the connection string to string to your new Postgres server, and `<dump_file_name>` is the name of the file you previously dumped using `pg_dump`.

But there was one caveat to using this approach when your Postgres version is less than version 14, the migration will not work because Bitio requires a version greater than or equal to 14. A way around this is to use docker (assuming you have docker installed)  as follows:

```bash
docker run --rm -it postgres:14 bash

```

The above command will give you an interactive terminal into Postgres, you can then proceed to run the commands above. One more thing, to do `pg_restore` on Render, you will have to slightly change the above command:

```bash
pg_restore --no-owner -d <destination_pg_string> <dump_file_name>
```

<br/>
