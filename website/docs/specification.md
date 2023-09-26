# CSV Table Schema Specification

#### Version 0.0.5

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119 and RFC8174](https://tools.ietf.org/html/bcp14) when, and only when, they appear in all capitals, as shown here.

This document is licensed under [Apache License, Version 2.0](https://opensource.org/license/apache-2-0/).

## Introduction

CSV Table Schema defines a standard for describing [CSV](https://datatracker.ietf.org/doc/html/rfc4180) schemas. CSV Table Schema can be used to document CSV data structures and to validate data against a given documentation.

### CSV

CSV (Comma-Separated Values) is a widely used file format for storing and exchanging structured data in plain text. It is characterized by its simplicity, human-readability, and versatility. In CSV, data is organized in rows and columns, much like a spreadsheet or database table. Each row represents a single record or data entry, and each column contains an attribute or field. Values within a row are separated by a delimiter, most commonly a comma, but other delimiters like semicolons, tabs, or pipes can also be used based on specific requirements.

Example of CSV Data:

``` csv
Name,Age,City
John,30,Berlin
Alice,25,London
Bob,35,Paris
```

In the example above, we have a CSV file with three columns (Name, Age, and City) and three rows of data, each representing an individual.

Variants of CSV:

+ **SSV (Semicolon-Separated Values)**: SSV (Semicolon-Separated Values), is a variant of CSV where semicolons (;) are used as delimiters instead of commas. This format is often used in regions where comma is used as a decimal separator in numeric values, reducing potential parsing conflicts (e.g. Germany). 

    For example:

    ``` csv
    Name;Age,City
    John;30;Berlin
    Alice;25;London
    Bob;35;Paris
    ```

+ **TSV (Tab-Separated Values)**: TSV (Tab-Separated Values) uses tabs (or tab characters) as delimiters between values instead of commas. TSV is commonly employed in situations where data contains commas naturally, such as text descriptions or addresses. TSV files are especially prevalent in data interchange within the scientific and research communities. 

    For example:

    ``` csv
    Name   Age  City
    John   30   Berlin
    Alice  25   London
    Bob    35   Paris
    ```

### CSV Tables

A CSV table, often referred to simply as a "CSV file," is a structured data file format that follows the principles of Comma-Separated Values (CSV). It is a way to represent tabular data, much like a spreadsheet or a database table, in plain text format. 

In a CSV table:

+ **Rows:** Each line of the CSV file typically represents a row of data. Each row corresponds to a record, entity, or data entry, and it contains one or more values separated by a delimiter, most commonly a comma. Each row ends with a line break character, such as a newline ("\n") on Unix-based systems or a carriage return and newline ("\r\n") on Windows systems.

+ **Columns:** The values within each row are organized into columns, which represent different attributes or fields of the data. Columns are separated by the chosen delimiter, typically a comma, but semicolons, tabs, or other characters can also be used depending on specific requirements.

+ **Headers:** The first row is often interpreted as a header, with the value in each column storing the name of the column.

### CSV Dictionaries

A CSV dictionary refer to a CSV file used to store key-value pairs or dictionary-like data. In this context, each row in the CSV file would typically represent a key-value pair, with the key and value separated by a delimiter. Such files are sometimes used to store configuration data or simple databases where each row represents a record with attributes and values.

For example, a CSV dictionary for storing configuration settings might look like this:

``` csv
ServerName,example.com
Port,8080
Timeout,30
```

In this CSV dictionary, there are two columns (Key and Value), and each row represents a configuration setting and its corresponding value.

## Specification

### Versioning

The CSV Table Schema specification is versioned according to the scheme `major.minor.patch`. The major-minor part of the version number (e.g. `0.1`) SHALL denote the feature set of the specification. Patch versions address errors in or provide clarifications to this document, not the feature set. Tools supporting CSV Table Schema in version `0.1` SHALL be compatible with all `0.1.*` versions of CSV Table Schema. The patch version SHOULD NOT be taken into account by the tools, so that for example no difference is made between `0.1.0` and `0.1.1`.

### Format

An CSV Table Schema document that conforms to the CSV Table Schema specification is itself a JSON object that can be represented in JSON format.

All field names in the specification are case sensitive. 

### Schema

A CSV Table Schema document is a JSON document which can be validated against the [CSV Table Meta Schema](https://github.com/openpotato/csv-table-schema/tree/main/schemas/v0.1/schema.json). 

It consists of a JSON object with following properties:

+ `title` : A title for the CSV Schema. **This property is REQUIRED**.
+ `description` : An optional description for the CSV Schema.
+ Two mutually exclusive properties. **One of them is is REQUIRED**.
    + `table` : A table object. 
    + `dictionary` : A dictionary object. 

### Tables

A CSV table is a text file. Each line of the file is a data record. Each record consists of one or more fields, separated by a delimiter. Every record has the same sequence of fields. The first line of a CSV table can be defined as header row, representing the names of the columns.

#### Table 

A table is a JSON object with following properties:

+ `name` : Defines the name of the CSV table. **This property is REQUIRED**.
+ `description` : An optional description of the table.
+ `type` : Specifies the table type. **This property is REQUIRED**. Possible values are:
    + `ordered` : A table with columns appearing in defined order.
    + `unordered` : A table with columns appearing in any order.
    + `headless` : A table with no header row. Columns must appear in defined order.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `additionalColumns` : If `true` additional non-specified columns within the CSV document are allowed. Default value is `false`.
+ `columns` : An array of column definitions. **This property is REQUIRED**.

#### Columns

The `columns` array describes all columns within a CSV table. A column is a JSON object with following properties:

+ `name` : The name of the column. **This property is REQUIRED**.
+ `description` : An optional description of the column.
+ `type` : The data type of the column. **This property is REQUIRED**. Possible values are:
    + `string`
    + `int`
    + `float`
    + `bool`
    + `enum`
    + `enum-set`
    + `time`
    + `date`
    + `date-time`
    + `json`
    + `xml`
+ `optional` : If `true` the column must not be present in the CSV table. Default value is `false`. 
+ `nullable` : If `true` values of this column can be empty. Default value is `false`.
+ `pattern` : An optional regular expression which must always match with values of the column.

#### Unqiue keys

The `unqiueKeys` array describes unique keys within a CSV table. A unique key is itself a JSON array with column names representing exting column. Each column name is a `string` value.

### About optional columns

If the table type is:

+ `ordered`, a column with `optional=true` marks an optional column. If the column is missing in the table all other columns must still apear in defined order. 
+ `headless`, a column with `optional=true` marks the start of the optional column range. If the column is missing in the table all columns after this column are of course also missing. 

### Dictionaries

A CSV dictionary is a collection of key/value pairs. It is a CSV table with exactly two columns, a key column and a value column. While the key column is always a string containing the key name, the value column can have different data types for each key.

#### Dictionary 

A dictionary is a JSON object with following properties:

+ `name` : Defines the name of the CSV dictionary. **This property is REQUIRED**.
+ `description` : An optional description of the dictionary.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `keys` : An array of key definitions. **This property is REQUIRED**.

#### Keys

The `keys` array describes all keys within a CSV dictionary. A key is a JSON object with following properties:

+ `name` : The name of the key. **This property is REQUIRED**.
+ `description` : An optional description of the key.
+ `type` : The data type of the key. **This property is REQUIRED**. Possible values are:
    + `string`
    + `int`
    + `float`
    + `bool`
    + `enum`
    + `enum-set`
    + `time`
    + `date`
    + `date-time`
    + `json`
    + `xml`
+ `optional` : If `true` the key must not be present in the CSV dictionary. Default value is `false`. 
+ `nullable` : If `true` the value of this key can be empty. Default value is `false`.
+ `pattern` : An optional regular expression which must always match with the value of the key.

### Data types

A data type defines the type of value which is expected in a column or key. Based on the data type additional schema properties are available:

#### string

Represents a text string. The following additional schema properties are available:

+ `format` : Specifies the expected format of a string value. Possible values are:
    + `none` : Any string value. This is the default value.
    + `uuid` : An uuid acccording to [rfc4122](https://datatracker.ietf.org/doc/html/rfc4122). 
    + `uri` : An uri acccording to [rfc3986](https://datatracker.ietf.org/doc/html/rfc3986).
    + `email` : An email address acccording to [rfc5322, section 3.4.1](https://datatracker.ietf.org/doc/html/rfc5322#section-3.4.1).
    + `base64` : A base64 encoded string acccording to [rfc4648](https://datatracker.ietf.org/doc/html/rfc4648). 
+ `minLength` : Specifies the minimum character length of a string value.
+ `maxLength` : Specifies the maximun character length of a string value.

#### int

Represents an integral number. The following additional schema properties are available:

+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### float

Represents a floating number. The following additional schema properties are available:

+ `formats` : An array of valid format string according to [.NET Custom numeric format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-numeric-format-strings). **This property is REQUIRED**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### bool

Represents a boolean value. The following additional schema properties are available:

+ `trueValues` : An array of valid strings each representing a logical true value. **This property is REQUIRED**.
+ `falseValues` : An array of valid strings each representing a logical false value. **This property is REQUIRED**.

#### enum

Represents an enumeration value. The following additional schema properties are available:

+ `members` : An array of value objects each representing one enumeration value. **This property is REQUIRED**.

#### enum-set

Represents an set of enumeration values formatted as csv string. The following additional schema properties are available:

+ `members` : An array of value objects each repesenting one enumeration value. **This property is REQUIRED**.
+ `delimiterChar` : Defines the delimiter character of the CSV string format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV string format. Default value is `"`.

#### date-time

Represents a datetime value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is REQUIRED**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### date

Represents a date only value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is REQUIRED**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### time

Represents a time only value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is REQUIRED**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### json

Represents a json formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [JSON Schema](https://json-schema.org/draft/2020-12/json-schema-core.html). **This property is REQUIRED**.
    + `uri` : An uri to a valid JSON Schema. **This property is REQUIRED**.

#### xml

Represents a xml formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [XML Schema](https://www.w3.org/TR/xmlschema-0/). **This property is REQUIRED**.
    + `uri` : An uri to a valid XML Schema. **This property is REQUIRED**.
