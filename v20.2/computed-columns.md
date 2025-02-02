---
title: Use Computed Columns
summary: A computed column stores data generated by an expression included in the column definition.
toc: true
---

A computed column stores data generated from other columns by a [scalar expression](scalar-expressions.html) included in the column definition.


## Why use computed columns?

Computed columns are especially useful when used with [partitioning](partitioning.html), [`JSONB`](jsonb.html) columns, or [secondary indexes](indexes.html).

- **Partitioning** requires that partitions are defined using columns that are a prefix of the [primary key](primary-key.html). In the case of geo-partitioning, some applications will want to collapse the number of possible values in this column, to make certain classes of queries more performant. For example, if a users table has a country and state column, then you can make a stored computed column locality with a reduced domain for use in partitioning. For more information, see the [partitioning example](#create-a-table-with-geo-partitions-and-a-computed-column) below.

- **JSONB** columns are used for storing semi-structured `JSONB` data. When the table's primary information is stored in `JSONB`, it's useful to index a particular field of the `JSONB` document. In particular, computed columns allow for the following use case: a two-column table with a `PRIMARY KEY` column and a `payload` column, whose primary key is computed as some field from the `payload` column. This alleviates the need to manually separate your primary keys from your JSON blobs. For more information, see the [`JSONB` example](#create-a-table-with-a-jsonb-column-and-a-computed-column) below.

- **Secondary indexes** can be created on computed columns, which is especially useful when a table is frequently sorted. See the [secondary indexes example](#create-a-table-with-a-secondary-index-on-a-computed-column) below.

## Considerations

Computed columns:

- Cannot be used to generate other computed columns.
- Cannot be a [foreign key](foreign-key.html) reference.
- Behave like any other column, with the exception that they cannot be written to directly.
- Are mutually exclusive with [`DEFAULT`](default-value.html).

## Creation

To define a computed column, use the following syntax:

~~~
column_name <type> AS (<expr>) STORED
~~~

Parameter | Description
----------|------------
`column_name` | The [name/identifier](keywords-and-identifiers.html#identifiers) of the computed column.
`<type>` | The [data type](data-types.html) of the computed column.
`<expr>` | The pure [scalar expression](scalar-expressions.html) used to compute column values. Any functions marked as `impure`, such as `now()` or `nextval()` cannot be used.
`STORED` | _(Required)_ The computed column is stored alongside other columns.

<span class="version-tag">New in v20.2</span>: For compatibility with PostgresSQL, CockroachDB also supports creating computed columns with the syntax `column_name <type> GENERATED ALWAYS AS (<expr>) STORED`.

## Examples

### Create a table with a computed column

{% include {{ page.version.version }}/computed-columns/simple.md %}

### Create a table with geo-partitions and a computed column

{% include {{ page.version.version }}/computed-columns/partitioning.md %} The `locality` values can then be used for geo-partitioning.

### Create a table with a `JSONB` column and a computed column

{% include {{ page.version.version }}/computed-columns/jsonb.md %}

### Create a table with a secondary index on a computed column

{% include {{ page.version.version }}/computed-columns/secondary-index.md %}

### Add a computed column to an existing table

{% include {{ page.version.version }}/computed-columns/add-computed-column.md %}

For more information, see [`ADD COLUMN`](add-column.html).

### Convert a computed column into a regular column

{% include {{ page.version.version }}/computed-columns/convert-computed-column.md %}

## See also

- [Scalar Expressions](scalar-expressions.html)
- [Information Schema](information-schema.html)
- [`CREATE TABLE`](create-table.html)
- [`JSONB`](jsonb.html)
- [Define Table Partitions ({{ site.data.products.enterprise }})](partitioning.html)
