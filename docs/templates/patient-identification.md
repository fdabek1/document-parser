# Patient Identification

To identify the patient that corresponds to a file, there are multiple methods that can be utilized.  The parser will go through each method in the list until it finds a patient successfully.

## Types

### filename

- Type: `String`

Search the filename for patient identifying information.

!> The extension part of the filename is stripped such that only the beginning part is searched.

```json
{
  "type": "filename",
  "parse": "SLP +Eval +{LAST_NAME}, +{FIRST_NAME}"
}
```

### folder

- Type: `String`

Search the folder name for patient identifying information.

## Keywords

When filling in the regular expression for each type of patient identifier, there are keywords available corresponding to the patient.  These keywords are referenced against the information in the [Patient List](patient-list.md) file.

Below are the keywords along with their corresponding regular expression utilized (regex).

### REFERRAL_ID

- Type: `Integer`
- Regex: `[0-9]+`

The patient's referral id.

### FIRST_NAME

- Type: `String`
- Regex: `[a-zA-Z ,.'-]+`

The patient's first name.

### LAST_NAME

- Type: `String`
- Regex: `[a-zA-Z ,.'-]+`

The patient's last name.

### FIRST_INITIAL

- Type: `String`
- Regex: `[a-zA-Z]`

The first character from the patient's first name.

### LAST_INITIAL

- Type: `String`
- Regex: `[a-zA-Z]`

The first character from the patient's last name.


## Multiple Identifiers

In cases where multiple formats are used to identify patients and a single regular expression does not suffice, it is possible to construct a list of identifiers.

```json
"identifiers": [
    {
      "type": "filename",
      "parse": "SLP +Eval +{LAST_NAME}, +{FIRST_NAME}"
    },
    {
      "type": "filename",
      "parse": "{LAST_NAME}.{FIRST_NAME}.*"
    }
  ],
```

?> Note: The list of identifiers can contain mixed types.