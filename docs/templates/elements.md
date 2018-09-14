# Elements

Three types of elements exist in the parser: [Paragraphs](templates/paragraphs.md), [Tables](templates/tables.md), and [Python](templates/python.md).  Each of these elements have their own unique attributes and functions available, but across both there exists helper attributes that can be utilized.

?> [Fields](templates/fields.md) can also be used across both elements.
 
## trim

- Type: `boolean`
- Default: `true`

Remove whitespace from beginning and end of string.

## strip

Different actions that allow for removing text from a string.

### text

- Type: `String` or `List`

Remove this string no matter where it occurs within the string.

### prefix

- Type: `string`

Remove this string if it is found at the beginning of the string.

!> This feature is currently disabled as it should be able to be accomplished with regular expressions.  This can be re-enabled if a use is found.

### suffix

- Type: `string`

Remove this string if it is found at the end of the string.

!> This feature is currently disabled as it should be able to be accomplished with regular expressions.  This can be re-enabled if a use is found.

### identifiers

A special strip attribute for Paragraphs.  See [strip.identifiers](templates/paragraphs.md#identifiers-1) for information on how to use this feature.


### Example

```text
Favorite Color: Blue
```

To extract only *Blue*

```json
"strip": {
  "prefix": "Favorite Color:"
}
```
