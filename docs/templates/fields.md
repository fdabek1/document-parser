# Fields

When data is extracted, fields are the way that it is parsed/verified and then put into the results file.  The configuration of fields can be used within any extraction element (paragraph, table, etc.).

## field_name

- Type: `String`

The name of the field.  This is the name that will be used for the column in the results file.

?> `field` can be used as a shortcut in place of `field_name`

!> `NOTE_DATE` is a special keyword that is to be used only for identifying the note's date: [note date identification](templates/note-identification.md#note-date).  Do not set the name to this keyword unless for the specified purpose.

## field_prefix

- Type: `String`

A string to append to the beginning of the field name.

?> This is used when nesting elements.  See the [Nesting](templates/nesting.md#prefix-amp-suffix-nesting) documentation on alternate value types that this attribute may contain. 

## field_suffix

- Type: `String`

A string to append to the end of the field name.

?> ?> This is used when nesting elements.  See the [Nesting](templates/nesting.md#prefix-amp-suffix-nesting) documentation on alternate value types that this attribute may contain.

## field_dtype

The data type for the field.

### integer

- Type: `Integer`
- Regex: `[-+]?[0-9]+`

### float

- Type: `Float`
- Regex: `[+-]?([0-9]+([.][0-9]*)?|[.][0-9]+)`

### date

- Type: `String`
- Regex: `.*`

### string

- Type: `String`
- Regex: `.*`

## field_nan

- Type: `String` or `List` or `Object`

The value to convert to NaN, which can consist of a single or a list of [Regular Expressions](templates/regex.md).

!> When extracted text is empty ("") then it is automatically treated as NaN.

### comment

- Type: `Boolean`
- Default: `False`

Whether or not the current NaN string should be saved as a comment.

For examples, refer to the [Output Examples](output.md#examples)

?> When the comment feature is used, the object version of the [Regular Expression](templates/regex.md) must be used.

### Example

Given data:

```
7
--
---
```

The following configuration would handle any amount of dashes as NaN.

```json
{
  "field": "raw_score",
  "field_dtype": "integer",
  "field_nan": "^-+$"
}
```

?> Note: using a single dash may result in negative or positive numbers being parsed as NaN, thus the presence of the regex commands **^** and **$**.

## field_parse

- Type: `String` or `List` or `Object`

A method to extract data from the content.  Keywords can be used to grab the necessary information.  The keywords correspond to the [field_dtype](#field_dtype)'s, but are to be fully capitalized.

Can consist of a single or a list of [Regular Expressions](templates/regex.md). 

!> The parser is limited to only using a single keyword at a time for this attribute.  This is due to a design decision.
To extract multiple pieces of information, add multiple fields using the `contents` field found in both [Paragraphs](templates/paragraphs.md) and [Tables](templates/tables.md).

### Example

```
2/7
6/7
4/7
2/7
3/7
```

```json
{
  "field": "raw_score",
  "field_dtype": "integer",
  "field_parse": "{INTEGER}/7"
}
```

### Custom Regular Expressions

- Type: `Object`

Due to the wildcard nature of the String expression (`.*`), as documented in [field_dtype](templates/fields.md#field_dtype), or any other changes in the dtype's, it may be necessary to utilize a custom regular expression when trying to extract and parse data.  In these cases, the `field_parse` must be set to an object of a regular expressions, as documented in [Regular Expressions](templates/regex.md#pattern) and a custom keyword `{KEYWORD}` should be utilized.

Given the following text and wanting to extract: **2:00**, **1:48**, **1:28**, and **2:00** respectively.

```text
112/120 in 2:00 mins

112/120 in 1:48/2:00 mins

120/120 in 1:28/2:00 mins.

100/120 in 2:00/2:00 mins
```

Original configuration that is unable to extract the correct information as it extracts **2:00**, **1:48/2:00**, **1:28/2:00**, and **2:00/2:00** respectively.
```json
{
  "field_parse": ".* in {KEYWORD}(?:/[0-9]+:?[0-9]+)? mins"
}
```

Using the feature extension of `field_parse`:

```json
{
  "field_parse": {
    "pattern": ".* in {KEYWORD}(?:/[0-9]+:?[0-9]+)? mins",
    "keyword": "[0-9]+:[0-9]+"
  }
}
```

## field_comment

- Type: `String` or `List` or `Object`
- Default: `None`

The associated [Comment](output.md#comment) for the field.  The same keywords can be utilized as found in [field_dtype](#field_dtype), however the comment will eventually be converted to a string by the system.

Can consist of a single or a list of [Regular Expressions](templates/regex.md). 

See [Output](output.md#examples) for examples containing comments.

## field_date_format

- Type: `String` or `List` or `Object`

Specifies the format of a date.  The format is specified by the Python 3 datetime library.  See Python's documentation on parsing dates:

https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior

?> When using the `List` type, the parser will attempt each date format in the list until it identifies a match.

### formats

- Type: `String` or `List`

When using `field_date_format` as an object, the date formats should be placed into this attribute.

### Example - Single Format

```text
May 3, 2015
December 11, 2017
```

```json
{
  "field_dtype": "date",
  "field_date_format": "%B %d, %Y"
}
```

### Example - Multiple Formats

```text
May 3, 2015
12/11/2017
```

```json
{
  "field_dtype": "date",
  "field_date_format": [
    "%B %d, %Y",
    "%m/%d/%Y"
  ]
}
```

```json
{
  "field_dtype": "date",
  "field_date_format": {
    "formats": [
      "%B %d, %Y",
      "%m/%d/%Y"
    ]
  }
}
```

### Multiple Dates

- Type: `Object`

There may instances where a date string has multiple dates and extracting a concrete date may be difficult.  See the below example for strings that contain multiple dates.  To address these cases, a multiple date extractor has been built into the parser with options, as shown below.

!> While the multiple date extractor has been written to handle a wide range of cases, due to the irregular nature of these strings, there exists the possibility that the dates may not be correctly extracted and the Python logic for extraction will need to be updated to handle additional cases.  The `formats` attribute can be used as a fallback option for when the date attempting to be parsed is not actually part of a multiple date. 

#### sort

- Type: `String`
- Value: `ascending` or `descending`

```json
{
  "field_date_format": {
    "multiple": true,
    "sort": "ascending"
  }
}
```

#### index

- Type: `Integer`

The index of the date to grab from the list of dates.

```json
{
  "field_date_format": {
    "multiple": true,
    "index": 3
  }
}
```

?> If the dates are not sorted then the order of the dates in the list correspond to the order in which they are listed within the string that was parsed.

#### Example

Putting together the different multiple date options, the following example has

```text
June 8 & March 20, 2011
September 4 & 27, 2010
February 15, December 5 & 6, 2013
```

```json
{
  "field": "min_date",
  "field_dtype": "date",
  "field_date_format": {
    "multiple": true,
    "sort": "ascending",
    "index": 0
  }
}
```


### Missing Date Parts

See the documentation on when date parts are missing in note identification [here](templates/note-identification.md#missing-date-parts).

## field_restrict

Restrict the values of data received in a more granular way than possible with [field_dtype](#field_dtype).  Different methods of restriction are available.

!> This feature is disabled as it needs to be fully built out. 

### Range

- Type: `Object` or `List`

Restrict to a specific range.

```json
{
  "field_restrict": {
    "lower": 0,
    "upper": 5
  }
}
```

Or to multiple ranges.

```json
{
  "field_restrict": [
    {
      "lower": 0,
      "upper": 3
    },
    {
      "lower": 7,
      "upper": 10
    }
  ]
}
```

!> Range restriction is not available when `field_dtype` is a `string`.

!> Range restriction is currently inclusive (>= and <=).

### Value

- Type: `field_dtype`

Restrict to a single value.  The type must be the same as the `field_dtype` of the field.

```json
{
  "field_restrict": "Yes"
}
```

### Values

- Type: `List` of `field_dtype`

A list of values that the field may be where the type is the same as `field_dtype`.

```json
{
  "field_restrict": [1, 2, 3]
}
```

```json
{
  "field_restrict": ["Y", "N"]
}
```


### Example

Example data:

```
2/7
5/7
8/7
```

Using the example data the following field configuration would pass for the first two lines, but give an error for the last line ("8/7") as 8 is out of the range.

```json
{
  "field": "raw_score",
  "field_dtype": "integer",
  "field_parse": "{INTEGER}/7",
  "field_restrict": {
    "lower": 0,
    "upper": 7
  }
}
```


## Shorthand

To encourage organization as well as to avoid typing `field_` repeatedly, a shorthand version can be utilized within the `contents` keyword within [Paragraphs](templates/paragraphs.md) and [Tables](templates/tables.md).

```json
"contents": {
  "type": "field",
  "name": "raw_score",
  "dtype": "integer",
  "parse": "{INTEGER}/7"
}
```

Alternatively, if only a few options for the field are to be utilized then the field can be explicitly written.  This is especially helpful when utilizing [Nesting](templates/nesting.md#fields) and the `field_dtype` and other attributes are passed down from the parent element. 

```json
  "field_name": "raw_score",
  "field_dtype": "integer",
  "field_parse": "{INTEGER}/7"
}
```