# CSV Table Schema

CSV Table Schema is a pragmatic approach for defining [CSV](https://datatracker.ietf.org/doc/html/rfc4180) schemas. CSV Table Schema provides a CSV schema description via JSON.

Main features:

+ Easy schema definiton via JSON which can be validated against the [CSV Table Meta Schema](https://github.com/openpotato/csv-table-schema/tree/main/schemas/v0.1/schema.json).
+ Dedicated to the CSV format.
+ Supports the following data types: string, enum, enum-set, boolean, integer, numeric, date, time, date-time, json and xml.
+ Supports CSV tables with ordered and unordered columns.
+ Supports CSV tables with or without header row.
+ Supports unique key definitions for CSV tables
+ Supports CSV dictionaries with key/value rows.
+ Supports polimorphic CSV tables with different row types, distinguished by a discriminator in the first column 

## Documentation

The documentation website is part of this repository. We have implemented it using [MkDocs](https://www.mkdocs.org) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material). It is published under https://openpotato.github.io/csv-table-schema/.

## Can I help?

Yes, that would be much appreciated. CSV Table Schema is currently a draft. We are open for any feedback! Use the Issue Tracker for asking and/or submitting a Pull Request.
