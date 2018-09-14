# Regular Expressions

Throughout the entire parser system it is possible to use regular expressions to help with identifying text.  Several features are provided to empower the use of regular expressions and handle a variety of use cases.

## pattern

- Type: `String`

The regular expression to evaluate.  If any of the advanced options are not needed and only a simple regular expression is needed then it is possible to write the regular expression as a string, otherwise an object is necessary.  See below examples:

### Example
```text
Value: 5
Color: Blue
```

Object method of representing the regular expression:

```json
{
  "type": "paragraph",
  "identifiers": [
    {
      "pattern": "Color:"
    }
  ]
}
```


String method of representing the regular expression:

```json
{
  "type": "paragraph",
  "identifiers": [
    "Color:"
  ]
}
```

?> It is advisable to use the string approach when advanced options are not being utilized.

## ignore_case

- Type: `Boolean`
- Default: `true`

Whether or not the regular expression engine should perform a search in a case sensitive manner.  A value of `true` indicates that it performs a **case-insensitive** search, which corresponds to `re.IGNORECASE` in Python.

## not

- Type: `Boolean`
- Default: `false`

If the regular expressions should be utilized to search if the text does not exist.

### Example

Given the following lines and the goal of extracting the amount of time that it took in minutes.
 
```text
Value: 5 in 20 minutes
Value: TBA
Value: 5
```

The below configuration would allow for the third line to be evaluated as NaN/None.  Otherwise it is not possible to use regular expressions to search for the non-existence of something.

```json
{
  "type": "paragraph",
  "identifiers": [
    "Value:"
  ],
  "field": "value_time",
  "field_parse": ".* in {INTEGER} minutes",
  "field_nan": [
    "TBA",
    {
      "pattern": "in",
      "not": true
    }
  ]
}
```

## Hints & Tips

Regular expressions are widely used throughout the template file.  Thus below are helpful hints to keep in mind when writing and using them.

### Lists

It is possible to supply a list of regular expressions for some attributes/features, such as but not limited to: [field_parse](templates/fields.md#field_parse), [field_nan](templates/fields.md#field_nan), [field_comment](templates/fields.md#field_comment), or identifiers within [Paragraphs](templates/paragraphs.md#identifiers) and [Tables](templates/tables.md#identifiers).  Generally, these instances should be documented within the feature.

When using a list then each regular expression will be evaluated in order until one is successful.  Therefore, ensure that the most specific regular expressions come first in the list with the most general/abstract ones towards the end of the list.

### Escaping Characters

As JSON allows for escaping characters, whenever a backward slash "\" is to be utilized, you need to make sure to escape it with a second slash.
  
```text
Desired text: \n
```

```json
{
  "text": "\\n"
}
```

?> Each slash requires its own escape.  Therefore to get double slashes "\\" would require the following in JSON: \\\\



### Unbalanced Parenthesis Error

There may be occasions where attempting to match text that has a parenthesis in it will give an error as shown below.  This error arises because the parser has been built to search for both a text match and a regular expression match, thus text that is not a regular expression is still evaluated as one.

```json
{
  "row": "Test A",
  "cell": "First) abc"
}
```

> <span style="color:red">sre_constants.error: unbalanced parenthesis</span>

To fix and avoid this error, make sure that any text matches are safe to be parsed with regular expressions.  For regular expressions this means that the parenthesis needs to be escaped.  A typical regular expression may look like:


```json
{
  "row": "Test A",
  "cell": "First) abc"
}
```

But an escaped regular expression that also follows the escaping rules defined in [Escaping Characters](templates/regex.md#escaping-characters) would look like this:

```json
{
  "row": "Test A",
  "cell": "First\\) abc"
}
```

### Non Capturing Groups

When utilizing features that extract parts of a string using regular expressions, most notably [field_parse](templates/fields.md#field_parse), it is important to utilize non-capturing groups within regular expressions.

!> Starting with version 2.1.3, the parser automatically converts all capturing groups within regular expressions into non-capturing groups.

#### Example

Given strings:
```text
A: yellow
B: blue
```

The following `field_parse` would be a typical regular expression to extract the colors.  However, in this case the system would give the results: **A** and **B**.

```json
"field_parse": "(A|B): {STRING}"
```

However, by appending *?:* to the front of the group the correct values of **yellow** and **blue** would be extracted:

```json
"field_parse": "(?:A|B): {STRING}"
```

### Tools

Many useful online tools exist for testing regular expressions.  Some of which are linked below:

- https://regexr.com/

- https://pythex.org/

