# Change Log

## CSV Table Schema specification

### 0.1.0 <small>_ July 31, 2024</small>

+ Complete refactoring of the json schema with breaking changes
+ Changed `$id` to `https://schemahub.openpotato.org/csv-table-schema/v0.1/schema.json`
+ Added `tableSet` next to `table` and `dictionary`
+ Added properties `$schema` and `notes`
+ Added properties `language`, `lineBreaks`, `skipFirstRows` and `skipEmptyRows` for tables and dictionaries
+ Added properties `code`, `alternativeNames` and `nullValues` for columns and keys
+ Added properties `exclusiveMinValue` and `exclusiveMaxValue` for numeric type
+ Removed property `format` from `string`
+ Renamed `int` to `integer`
+ Renamed `float` to `numeric`
+ Renamed `bool` to `boolean`
+ Many bug fixes

### 0.0.5 <small>_ September 22, 2023</small>

+ Added unique key definition for CSV tables
+ Some more restrictions in the schema
+ Rework of the website
+ Reorganization of the repository
+ Change of license to Apache License Version 2.0

### 0.0.4 <small>_ February 06, 2023</small>

+ First publication