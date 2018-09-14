# Nesting

There may be occasions where different elements may be nested within one another.  For example, a table may exist within another table or a paragraph within a table cell.  Because of these cases it is possible to nest [Paragraph](templates/paragraphs.md) and [Table](templates/tables.md) elements within one another using the keyword `contents`.

!> Currently elements may only be nested inside of tables.  No examples have been found of having elements within a paragraph.

## Elements

|  Date  |                                                    Test Questions                                         |
|:------:|:---------------------------------------------------------------------------------------------------------:|
| June 8 | Score 12 <br> <br> Favorite Color: Blue <br> <br> Favorite Season: Summer <br> <br> Activity? Run outside |


```json
{
  "type": "table",
  "identifiers": [
    "Test Questions"
  ],
  "cells": [
    {
      "col": "Date",
      "row": 1,
      "field": "test_date",
      "field_dtype": "integer",
      "field_parse": "June {INTEGER}"
    },
    {
      "col": "Test Questions",
      "row": 1,
      "contents": [
        {
          "type": "field",
          "name": "score",
          "dtype": "integer",
          "parse": "Score {INTEGER}.*"
        },
        {
          "type": "paragraph",
          "identifiers": [
            "Favorite Color"
          ],
          "field": "color",
          "field_dtype": "string"
        },
        {
          "type": "paragraph",
          "identifiers": [
            "Favorite Season"
          ],
          "field": "season",
          "field_dtype": "string"
        },
        {
          "type": "paragraph",
          "identifiers": [
            "Activity"
          ],
          "field": "activity",
          "field_dtype": "string"
        }
      ]
    }
  ]
}
```

Nesting paragraph inside table, table inside table, table inside paragraph, etc.


## Fields

### Basic Nesting

Because field attributes may be repeated across elements, it is possible to nest them and thus allow for inheritance.  The parser system will automatically look for any field definitions 


#### Example

An example of a table definition without field nesting:

```json
{
  "col": "Raw Score",
  "rows": [
    {
      "row": "Test A",
      "field": "raw_a",
      "field_dtype": "integer",
      "parse": "{INTEGER}/7",
      "nan": "^-+$"
    },
    {
      "row": "Test B",
      "field": "raw_b",
      "field_dtype": "integer",
      "parse": "{INTEGER}/7",
      "nan": "^-+$"
    },
    {
      "row": "Test C",
      "field": "raw_c",
      "field_dtype": "integer",
      "parse": "{INTEGER}/7",
      "nan": "^-+$"
    }
  ]
}
```

And the same table simplified using basic field nesting:

```json
{
  "col": "Raw Score",
  "field_dtype": "integer",
  "field_parse": "{INTEGER}/7",
  "field_nan": "^-+$",
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
    }
  ]
}
```

### Prefix & Suffix Nesting

As names may need to be repeated, both [field_prefix](templates/fields.md#field_prefix) and [field_suffix](templates/fields.md#field_suffix) can be utilized to pass down the prefix's and suffix's.


#### Example

```json
{
  "col": "Raw Score",
  "field_prefix": "raw_",
  "field_dtype": "integer",
  "field_parse": "{INTEGER}/7",
  "field_nan": "^-+$",
  "rows": [
    {
      "row": "Test A",
      "field": "a"
    },
    {
      "row": "Test B",
      "field": "b"
    },
    {
      "row": "Test C",
      "field": "c"
    }
  ]
}
```

#### Chaining Prefix & Suffix

All `field_prefix` and `field_suffix` attributes are remembered such that a series of them can be used.  In the following example the resulting field names would consist of *main_raw_a*, *main_raw_b*, and *main_raw_c*.

```json
{
  "type": "table",
  "identifiers": [
    "Main Test"
  ],
  "field": {
    "prefix": "main_"
  },
  "cols": [
    {
      "col": "Raw Score",
      "field": {
        "prefix": "raw_",
        "dtype": "integer",
        "parse": "{INTEGER}/7",
        "nan": "^-+$"
      },
      "rows": [
        {
          "row": "Test A",
          "field": "a"
        },
        {
          "row": "Test B",
          "field": "b"
        },
        {
          "row": "Test C",
          "field": "c"
        }
      ]
    }
  ]
}
```


#### Overriding Prefix & Suffix

There may exist times that the chained prefix and suffix attributes may want to be overridden.  In these cases there are several options to override them within a child element.

- Type: `null`

Ignore all previous prefix's and set it to none.

```json
{
  "field_prefix": null
}
```

- Type: `list`

Ignore all previous prefix's and set to a new value.

```json
{
  "field_prefix": ["secondary_"]
}
```


### Shorthand

To avoid having to type out all field attributes with `field_`, especially in cases where a lot of attributes exist, the system supports using `field` containing an object of attributes.

```json
{
  "col": "Raw Score",
  "field": {
    "prefix": "raw_",
    "dtype": "integer",
    "parse": "{INTEGER}/7",
    "nan": "^-+$"
  },
  "rows": [
    {
      "row": "Test A",
      "field": "a"
    },
    {
      "row": "Test B",
      "field": "b"
    },
    {
      "row": "Test C",
      "field": "c"
    }
  ]
}
```