# Database design

A few tips and conventions for relational database design specifically around Postgres.

## Conventions

### Table design

* Avoid nullable columns where possible, always mark a column that is required as non null.
* Include a `created_at` column that defaults to `NOW()` on each
* Include an `updated_at` column on any table where you intend to ever modify a row after inserting it.  This should also default to `NOW()`.

### Constraints and indexes

* Make the database to enforce constraints wherever it can, it is much more likely to do it properly than your application.
* Always use a foreign key to reference another table where possible.  Make sure to set the right behavior for cascading deletion or setting null in the case of deleting a row.
* Avoid making indexes that don't have a clear purpose, they take up space and slow down inserts.
* Use check constraints where the database can help you to enforce invariants.

### Column types

* Avoid json/jsonb columns in favor of relational data where possible.  Relational databases are designed for relational data.
* Using json/jsonb columns is okay when the data structure is not under your control or is naturally a map.  Examples of this include storing raw messages from webhooks or recording what A/B test variants a user is part of.
* Use a nullable timestamp rather than a boolean column in a case where the default value is false and it can only change once to true.  This stores two pieces of information in one column and it might be useful to know when the value changed especially in tables where you update rows multiple times.

## Updates vs ledgers
