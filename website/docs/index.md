CSV Table Schema is a pragmatic approach for defining [CSV](https://datatracker.ietf.org/doc/html/rfc4180) schemas. CSV Table Schema provides a CSV schema description via JSON.

Main features:

+ Easy schema definiton via JSON which can be validated against the [CSV Table Meta Schema](https://github.com/openpotato/csv-table-schema/blob/main/spec/csv-table-schema.json).
+ Dedicated to the CSV format.
+ Supports the following data types: string, enum, enum-set, bool, int, float, date, time, date-time, json and xml.
+ Supports CSV tables with ordered and unordered columns.
+ Supports CSV tables with or without header row.
+ Supports CSV dictionaries with key/value rows.

## Examples

The following examples are available:

* [Sample 01a: List of Students](https://github.com/openpotato/csv-table-schema/blob/main/samples/sample01a.csvts.json)
* [Sample 01b: List of Courses](https://github.com/openpotato/csv-table-schema/blob/main/samples/sample01b.csvts.json)
* [Sample 01c: List of Subjects](https://github.com/openpotato/csv-table-schema/blob/main/samples/sample01b.csvts.json)
* [Sample 02: Configuration](https://github.com/openpotato/csv-table-schema/blob/main/samples/sample02.csvts.json)

## Why yet another CSV schema definition?

Unlike JSON or XML there is no well established schema definition language for CSV. But we are of course not the first ones with a proposal:

+ The [CSV Schema Language](https://digital-preservation.github.io/csv-schema/csv-schema-1.2.html) is a language for defining and validating CSV data. It uses its [own domain language](http://digital-preservation.github.io/csv-schema/csv-schema-1.2.html#ebnf) for describing the schema. 
+ The [Table Schema](https://specs.frictionlessdata.io/table-schema/) is a format to declare a schema for tabular data (CSV is just one example). The schema is designed to be expressible in JSON.

Both approaches are well thought but sadly didn't suit our needs.

CSV Table Schema is an alternative schema description which is heavily inspired by both existing schema languages.

## What's missing?

A data schema has two purposes:

+ Semantical documentation of the data structure
+ The possibility to validate real data against the data schema.

The documentation part is done. What's still missing is a reference validator.
