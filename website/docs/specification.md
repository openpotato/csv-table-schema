A CSV Table Schema document is a JSON document which can be validated against the [CSV Table Meta Schema](https://github.com/openpotato/csv-table-schema/blob/main/spec/csv-table-schema.json). 

It consists of a JSON object with following properties:

+ `title` : A title for the CSV Schema. **This property is required**.
+ `description` : An optional description for the CSV Schema.
+ Two mutually exclusive properties. **One of them is is required**.
    + `table` : A table object. 
    + `dictionary` : A dictionary object. 

## Tables

A CSV table is a text file. Each line of the file is a data record. Each record consists of one or more fields, separated by a delimiter. Every record has the same sequence of fields. The first line of a CSV table can be defined as header row, representing the names of the columns.

### Table 

A table is a JSON object with following properties:

+ `name` : Defines the name of the CSV table. **This property is required**.
+ `description` : An optional description of the table.
+ `type` : Specifies the table type. **This property is required**. Possible values are:
    + `ordered` : A table with columns appearing in defined order.
    + `unordered` : A table with columns appearing in any order.
    + `headless` : A table with no header row. Columns must appear in defined order.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `additionalColumns` : If `true` additional non-specified columns within the CSV document are allowed. Default value is `false`.
+ `columns` : An array of column definitions. **This property is required**.

### Columns

The `columns` array describes all columns within a CSV table. A column is a JSON object with following properties:

+ `name` : The name of the column. **This property is required**.
+ `description` : An optional description of the column.
+ `type` : The data type of the column. **This property is required**. Possible values are:
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

### About optional columns

If the table type is:

+ `ordered`, a column with `optional=true` marks an optional column. If the column is missing in the table all other columns must still apear in defined order. 
+ `headless`, a column with `optional=true` marks the start of the optional column range. If the column is missing in the table all columns after this column are of course also missing. 

## Dictionaries

A CSV dictionary is a collection of key/value pairs. It is a CSV table with exactly two columns, a key column and a value column. While the key column is always a string containing the key name, the value column can have different data types for each key.

### Dictionary 

A dictionary is a JSON object with following properties:

+ `name` : Defines the name of the CSV dictionary. **This property is required**.
+ `description` : An optional description of the dictionary.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `keys` : An array of key definitions. **This property is required**.

### Keys

The `keys` array describes all keys within a CSV dictionary. A key is a JSON object with following properties:

+ `name` : The name of the key. **This property is required**.
+ `description` : An optional description of the key.
+ `type` : The data type of the key. **This property is required**. Possible values are:
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

## Data types

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

+ `formats` : An array of valid format string according to [.NET Custom numeric format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-numeric-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### bool

Represents a boolean value. The following additional schema properties are available:

+ `trueValues` : An array of valid strings each representing a logical true value. **This property is required**.
+ `falseValues` : An array of valid strings each representing a logical false value. **This property is required**.

#### enum

Represents an enumeration value. The following additional schema properties are available:

+ `members` : An array of value objects each representing one enumeration value. **This property is required**.

#### enum-set

Represents an set of enumeration values formatted as csv string. The following additional schema properties are available:

+ `members` : An array of value objects each repesenting one enumeration value. **This property is required**.
+ `delimiterChar` : Defines the delimiter character of the CSV string format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV string format. Default value is `"`.

#### date-time

Represents a datetime value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### date

Represents a date only value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### time

Represents a time only value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### json

Represents a json formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [JSON Schema](https://json-schema.org/draft/2020-12/json-schema-core.html). **This property is required**.
    + `uri` : An uri to a valid JSON Schema. **This property is required**.

#### xml

Represents a xml formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [XML Schema](https://www.w3.org/TR/xmlschema-0/). **This property is required**.
    + `uri` : An uri to a valid XML Schema. **This property is required**.
