# Execution

The parser can be run from either the command line or from a python script.

## Command Line

The preferred method to run the parser is using the provided command line script.  This script behaves similarly to any other command line program on a typical Unix system.

### Documentation 

To avoid duplicate documentation and the potential for out of sync documentation, please refer to documentation built into the command line program by using either the `-h` or `--help` flags:

```bash
python parse.py --help
```

### Example

```bash
python parse.py -p data/Patient_List.xlsx -t templates/slp/mixed.json -i data/slp/* -o results.csv -v
```

?> Remember to run the python script from the anaconda environment that was installed.  To load the environment, before running the above python script run: `conda activate parser` or `source activate parser`.



## Editor

In the cases where it may be necessary to run the parser from another python script or from 

```python
from code.log import DEBUG_NO_IDENTIFIER
from parse import run
import logging

if __name__ == '__main__':
    ARGUMENTS = {
        'patient-list': 'data/Patient_List.xlsx',
        'templates': [
            'templates/slp/mixed.json'
        ],
        'input': [
            'data/Mixed-SLP/SLP Eval Cruise, Tom.docx'
        ],
        'output': 'results.csv',
        'verbose': logging.INFO,
        'raw': False,
        'excerpt': 10,
        'log': None,
    }

    run(ARGUMENTS)
```

It is advised to name your script, `run.py`, in the root directory as the filename has already been added to the .gitignore file.

### Logging Levels

There are multiple logging levels supported within the parser.  These logging levels can be changed by using the `verbose` keyword in the above script.

?> All logging levels will continue to show errors and warnings.

#### logging.INFO

Shows the name and count of files being parsed.

#### logging.DEBUG

Outputs detailed statements of what was identified as well as the values that were extracted for output.

?> Includes everything within logging.INFO

#### DEBUG_NO_IDENTIFIER

Shows when a paragraph and table were encountered, but no identifier in the template to be matched against.

?> Includes everything within logging.DEBUG

?> This level is a custom level and has already been imported within the above Python script.