# Configuration

The general configuration of the parser.


## type

- Type: `String`
- Default: `docx`
- Values: `docx`, `xlsx`

The type of file that will be parsed.

!> Currently only `docx` is supported; `xlsx` is coming in a future version.

## version

- Type: `String`

The version of the template file.

?> This variable servers as a helper.  The parser has the ability to keep track of the results based on the version.

## parser_version

- Type: `String`

The version of the parser that the template file was checked against.

!> A warning will be thrown if the template file's parser_version does not match the currently installed version of the parser.

## note_split

- Type: `Object`
- Optional

Configures the note splitting feature of the parser.  See [Note Splitting](templates/note-identification.md#splitting-notes) for more information on how to configure and use this feature.

## data

### separate_lines

- Type: `Boolean`
- Default: `false`

Whether or not paragraph data can be found on separate lines.  See [Paragraphs](templates/paragraphs.md#separate_lines) for more information on this attribute.

### contents

- Type: `List`

A list of the contents of the document (using [Paragraphs](templates/paragraphs.md) and [Tables](templates/tables.md)).

?> The list of contents do not need to be in order as they appear in the document.


### field_*

The `data` attribute is the first location that field inheritance/nesting is possible.  See [Nesting](templates/nesting.md#fields) for more information.