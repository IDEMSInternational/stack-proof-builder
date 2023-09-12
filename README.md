# STACK Proof builder

This is a tool to generate randomized proof questions for [STACK](https://stack-assessment.org/).

As an input, the user provides a base statement and its proof,
as well as a potential alterations and mistakes to be added to the proof,
all in spreadsheet format. The output is a set of STACK questions
containing variations of the proof, possibly with mistakes for the
students to identify, in Moodle xml format to be imported into a question
bank.

In the resulting questions, proof steps are numbered, and students are
prompted to idenify whether the proof is correct, and if not, give
the number of the proof step that contains a mistake.

To create a quiz, import a set of these questions into a question bank
category, and select the option "random question" in the quiz so that
a random question from the category is picked.

The current implementation is very crude, but functional.

## Input specification

To create a new proof, make a new folder of a name of your choice.
Two examples are provided: `cauchy` and `monotonicity`.

Each proof folder should contain 4 spreadsheet files.

- `proof.xlsx`: The base proof itself. The rows in this sheet are: Name of the question, question variables, theorem statement, followed by the steps of the proof.
- `variants.xlsx`: Alterations that can be applied to the proof. They consist of a set of changes that are made to the theorem, its steps or the question variables. The `type` column indicates whether the alteration leaves the proof intact, or introduces a mistake. The `status` column allows to selectively enable/disable different alteration, e.g. if we want to generate one set of questions with mistakes from a certain subset of mistakes.
- `substitutions.xlsx`: Definitions of those changes. Currently, two change types are supported (the `identifier` column is currently ignored):
    - `replace_step`/`replace_statement` (these are currently equivalent): In the theorem statement and all proof steps, looks for the string in the `orig` column and replaces any occurrence of it with the string in the `replacement` column.
    - `replace_variables`: In the question variables, looks for the string in the `orig` column and replaces any occurrence of it with the string in the `replacement` column.
- `feedback.xlsx`: Feedback and scores for different student answers. This sheet contains multiple entries for each alteration that introduces a mistake, with the feedback/score if the proof variant contains the given mistake. The `match` column identifies a proof step via a substring that has to be contained in the step. The `feedback` column provides the feedback that is given if the student picks this proof step, and the `score` column the corresponding score. For each mistake, there must also be an entry where `match` is blank, identifying the default feedback.
    - Note 1: Currently, no customizable feedback is supported if the proof has no mistakes.
    - Note 2: Currently, only up to 3 different feedbacks per mistake are supported (plus 1 default feedback.)

## Usage

In `generate.py`, edit the variable `QUESTION_NAME` to indicate the folder name of the question, and `NUMBER_VARIANTS` for the desired number of variants to be generated. Then run `generate.py`.
The alterations and mistakes that appear in each of the generated proof variants are picked randomly.
It may be that certain alterations are incompatible with each other: e.g. two alterations that modify the same proof step. The program ensures that in this case, only one (or none) of them is applied.

The output file is `[foldername].xml`, a file that can be imported into Moodle question banks. Furthermore, a file `error_log.txt` is produced. It contains any kind of warnings indicating issues with the input data, as well as a list of the generated questions (e.g. which alterations and mistakes each of them has applied to them).


