# Note Identification


## Note Date

Note may have a date indicating the date that the note was written.  To extract this date into the result file, a special `field_name` of `NOTE_DATE` can be used.

Setting the field to `NOTE_DATE` will tell the parser to ignore any prefixes or suffixes that may have been set.  The parser identifies this name as a special keyword and thus no other field should ever use this name.

?> See [field_date_format](templates/fields.md#field_date_format) to correctly parse the date. ~~Make sure that the [field_dtype](templates/fields.md#field_dtype) is set to [date](templates/fields.md#date).~~  
(As of v2.2 the parser automatically sets the dtype to date when `NOTE_DATE` is encountered.)

### Simple Example

In this simple example, the paragraph element strips out the identifier as well as trims the whitespace, as per its default functionality. 
 
```text
Date: June 3, 2018
```

```json
{
  "type": "paragraph",
  "identifiers": [
    "Date:"
  ],
  "field": {
    "name": "NOTE_DATE",
    "dtype": "date",
    "date_format": "%B %d, %Y"
  }
}
```

### Complex Example

In this complex example additional extraction of the date is necessary.

```text
Date: Initial (June 3, 2018)
```

```json
{
  "type": "paragraph",
  "identifiers": [
    "Date:"
  ],
  "field": {
    "name": "NOTE_DATE",
    "dtype": "date",
    "parse": "Initial ({DATE})",
    "date_format": "%B %d, %Y"
  }
}
```

## Splitting Notes

- Type: `Object`

A single file may contain multiple notes and thus the document needs to be split up into separate notes, with separate dates and information.  To enable the parser's note splitting feature, set the attribute `note_split` in the root of the template's configuration file using the below options.  See [note_split](templates/configuration.md#note_split) in the Configuration section to see the reference to this attribute. 

!> Only a single method of splitting notes can be used at a time.  If you attempt to use multiple methods at once, the parser will print an error.

### identifiers

- Type: `List`

Searches the document for the existence of any of the identifiers within the list.  For paragraphs, searches the raw text.  For tables, searches each individual cell.

```json
{
  "note_split": {
    "identifiers": [
      "Follow Up"
    ]
  }
}
```

#### Example

Given a document with multiple notes separated:

<pre>
<b>Initial</b>
Color: Blue
Season: Summer

<b>Follow Up</b>
Color: Yellow
Season: Fall
</pre>


```json
{
  "note_split": {
    "identifiers": [
      "Follow Up"
    ]
  },
  "contents": [
    {
      "type": "paragraph",
      "identifiers": [
        "Color:"
      ],
      "field": "color"
    },
    {
      "type": "paragraph",
      "identifiers": [
        "Season:"
      ],
      "field": "season"
    }
  ]
}
```


### duplicate

- Type: `Boolean`

```json
{
  "note_split": {
    "duplicate": true
  }
}
```

#### Example

Given a document with multiple notes separated:

<pre>
<b>Initial</b>
Color: Blue
Season: Summer

<b>Follow Up</b>
Color: Yellow
Season: Fall
</pre>

Using the below configuration, once a paragraph definition has come up for the second time then the parser would identify as a new note has started.

```json
{
  "note_split": {
    "duplicate": true
  },
  "contents": [
    {
      "type": "paragraph",
      "identifiers": [
        "Color:"
      ],
      "field": "color"
    },
    {
      "type": "paragraph",
      "identifiers": [
        "Season:"
      ],
      "field": "season"
    }
  ]
}
```

?> The order of elements within the document does not matter for duplicates.  Example: in the **Follow Up** section, "Season" could come before "Color" and the duplicate would still trigger. 

?> If there is an element that only occurs in the second note then an incorrect mixture of data may occur, where data from the second note is placed into the first.  Example: A unique element that does not exist in the first note shows up at the beginning of the second note.  In this case it will be treated as being in the first note.


### Missing Date Parts

There may be instances where parts of a date may be missing due to a document containing multiple notes.  In these cases additional keywords are provided to be used within the [field_date_format](templates/fields.md#field_date_format) attribute.

#### LAST_YEAR 

The identified year in the previous note.

#### Example

<pre>
<b>Initial</b>
Date: January 3, 2018
Season: Winter

<b>Follow Up</b>
Date: July 4
Season: Summer
</pre>

In this example the year was not written for the follow up and thus since we are assuming that it is the same as the previous note we utilize `{LAST_YEAR}` within the date format as shown below.

```json
{
  "note_split": {
    "duplicate": true
  },
  "contents": [
    {
      "type": "paragraph",
      "identifiers": [
        "Date:"
      ],
      "field": {
        "name": "NOTE_DATE",
        "dtype": "date",
        "date_format": [
          "%B %d, %Y",
          "%B %d {LAST_YEAR}"
        ]
      }
    },
    {
      "type": "paragraph",
      "identifiers": [
        "Season:"
      ],
      "field": "season"
    }
  ]
}
```

!> Be aware that making the assumption that a part of the date is the same as the previous note is a dangerous assumption to make.  Use this feature with **extreme** caution.
