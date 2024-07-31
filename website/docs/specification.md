# CSV Table Schema Specification

#### Version 0.1.0

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

### CSV Table Sets

A CSV table set is a polymorphic table without a header row, in which each row can be different and is identified by a discriminator value.

For example, a CSV polymorphic table for storing two different tables might look like this:

``` csv
r1,John,30,Berlin
r1,Alice,25,London
r2,Berlin,DE
r2,London,UK
```

In this CSV table set, the first column is the discriminator. Possible values are `r1` and `r2`. Rows with `r1` contain three additional columns, rows with `r2` contain two additional columns.

## Specification

### Versioning

The CSV Table Schema specification is versioned according to the scheme `major.minor.patch`. The major-minor part of the version number (e.g. `0.1`) SHALL denote the feature set of the specification. Patch versions address errors in or provide clarifications to this document, not the feature set. Tools supporting CSV Table Schema in version `0.1` SHALL be compatible with all `0.1.*` versions of CSV Table Schema. The patch version SHOULD NOT be taken into account by the tools, so that for example no difference is made between `0.1.0` and `0.1.1`.

### Format

An CSV Table Schema document that conforms to the CSV Table Schema specification is itself a JSON object that can be represented in JSON format.

All field names in the specification are case sensitive. 

### Schema

A CSV Table Schema document is a JSON document which can be validated against the [CSV Table Meta Schema](https://github.com/openpotato/csv-table-schema/tree/main/schemas/v0.1/schema.json). 

It consists of a JSON object with following properties:

+ `$schema` : The used dialect of the CSV schema. Can be used to verify that the schema is valid.
+ `version` : The version of the CSV schema.
+ `title` : A title for the CSV Schema. **This property is REQUIRED**.
+ `description` : An human-readable description for the CSV Schema.
+ `notes` : Additional human-readable notes for the CSV Schema.
+ Three mutually exclusive properties. **One of them is is REQUIRED**.
    + `table` : A table object. 
    + `dictionary` : A dictionary object. 
    + `tableSet` : A table set object. 

The object MAY be extended.

### Tables

A CSV table is a text file. Each line of the file is a data record. Each record consists of one or more fields, separated by a delimiter. Every record has the same sequence of fields. The first line of a CSV table can be defined as header row, representing the names of the columns.

#### Table 

A table is a JSON object with following properties:

+ `name` : Defines the name of the CSV table. **This property is REQUIRED**.
+ `description` : An optional human-readable description of the table.
+ `type` : Specifies the table type. **This property is REQUIRED**. Possible values are:
    + `ordered` : A table with columns appearing in defined order.
    + `unordered` : A table with columns appearing in any order.
    + `headless` : A table with no header row. Columns must appear in defined order.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `lineBreaks` : Defines the valid line breaking strings of the CSV format. Default value is `\r\n` and `\n`.
+ `skipFirstRows` : Defines the number of rows to skip at the beginning of the CSV file.
+ `skipEmptyRows` : Specifies whether empty rows should be ignored. Empty rows are rows in which each column has an empty value.
+ `language` : Defines the content language of the CSV format. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). Can be overriden by the language tag of a column.
+ `additionalColumns` : If `true` additional non-specified columns within the CSV document are allowed. Default value is `false`.
+ `columns` : An array of column definitions. **This property is REQUIRED**.

This object MAY be extended.

#### Columns

The `columns` array describes all columns within a CSV table. A column is a JSON object with following properties:

+ `code` : The code of the column. **This property is REQUIRED**.
+ `name` : The human-readable name of the column.
+ `alternativeNames` : A list of alternative names of the column.
+ `description` : An human-readable description of the column.
+ `type` : The data type of the column. **This property is REQUIRED**. Possible values are:
    + `string`
    + `integer`
    + `numeric`
    + `boolean`
    + `enum`
    + `enum-set`
    + `time`
    + `date`
    + `date-time`
    + `json`
    + `xml`
+ `optional` : If `true` the column must not be present in the CSV table. Default value is `false`. 
+ `nullable` : If `true` values of this column can be empty. Default value is `false`.
+ `nullValues`: If `nullable=true` the list of values that represent the null state.

#### Unique keys

The `uniqueKeys` array describes unique keys within a CSV table. A unique key is itself a JSON array with column codes representing an existing column. Each column code is a `string` value.

#### About optional columns

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
+ `lineBreaks` : Defines the valid line breaking strings of the CSV format. Default value is `\r\n` and `\n`.
+ `skipFirstRows` : Defines the number of rows to skip at the beginning of the CSV file.
+ `skipEmptyRows` : Specifies whether empty rows should be ignored. Empty rows are rows in which each column has an empty value.
+ `language` : Defines the content language of the CSV format. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). Can be overriden by the language tag of a column.
+ `keys` : An array of key definitions. **This property is REQUIRED**.

This object MAY be extended.

#### Keys

The `keys` array describes all keys within a CSV dictionary. A key is a JSON object with following properties:

+ `code` : The code of the key. **This property is REQUIRED**.
+ `name` : The human-readable name of the key. 
+ `alternativeNames` : A list of alternative names of the column.
+ `description` : An human-readable description of the key.
+ `type` : The data type of the key. **This property is REQUIRED**. Possible values are:
    + `string`
    + `integer`
    + `numeric`
    + `boolean`
    + `enum`
    + `enum-set`
    + `time`
    + `date`
    + `date-time`
    + `json`
    + `xml`
+ `optional` : If `true` the key must not be present in the CSV dictionary. Default value is `false`. 
+ `nullable` : If `true` the value of this key can be empty. Default value is `false`.
+ `nullValues`: If `nullable=true` the list of values that represent the null state.

### Table Sets

#### TableSet

A table set is a JSON object with following properties:

+ `name` : Defines the name of the CSV table set. **This property is REQUIRED**.
+ `description` : An optional human-readable description of the table set.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `lineBreaks` : Defines the valid line breaking strings of the CSV format. Default value is `\r\n` and `\n`.
+ `skipFirstRows` : Defines the number of rows to skip at the beginning of the CSV file.
+ `skipEmptyRows` : Specifies whether empty rows should be ignored. Empty rows are rows in which each column has an empty value.
+ `language` : Defines the content language of the CSV format. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). Can be overriden by the language tag of a column.
+ `additionalColumns` : If `true` additional non-specified columns within the CSV document are allowed. Default value is `false`.
+ `tables` : An array of table definitions. **This property is REQUIRED**.

This object MAY be extended.

#### Tables

The `tables` array describes all tables within a CSV table set. A table is a JSON object with following properties:

+ `name` : Defines the name of the table. **This property is REQUIRED**.
+ `description` : An optional human-readable description of the table.
+ `columns` : An array of column definitions. **This property is REQUIRED**.

#### Columns

The `columns` array describes all columns within a table of a CSV table set. A column is a JSON object with following properties:

+ `code` : The code of the column. **This property is REQUIRED**.
+ `name` : The human-readable name of the column.
+ `description` : An human-readable description of the column.
+ `type` : The data type of the column. **This property is REQUIRED**. Possible values are:
    + `discriminator`
    + `string`
    + `integer`
    + `numeric`
    + `boolean`
    + `enum`
    + `enum-set`
    + `time`
    + `date`
    + `date-time`
    + `json`
    + `xml`
+ `optional` : If `true` the column must not be present in the CSV table. Default value is `false`. 
+ `nullable` : If `true` values of this column can be empty. Default value is `false`.
+ `nullValues`: If `nullable=true` the list of values that represent the null state.

A column of type `discriminator` MUST occur exactly once.

#### Unique keys

The `uniqueKeys` array describes unique keys within a CSV table set. A unique key is itself a JSON array with column codes representing an existing column. Each column code is a `string` value.

### Data types

A data type defines the type of value which is expected in a column or key. Based on the data type additional schema properties are available:

#### discriminator

Represents a discriminator value for table sets. The following additional schema properties are available:

+ `values` : An array of value objects each representing one allowed value. **This property is REQUIRED**.
+ `language` : Defines the language of the content of the discriminator value. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). 

This data type can only occur within table sets.

#### string

Represents a text string. The following additional schema properties are available:

+ `minLength` : Specifies the minimum character length of a string value.
+ `maxLength` : Specifies the maximun character length of a string value.
+ `language` : Defines the language of the content of the string value. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). 
+ `pattern` : An optional regular expression which must always match with the string value.

#### integer

Represents an integral number. The following additional schema properties are available:

+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### numeric

Represents a floating number. The following additional schema properties are available:

+ `formats` : An array of valid format string according to [.NET Custom numeric format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-numeric-format-strings). **This property is REQUIRED**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.
+ `exclusiveMinValue`: Specifies the exclusive lower limit for a value.
+ `exclusiveMaxValue`: Specifies the exclusive upper limit for a value.

#### boolean

Represents a boolean value. The following additional schema properties are available:

+ `trueValues` : An array of valid strings each representing a logical true value. **This property is REQUIRED**.
+ `falseValues` : An array of valid strings each representing a logical false value. **This property is REQUIRED**.

#### enum

Represents an enumeration value. The following additional schema properties are available:

+ `members` : An array of value objects each representing one enumeration value. **This property is REQUIRED**.
+ `language` : Defines the language of the content of the member value. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). 

#### enum-set

Represents an set of enumeration values formatted as csv string. The following additional schema properties are available:

+ `members` : An array of value objects each repesenting one enumeration value. **This property is REQUIRED**.
+ `delimiterChar` : Defines the delimiter character of the CSV string format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV string format. Default value is `"`.
+ `language` : Defines the language of the content of the member value. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). 

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

+ `schema` : Specifies a [JSON Schema](https://json-schema.org). **This property is REQUIRED**.
    + `uri` : An uri to a valid JSON Schema. **This property is REQUIRED**.

#### xml

Represents a xml formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [XML Schema](https://www.w3.org/TR/xmlschema-0/). **This property is REQUIRED**.
    + `uri` : An uri to a valid XML Schema. **This property is REQUIRED**.

### Extension of the Specification

The CSV Table Schema specification can be extended with additional data at certain points.

The properties of these extensions are implemented as free fields, which must always be prefixed with `x-` (e.g., `x-external-id`). The value can be a string, a number, a boolean value, null, an object, or an array.

The extensions may or may not be supported by the available tools. Ideally, these tools can be extended to add the desired support (e.g., in Open Source projects).

Example:

``` json
"table": {
  "name": "Subjects.csv",
  "type": "unordered",
  "x-origin": "https://www.example.com"
}
```