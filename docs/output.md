# Output

Data within clinical documents can take on my different forms and thus a standardized way of extracting the data is necessary.  To accomplish this each time that data is extracted using [fields](templates/fields.md), it is filled and stored into three columns: **Computed**, **Comment**, and **Raw**.  These three columns make up a guideline of how data should be stored.

## Computed

- Type: [field_dtype](templates/fields.md#field_dtype)

This column corresponds to the simplest and best fit of the data possible to its data type, defined by the `field_dtype`.

?> The premise is that an analyst/researcher should be able to use the computed column without any modifications necessary.

## Comment

- Type: `String`
- Optional

Additional information about the **Computed** column, indicating the true value that was extracted from the document. 

?> An analyst/researcher should be able to use the comment column to identify how to recode the computed column for a given row.

## Raw

- Type: `String`

The entire contents of the paragraph or table cell before any processing was performed. 

?> An analyst/researcher should be able to use the raw column to see the text from the document in an unaltered form, as well as to see how the computed and comment fields were built.

## Examples

Below are examples that provide a guideline of how it should be parsed.  As these examples are not able to cover all use cases, make sure to properly think through and discuss the optimal way to extract data.

### Determining Data Type

<pre>
<strong>Test Results</strong>
3
2
5
8
<9
</pre>

The majority of the values correspond to an integer and/or because the clinicians informed us that this test is one that should be an integer, then we can conclude that this field is now an `Integer`.

Because the field\_dtype is an integer, the _<_ symbol would need to be removed to fit into the field.  The resulting columns would look like:

| Computed | Comment | Raw |
|:--------:|:-------:|:---:|
|     3    |         |  3  |
|     2    |         |  2  |
|     5    |         |  5  |
|     8    |         |  8  |
|     9    |    <9   |  <9 |

The comment column does not contain the _<_ symbol by itself as this may be difficult for an analyst/researcher to understand its purpose and meaning unless they look at the raw column.


```json
{
  "field": {
    "name": "score",
    "dtype": "integer",
    "parse": "{INTEGER}",
    "comment": {
      "pattern": "{KEYWORD}",
      "keyword": "<[0-9]+"
    }
  }
}
```

### Comment Text

<pre>
<strong>Test Results</strong>
3; on a rainy day
2
5; on a sunny day
8
9
</pre>


| Computed |     Comment    |        Raw        |
|:--------:|:--------------:|:-----------------:|
|     3    | on a rainy day | 3; on a rainy day |
|     2    |                |         2         |
|     5    | on a sunny day | 5; on a sunny day |
|     8    |                |         8         |
|     9    |                |         9         |


The semicolon was removed from the comment field for ease of reading.

```json
{
  "field": {
    "name": "score",
    "dtype": "integer",
    "parse": "{INTEGER}",
    "comment": "[0-9]; {STRING}"
  }
}
```

### Mixture of Extra & Comments

This example shows a mixture of having extra characters as well as comments 

<pre>
4
<4
<4; with hearing aid
4; with hearing aid
</pre>


| Computed |        Comment       |          Raw         |
|:--------:|:--------------------:|:--------------------:|
|     4    |                      |           4          |
|     4    |          <4          |          <4          |
|     4    |   with hearing aid   |  4; with hearing aid |
|     4    | <4; with hearing aid | <4; with hearing aid |

```json
{
  "field": {
    "name": "score",
    "dtype": "integer",
    "parse": "{INTEGER}",
    "comment": [
      ".+; {STRING}",
      {
        "pattern": "{KEYWORD}",
        "keyword": "<[0-9]+"
      }
    ]
  }
}
```

### Comments without Data

<pre>
4
<4
could not perform
4
</pre>


| Computed |      Comment      |        Raw        |
|:--------:|:-----------------:|:-----------------:|
|     4    |                   |         4         |
|     4    |         <4        |         <4        |
|    NaN   | could not perform | could not perform |
|     4    |                   |         4         |

```json
{
  "field": {
    "name": "score",
    "dtype": "integer",
    "parse": "{INTEGER}",
    "comment": {
      "pattern": "{KEYWORD}",
      "keyword": "<[0-9]+"
    },
    "nan": [
      {
        "pattern": "{KEYWORD}",
        "keyword": "[A-z ]+",
        "comment": true
      }
    ]
  }
}
```

### Empty Data

There are times where the data may contain a string that corresponds to it being empty.  This example shows how to handle these cases.

<pre>
4
4
---
--
4
to be determined
</pre>


| Computed |      Comment     |        Raw       |
|:--------:|:----------------:|:----------------:|
|     4    |                  |         4        |
|     4    |                  |         4        |
|    NaN   |                  |        ---       |
|    NaN   |                  |        --        |
|     4    |                  |         4        |
|    NaN   | to be determined | to be determined |


The corresponding template configuration using the corresponding [comment](templates/fields.md#comment) feature of [field_nan](templates/fields.md#field_nan).

```json
{
  "field": {
    "name": "score",
    "dtype": "integer",
    "parse": "{INTEGER}",
    "nan": [
      {
        "pattern": "to be determined",
        "comment": true
      },
      "^-+$"
    ]
  }
}
```

