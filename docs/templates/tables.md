# Tables

Data that needs to be extracted can commonly be found within tables.

```json
{
  "type": "table",
  ...
}
```

## Identifiers

- Type: `List`

A list of strings to match in order to identify a table.

```json
"identifiers": [
  ...
]
```

### Example

Given two tables corresponding to intake and discharge tests:

| Intake Tests | Test A | Test B | Test C |
|:------------:|:------:|:------:|:------:|
|   Raw Score  |   50   |   49   |   55   |


| Discharge Tests | Test A | Test B | Test C |
|:---------------:|:------:|:------:|:------:|
|    Raw Score    |   50   |   49   |   55   |


If we want to read the intake table then the identifier list would look as such:

```json
"identifiers": [
  "Intake Tests"
]
```

Using Regular Expressions:

```json
"identifiers": [
  "Intake.*"
]
```


## Cells

A method to extract information from a singular cell.  For each cell the identifying row and column is specified.

The definitions for all cells to extract are stored in the table structure as `cells`:

```json
"cells": [
  ...
]
```

### row

- Type: `String`

A text string that can be consistently found, corresponding to the row in which in the data exists.

### col

- Type: `String`

A text string that can be consistently found, corresponding to the columns in which in the data exists.

### Example


|        |   | Raw Score |
|:------:|:-:|:---------:|
|        |   |     21    |
|  Some  | 3 |           |
| Test B |   |     49    |
|  Other | 5 |     1     |


```json
{
  "row": "Test B",
  "col": "Raw Score",
  "field": "raw_score",
  "field_dtype": "integer"
}
```



## Rows & Columns

As the method of using individual cells can be cumbersome for tables that follow a uniform structure, there exist two shortcuts for identifying a table by a specific column or row.

### Columns

For the instance where a table has all of the required values in a single column.  The column where data exists is defined and then a list of the rows is supplied afterwards.

The definitions for columns to extract are stored in the table structure as `cols`:

```json
"cols": [
  ...
]
```

#### col

- Type: `String`

A text string that can be consistently found, corresponding to the column in which all of the data exists.

#### rows

- Type: `List`

A list of objects, where each object contains a `row` key, corresponding to the row in which to extract in relation to the data column.

```json
{
  "row": "Test A"
}
```


#### Example


|  Test  | Raw Score |
|:------:|:---------:|
| Test A |     50    |
| Test B |     49    |
| Test C |     55    |


```json
{
  "col": "Raw Score",
  "field": {
    "dtype": "integer"
  },
  "rows": [
    {
      "row": "Test A",
      "field": "raw_a"
    },
    {
      "row": "Test B",
      "field": "raw_b"
    },
    {
      "row": "Test C",
      "field": "raw_c"
    },
  ]
}
```

### Rows

For the instance where a table has all of the required values in a single row.  The row where data exists is defined and then a list of the columns is supplied afterwards.

The definitions for rows to extract are stored in the table structure as `rows`:

```json
"rows": [
  ...
]
```

#### row

- Type: `String`

A text string that can be consistently found, corresponding to the row in which all of the data exists.

#### cols

- Type: `List`

A list of objects, where each object contains a `col` key, corresponding to the column in which to extract in relation to the data row.

```json
{
  "col": "Test A"
}
```


#### Example


|  Test  | Raw Score | Scaled Score | Percentile Score |
|:------:|:---------:|:------------:|:----------------:|
| Test A |     50    |      0.5     |        50       |


```json
{
  "row": "Test A",
  "field": {
    "dtype": "integer",
    "suffix": "_score"
  },
  "cols": [
    {
      "col": "Raw Score",
      "field": "raw"
    },
    {
      "col": "Scaled Score",
      "field": "scaled",
      "field_dtype": "float"
    },
    {
      "col": "Percentile Score",
      "field": "percentile"
    },
  ]
}
```

## Indices

There may be times where using a string to identify row or column may not be possible.  When this is the case then it is possible to use the indices of the row and columns.  The system will check for whether the identifier is a string or an integer in order to make the distinction.

- Type: `integer`

### Example


|        |   | Raw Score |
|:------:|:-:|:---------:|
|        |   |     21    |
|  Some  | 3 |           |
| Test B |   |     49    |
|  Other | 5 |     1     |


To get the value stored in the cell that currently has *21*:

```json
{
  "row": 1,
  "col": 2,
  "field": "raw_score",
  "field_dtype": "integer"
}
```

?> All indices start at 0.

!> It is advisable to use strings and regular expressions whenever possible to identify rows and columns, as the addition of a blank column or row will cause for the identification to still work.  Therefore, use this approach of indices as sparingly as possible and think carefully before resorting to this approach. 