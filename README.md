# Exercises

 - The main purpose of this repository is to collect exercises (pairs of *task* and *solution*).
 - These exercises are organized in a specific way; see [Database](#database).
- This organization thus provides an interface to the exercise pool, which can be exploited to
write code which, e.g., generates a worksheet from a user-defined selection of exercises;
see [Worksheet Generator](#worksheet-generator) for an example.

## Database

The directory `database` is a collection of exercises.

An exercise itself is a directory with the following structure:

```shell
|-- EXERCISE_NAME
|   |-- build
|   |   `-- print.pdf
|   |-- meta.json
|   |-- solution.tex
|   `-- task.tex
```

or for an exercise, where the solution is just (Python) code:

```shell
|-- EXERCISE_NAME
|   |-- build
|   |   `-- print.pdf
|   |   `-- solution.tex
|   |-- meta.json
|   |-- solution.py
|   `-- task.tex
```

- the user has to provide at least `task.tex` and `solution.tex (.py, .ipynb)`
- `meta.json` is optional
- the subdirectory `build/` and its content is automatically created by the programs
- `task.tex`:
    - contains the task
    - try to avoid your own commands (e.g., rather write `\mathbb{R}` instead of `\R`)
- `solution.tex` or `build/solution.tex` or `solution.py` or `solution.ipynb`
    - contains the solution to the task
    - if the solution just contains code, which is written in `solution.py`
      or `solution.ipynb`, then the `solution.tex` is auto-generated (simply inputs the
      code as listing) and thus located in the directory `build/`

## Database Editor

- nothing here yet
- utilities will be provided to work with the database, e.g., to
    - create and modify exercises in the correct format
    - create a preview-pdf of a single exercise (see `build/print.pdf`)
    - query the database based on the sidecar files meta.json
- (finally consider mongoDB?!)

## Worksheet Generator

- based on the database with the defined structure of an exercise one can write a simple
  LaTex template engine which collects some exercises from the database and puts them into
  a worksheet
- the provided example in `worksheet_generator` is quick and dirty; in its core it simply
  consists of the following two components
    1. a latex command  `\exercises{}...{}` in `latex_src/exercisecommand.sty` (should
       rather be an environment or existing
       package)
    2. some python functions, which take the selected exercises and generate
        - `exercises.tex` (list of exercise commands `\exercises{}...{}`)
        - `table.tex` (point table, if specified)

### Usage

1. **Template**: Create a template, say `sheet.tex` (or simply
   copy `worksheet_generator/examples/templates/sheet.tex`)
    - in the preamble
        - you have to write `\usepackage{exercisecommand}`
        - import any usepackages which may be needed to properly compile your selected
          exercises (e.g., `amsmath,amsthm,amssymb,...`)
    - in the document body you can input the following three **auto-generated** files:
        - `\input{newcommands}`
            - some arbitrarily many variables that you would like to use in your
              template (e.g., `\Lecturer`, `\Semester`)
            - you inform the code about your newcommands in the JSON file in the next step
        - `\input{table}`
            - contains a table with exercise numbers and specified Points
        - `\input{exercises}`
            - the selected exercises

2. **Config**: create a config JSON file, say `sheets.json`

```json
{
    "SHEET_NAME_1": {
        "exercises_database": "PATH TO DIRECTORY DATABASE",
        "build_directory": "WHERE YOUR OUTPUT IS PLACED",
        "template": {
            "filename": "PATH TO YOUR TEMPLATE .tex-FILE",
            "symlink": false,
            "newcommands": {
                "COMMAND_NAME": "COMMAND"
            }
        },
        "table": true,
        "exercises": {
            "REL. PATH FROM exercise_database TO EXERCISE-DIR": {
                "Points": "5",
                "tags": "Python"
            },
            "REL. PATH FROM exercise_database TO EXERCISE-DIR": {
                "Points": "10",
                "Header": false
            }
        }
    },
    "SHEET_NAME_2": {
        "...": "..."
    }
}
```

3. **Run**:

- execute `worksheet_create.py sheets.json`
- find the output in `build_directory/*`
- by default three versions of `exercises.tex` are generated, this results in three
  versions of your sheet
    - `SHEET_NAME-plain.pdf` (just the tasks)
    - `SHEET_NAME-inclass.pdf` (the tasks with empty space (squared) after each task to
      develop the solution in class)
    - `SHEET_NAME-solution.pdf` (the tasks with solutions)
- also the LaTex-source will be copied to modify the sheet later on if necessary

### Examples

- some examples can be found in `worksheet_generator/examples`
- copy them and adapt to your needs


# To Do

- data base editor, see [above](#database-editor)
- data base
  - clean up names
  - set reasonable meta in sidecar files meta.json
- docs
- test
- requirements