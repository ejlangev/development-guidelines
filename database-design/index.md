# Database design

A few tips and conventions for relational database design specifically around Postgres.

## Conventions

### Table design

* Avoid nullable columns where possible, always mark a column that is required as non null.
* Include a `created_at` column that defaults to `NOW()` on each table
* Include an `updated_at` column on any table where you intend to ever modify a row after inserting it.  This should also default to `NOW()`.
* Avoid tables that store small, fixed data in favor of storing that data in a file within the codebase.

### Column types

* Avoid json/jsonb columns in favor of relational data where possible.  Relational databases are designed for relational data.
* Using json/jsonb columns is okay when the data structure is not under your control or is naturally a map.  Examples of this include storing raw messages from webhooks or recording what A/B test variants a user is part of.
* Use a nullable timestamp rather than a boolean column in a case where the default value is false and it can only change once to true.  This stores two pieces of information in one column and it might be useful to know when the value changed especially in tables where you update rows multiple times.  An example is instead of having an `is_deleted` boolean column use a `deleted_at` timestamp column.

### Constraints and indexes

* Make the database to enforce constraints wherever it can, it is much more likely to do it properly than your application.
* Always use a foreign key to reference another table where possible.  Make sure to set the right behavior for cascading deletion or setting null in the case of deleting a row.
* Avoid making indexes that don't have a clear purpose, they take up space and slow down inserts.
* Use check constraints where the database can help you to enforce invariants.

## Deletions and ledgers

* Generally prefer soft deletes (i.e. a `deleted_at` column) to hard
  deletes that remove the row. In cases where you are storing user data
  try to delete it where you can in compliance with your terms of
  service.
* In cases where you need to overwrite records wholesale prefer an append
  only data store.

## Naming

This section is largely inspired by the [this](https://launchbylunch.com/posts/2014/Feb/16/sql-naming-conventions/) blog post.

### General rules

* Use all lowercase names with underscores connecting
  words. e.g. `blog_post_count` rather than `blogpostcount` or
  `BlogPostCount`.
* Avoid using abbreviations in names unless the
  abbreviation is more commonly used than the actual word.  e.g.
  `post_count` rather than `post_cnt`.
* Avoid words that are reserved for SQL like `join` or `lock` or
  `table`.
* Avoid names that repeat the name of the datatype in favor of ones that
  convey a specific meaning.  e.g. `start_time` or `created_at` rather than `timestamp`.

### Tables and Columns

* Tables should always have singular names e.g. `post` rather than
  `posts`.  This avoids confusion with pluralization or uncountable
  words e.g. `person` -> `people`.
* Tables and columns should not be prefixed or suffixed in any way. Most databases provide
  other ways to group things like Postgres schemas which should be
  leveraged instead and suffixing adds complexity without much value and
  make changing datatypes more difficult.
* Single column primary key fields should be named `id` without any
  prefix.
* Single column foreign keys should be named `(other_table)_id` and
  reference `(other_table).id`.


### Indexes and Constraints

* Always give indexes and constraints human readable names.  Some ORMs
  generate random names but those should be changed to aid in debugging.
* Use the following templates for consistency:
  - Indexes: `ix_(table_name)_(column_names.join('_'))`
  - Foreign keys; `fk_(table_name)_(referenced_table_name)`
  - Check constraints: `ck_(table_name)_(column)_(validation)
