# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/)
and this project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## [2.3] - 2018-09-10
### Added
- Comments in JSON files are now allowed and automatically handled.

### Changed
- Code folder renamed to "src" due to package conflicts.

## [2.2.3] - 2018-09-10
### Changed
- Fixed non-capturing groups to work for custom {KEYWORD}.

## [2.2.2] - 2018-09-11
### Added
- Log statement for when note split found.
- Showing identifier that was matched.
- field_comment is allowed to not have any matches.
- Added raw value to output (as was expected).

### Changed
- Fixed crashing bug in output.
- Moved field value log statement above identified statement for separate lines.
- Printing out raw text for field_parse and field_comment errors.
- Fixed field_* shorthand being picked up outside of field objects.
- Updated field_nan documentation with warning about empty string evaluating to NaN.
- Fixed bug with field_nan evaluating empty string as always being NaN.
- Fixed bug where field_parse and field_comment failed before checking entire list.
- Fixed bug with comment always being same as raw value.

## [2.2.1] - 2018-09-10
### Added
- Sample vestibular data.

### Changed
- Fixed typos in documentation.

## [2.2] - 2018-09-03
### Added
- Document file paths now link directly to the folder that they are contained within for the Raw Value HTML Viewer.
- Added new feature: field_comment.
- Added feature to get year from last note when they are split.
- Full list support now available in field_parse, field_nan field_comment, field_date_format, and strip.text.
- The field "NOTE_DATE" automatically gets set to a dtype of "date".

### Changed
- Raw Value HTML Viewer styles updated.
- Updated all documentation to use "^-+$" rather than "-+" for NaN identification.
- Organized sample folders within data.

## [2.1.3] - 2018-09-03
### Added
- The raw flag now outputs unidentified elements.
- HTML Viewer for raw values output.

### Changed
- All capturing groups get converted to non-capturing groups within regular expressions.
- Fixed bug for raw flag and paragraphs (redid logic to avoid future bugs).

## [2.1.2] - 2018-08-30
### Changed
- Replacing unicode characters with standard utf8 characters.

## [2.1.1] - 2018-08-30
### Changed
- Fixed bug with multiple identifiers (cleaning text converted to boolean).

## [2.1.0] - 2018-08-29
### Changed
- Changed raw output to be a text file.
- Fixed separate_lines feature.
- Ignoring elements when field_name is not defined (allows for paragraphs to be identified and nothing to be extracted).
- Fixed rare bug with nested configuration options.

## [2.0.9dev] - 2018-08-29
### Added
- Added option to get raw values.

## [2.0.8dev] - 2018-08-28
### Added
- Wellness README to docs.
- Changelog to docs.

### Changed
- Strip now utilizes regular expressions for identifiers and text.
- Fixed bug with trim.
- Document counter starts at 1 rather than 0.
- Added document counter to ignore log lines.

### Removed
- Disabled prefix and suffix for strip.

## [2.0.7dev] - 2018-08-28
### Added
- Document counter added to log statement.

### Changed
- Files are now parsed in alphabetical order.
- Log statement for "completed file" moved from INFO to DEBUG.

## [2.0.6dev] - 2018-08-27
### Changed
- Rewrote regular expression parser with additional features.
- Custom regular expression support for field_parse has been renamed from "string" to "keyword".
- Changed attribute "regex" to "pattern" for regular expressions.
- Changed attribute "template" inside of patient identification to "parse".

## [2.0.5dev] - 2018-08-27
### Added
- Docs check for valid url to utilize.

## [2.0.4dev] - 2018-08-27
### Added
- Note split feature documented and implemented.
- Empty string values get evaluated to NaN/None.
- Newlines in text are replaced with spaces when performing field_parse.

## [2.0.3dev] - 2018-08-27
### Added
- Regular expression documentation on escaping characters.
- Added documentation to paragraphs on numbering within lists.
- Added run.py to make_dist.py.

### Changed
- Updated documentation on trim and strip to be inside of Elements.
- Fixed no identifier log for when separate_lines is false.

## [2.0.2dev] - 2018-08-26
### Added
- Distribution python script & corresponding folder.
- Field names in field validation error statements.
- Changelog began.

### Changed
- Table columns and rows that are missing no longer throw an error.  Now a warning is simply supplied.