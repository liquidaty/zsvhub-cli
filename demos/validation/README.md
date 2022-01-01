# Demo: validation

## Overview
`zh` (zsvhub's command-line extension for `zsv`) has sophisticated validation capabilities
that will be introduced in this demo through the use of the `validate` command.

We will walk through the steps for generating a CSV summary as well as
an XLSX or XLSM validation report, examples
of which you can find [here](validation.xlsx) and [here](validation.xlsm)

Here are some illustrative screenshots of what this report looks like:

<img src="https://user-images.githubusercontent.com/26302468/131268149-81d91dbf-ff16-41ea-b4af-d1db71b227db.png" width="350" />

![image](https://user-images.githubusercontent.com/26302468/131268238-a233f501-ea7a-425e-87c9-85925d2f6674.png)

![image](https://user-images.githubusercontent.com/26302468/131268336-3dcef4e1-8e45-4233-a23e-87d7d2b6a647.png)

![image](https://user-images.githubusercontent.com/26302468/131268355-0b88c7bc-39f4-4bab-85e6-e500aa2532c0.png)

![image](https://user-images.githubusercontent.com/26302468/131268382-2fa85c73-f127-4590-89d8-c501834d6c52.png)


## Introduction

When it comes to validation, the biggest challenges usually have little to do
with the actual logic required to perform validation.
In fact, validation logic, anywhere you look, is usually quite basic:

* duplicate rows or row IDs
* blank values that should be non-blank (always, or conditionally)
* non-blank values that should be blank (always, or conditionally)
* invalid values (based on an enumeration, a lookup table, or similar)
* value out of range (numbers, dates)
* logical relationship between several columns (e.g. A + B must equal C)

There are countless tools that can apply this kind of logic to your data.
That being the case, why is validation still a pain?
Unfortunately, there are a lot of reasons! Consider:

* Generating and maintaining validation rules is a huge burden.
  Imagine you have 100 columns, of which 80%
  are numeric or dates. Right off the bat, that's 80 rules to validate that your
  input can be converted to the proper data type. Add in
  validations for lengths, ranges, enumerations, required and
  conditionally required, and you probably have at least three rules per column
  before you've even started applying domain-specific logic
* getting your data into a form that your logic can understand. For example,
  if you want to validate a date, you may require
  a very specific date input format which your data does not conform to (e.g.
  yyyy-mm-dd instead of mm/dd/yy), or
  you may require that number ranges conform to a
  particular scaling convention (e.g. "0.70" instead of "70" or "70%")
* validation logic permutations. Imagine in your dictionary A + B = C, and C should be 
* seeing the big picture. If you are going to run 600 validation rules on 25,000
  rows of data, how do you quickly get
  a summary of the results to get a sense of how "close" you are to having
  acceptable data?
* drilling-down on the specific rows violating any specific validation rule.
  You want to be able to do this quickly,
  anytime, for any validation rule, and without having to go through someone
  else to do it for you-- and to be able to
  make that capability portable so that you can send it to a counterparty
  and/or farm it out to many people to fix independently
* drilling-down on only the relevant columns of data so as not to get lost in a sea of data.
  Imagine you have a validation
  rule that says "X and Y must be both blank or both non-blank", and your input data has 350
  columns of data, of which X
  and Y are located in position 59 and 310. A useful drill-down will show you only those two
  columns; showing you all 350 will
  only waste everyone's time. The catch? Which columns are "relevant" will be different for
  every validation rule, will change
  the moment the validation rule logic changes, and may be entirely unrelated to the rule
  logic (e.g. a row identifier to be included in all drill-down results).
* retaining the original data format. If you receive data from someone else, and
  had to convert it to run your validation
  tests (for example, convert a percent value from "70" to "0.70"), then, if you
  want to send those results back to the data provider (maybe with a note saying
  "please fix this!!"),
  then it is far easier-- and in sometimes, only possible-- for the provider to
  take action if the data retains its original format. This can be especially
  important when the data is being provided under a legal contract where
  data reps and warranties may come into play

## XLSX reporting to the rescue

In many cases, the XLSX (i.e. Excel workbook) format is cumbersome. It can be slow to load
(due to the fact that it's actually a zip archive of a bunch of somewhat bloated XML files),
and for users interested solely in data as opposed to cell formatting, charts etc,
its extra features often go unused.

However, in the context of validation, XLSX offers a few features that are extremely
useful:
1. Multiple worksheets (tabs). In one file, we can have a summary, a data dump, and
   breakout tabs, for each validation rule with 1 or more results
2. Hyperlinks. This makes it easy to drill-down on any line of our summary, and jump
   directly to a tab that contains the detailed data behind that summary line
3. Macros. If we only had one sheet of data, we could just pump it into some other utility
   to format it nicely, without the use of any macros, before we hand it over to the boss.
   But in this context, where we could have dozens or hundreds of break-out tabs, macros
   at least give us the option to pseudo-automate any post-processing steps

Note: the XLSX format has a limit of 1mm rows, but you can still run `validate` on
datasets larger than 1mm rows by either generating only the summary tab (in CSV),
or generating an XLSX with the understanding that the data tab (if you choose to generate)
will be limited to 1mm rows and therefore will be incomplete, and each breakout can only
contain up to 1mm rows of data that violates the corresponding rule, and so might be
incomplete if the corresponding rule's violation count (as shown in the summary) exceeds 1mm.

## Generating a validation report

The basic syntax for generating a validation report is:
```
validate [-m specification.json | -d dictionary.json] [data.csv] [-o output.xlsx]
```

Additional options and details can be viewed by running `help validate`.

## Running the validation command

To generate the validation report in this demo, we need two things:

1. The data we want to run on. We'll use
   [loans.csv](https://raw.githubusercontent.com/liquidaty/zsvhub-cli/main/demos/validation/loans.csv)

1. A validation specification that defines our validation rules. We can either create validations ourselves, or find
validations that someone else has created. Let's walk through each of these approaches.

## Validation rules

`validate` supports two forms of validations:
1. validations generated from a <i>dictionary</i> or <i>column list</i>
2. validations defined in set of formulaic rules and referred to herein as a <i>validation set</i>

## Creating validations by generating a dictionary

The easiest way to start creating validations is to let `desc` auto-generate something from your data,
which you can then refine.

### Global transformations

Few idioms are as true as "garbage in, garbage out". So before we try to auto-generate anything,
let's take a quick with `desc` to see some examples of our data:

Running the below:
```
desc loans.csv
```

will output a row of information for each of the 80+ columns, and from this we can quickly see
some invalid values including "00/00/0000" and "NA" in columns such as First Rate Change Date and
Prepayment Penalty Description.

For most purposes, these would be considered "garbage", and the "00/00/0000" values will certainly
create issues if we wanted to import our First Rate Change Date into a properly-designed database
table which would typically designate that column to be a "date" data type.

To see the rows that contain this value,
if we knew exactly which column(s) could contain these values, we could run a simple SQL query to
view those values more closely. But, at this point since we only looked at up to five
examples per column, we don't know for certain that we have identified all of the
columns that could contain these values, so a better way would just be to search for any row that
contains any of those values in any cell.

```
select -s "00/00/0000" loans.csv
```

shows 1,984 rows, which out of our 4,088 rows (`count loans.csv`) is a pretty decent chunk
even if we assume 100% overlap between our two searches.

(On a unix-like system, we could have just as easily done this with `grep`, but in certain cases, such
as if a cell value may contain a comma, a cell-value search can be useful where grep fails)

For the purpose of deriving a proper dictionary (with datatypes), we'll need to
remove these "garbage" values, which we can do with
a global transformation via `transform` like so. While we're at it, let's also do so for 'NA':

```
transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv
```

The formula syntax is similar to that of SQL, with various functions as described
at [https://github.com/liquidaty/zsvhub/#formulas-and-functions](https://github.com/liquidaty/zsvhub/#formulas-and-functions),
and with a couple of overarching concepts:

* columns names are referenced as either `*.[<i>Column Name</i>]` or,
  when using a dictionary (more on this later), `[<i>Dictionary column name</i>]`

* the "current value", which, in the case of a global transformation, is the text
  of the cell currently being processed, is referenced as `[]`

* either single-quotes or double-quotes can be used in formulas to specify text values,
and backslash can be used to escape embedded double-quotes.

Using this as a pre-filter will help our auto-generated validation tests,
to more accurately guess proper data types and other validation criteria.

### Using default values to create a dictionary and validation report

In addition to using `transform` to pre-filter, we will use
`desc+ --dictionary` to auto-generate a dictionary,
and use `validate` to generate a validation report.

Let's first see what we get "out of the box" with no further options:

```
# using the desktop ZSV extension
zsv zh-transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv | zsv zh-desc+ --dictionary | zsv zh-validate loans.csv

# or, on https://zsvhub.com/playground/

transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv -o loans_clean.csv

desc+ --dictionary loans_clean.csv -o loans_dictionary.json

validate loans.csv -d loans_dictionary.json
```

which prints the following CSV table:
|Validation                                                                                                   |Count|
|-------------------------------------------------------------------------------------------------------------|-----|
|Prepayment Penalty Description value not in { '4per', '1per80%', '6moint', '5per90%', <24 more>, '2mo80pct' }|863  |
|First Rate Change Date is not a date                                                                         |1,984|
|First Payment Change Date is not a date                                                                      |1,984|
|Next Rate Change Date is not a date                                                                          |1,984|
|Next Payment Change Date is not a date                                                                       |1,984|

No surprise that the date errors match the number of rows we already saw with the '00/00/0000' garbage data--
but wait, you say, how did those show up on our report
if we transformed them to blanks?

The answer is, we only transformed the data for the purpose of generating the validation rules via `desc+ --dictionary`.
The rules were then passed to `validate`, which applied them to the original data set-- hence the presence of the
"loans.csv" argument in the `validate` command.

(Note: at this point, if we wanted to see a detailed breakout of each, we could generate an xlsx workbook 
by adding `-o breakouts.xlsx` to the above command, and then either opening the saved xlsx file with a spreadsheet program,
or convert its contents to csv using `2csv`. We will explore this capability in more detail below)

### Viewing all validation checks (not only those with non-zero results)

So now we've seen some validation errors, but what about validation rules that were checked, but had no errors?
We can see that by adding
the `--all` flag:
```
zsv transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv | zsv desc+ --dictionary | zsv validate <b>--all</b> loans.csv

# or, on https://zsvhub.com/playground/, continuing from the prior step

validate <b>--all</b> loans.csv -d loans_dictionary.json

```

which yields 221 auto-generated validation rules, showing those with zero as well as non-zero results:

|Validation|Count|
|--|--|
|Loan Id is blank|0|
|Loan Id is not a number|0|
|Loan Id not between 7,188,558 and 7,220,374|0|
|As Of Date is blank|0|
|As Of Date is not a date|0|
|Original Balance is blank|0|
|Original Balance is not a number|0|
|Original Balance not between 40,000 and 500,000|0|
|Principal Balance is blank|0|
|Principal Balance is not a number|0|
|Principal Balance not between 16,052.79 and 500,000|0|
|... another ~80 rows of results ... |... ~ ... |
|State not in { 'me', 'tx', 'oh', 'ut', <47 more>, 'hi' }|0|
|... another ~40 rows of results ... |... ~ ... |
|First Rate Change Date is not a date|1,984|
|... ~ ... |... ~ ... |
|Rate Adjustment Period is blank|0|
|Rate Adjustment Period is not a number|0|
|... another ~40 rows of results... |... ~ ... |

### Modifying how enumeration validation checks are generated

Enumeration validation checks are generated by tracking number of distinct values in a column up to
a predetermined threshold number. If that number is not exceeded, and the column datatype is
determined to be integer or text, an enumeration check is created.

You can change the threshold applied to all columns by using the `--max-distinct` option,
and you can remove the limit entirely for a specific column with the `--distinct` option, which
can be used more than once to apply to more than one column.

For example, adding the options `--max-enum 10 --enum State` will prevent enumerations checks
from being generated on anything except the State column, or columns with no more than 10 distinct values:
```
zsv transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv \
| zsv desc+ --dictionary --max-enum 10 --enum State \
| zsv validate <b>--all</b> loans.csv

# or in the playground (continuing from above):

desc+ --dictionary --max-enum 10 --enum State loans_clean.csv -o loans_dictionary.json

validate --all loans.csv -d loans_dictionary.json

```

### Specifying a row identifier

You may have noticed that one of the validation rules was called 'Loan Id duplicate',
but the duplicate check was only applied to that one column (Loan Id). 

The reason for this is that, when we generated the dictionary,
`desc+` assumed that the first column in the data set is the asset id.
In this case, that assumption was correct (Loan Id indeed is our row ID);
if we wanted, we could specify a different ID column by using the `--asset-id` option.

For illustrative purposes, let's go ahead and specify the id column anyway.
Our cumulative dictionary generation command is now:

```
desc+ --dictionary --asset-id 'Loan Id' --max-enum 10 --enum State loans_clean.csv
```

Upon running, we can see in the output that <i>"asset_id": true</i>
is in the "Loan Id" property of the output JSON.

### Modifying how range validation checks are generated

By default, `zsv desc+ --dictionary` will generate numeric and date range limits
that allow for the actual ranges it observed in the reference data. You can
change this to use a standard
deviation multiple to either widen or narrow the generated
ranges by using `zsv desc+ --dictionary` with the
`--dictionary-validation-stdev` option.
For example, if we wanted the numerical ranges to be, at most, 2.5 standard
deviations
from the mean, we would use `--dictionary-validation-stdev -2.5`,
and if we wanted the ranges to be at *least* 2.5 standard deviations from the mean,
we would use `--dictionary-validation-stdev +2.5`.

Let's re-run our first validation, but with more restrictive ranges limited to
2.5 standard deviations from the mean:
```
transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv | zsv desc+ --dictionary <b>--dictionary-validation-stdev -2.5</b> | zsv validate loans.csv

# or on https://zsvhub.com/playground/

zsv desc+ --dictionary --dictionary-validation-stdev -2.5 loans_clean.csv -o loans_dictionary_stdev.json

zsv validate loans.csv -d loans_dictionary_stdev.json

```

Now, we get a lot more validation results-- 45 rows vs about half a dozen:

|Validation|Count|
|--|--|
|Original Balance not between 40,000 and 331,334.55|110|
|Principal Balance not between 16,052.79 and 328,074.47|111|
|Actual Balance not between 16,052.79 and 328,187.79|111|
|Scheduled Balance not between 16,052.79 and 328,074.47|111|
|... another ~35 rows of results... |... ~ ... |
|Next Payment Change Date not between 2007-08-03 and 2010-02-05|2,006|
|Rate Adjustment Period not between 0 and 10|3|
|Payment Adjustment Period not between 0 and 10|3|

### Saving and editing generated and custom dictionary validations

At this point, while you may have a sense of the validation checks that
can be automatically generated
to do basic things like checking data types and ranges, there are still
two major gaps in our validation capabilities:

* The generated validations are usually a good start,
  and usually need refinement for production use
* In addition to validations that you can generate from `desc+`,
  it is often necessary to run other types of checks, such as verifying
  internal consistency between several columns of data

To address these needs, we can save the generated validations
and then customize them with a text editor

#### Saving the generated dictionary

To save the generated dictionary, along with its validations, we just run the
`desc+ --dictionary ...` command, which outputs the dictionary specification
in JSON, by itself, without piping its output into `validate`.

For example, to save our dictionary, with all the various options
discussed above, to "loans_dictionary_final.json",
we can run `desc+` and use the `-o` option (or if you're using desktop CLI,
use a standard `>` redirect) as follows:

```
transform --global-transform "if([] = 'NA' or [] = '00/00/0000', '', [])" loans.csv | zsv desc+ --dictionary --asset-id 'Loan Id' --dictionary-validation-stdev -2.5 --max-enum 10 --enum State > loans_dictionary_final.json

# in the playground, as we've already seen above, use the -o flag
desc+ loans_clean.csv --dictionary --asset-id 'Loan Id' --dictionary-validation-stdev -2.5 --max-enum 10 --enum State -o loans_dictionary_final.json

```

We can then edit "loans_dictionary_final.json" and use it to generate validations for our original csv file, or for another csv file in the same format (later,
we will see how to reuse this even for other CSV files in different formats).

Let's look at the "First Payment Date" entry:
```
  {
    "definition": "*.[First Dayment Date]",
    "datatype": "D",
    "validation": {
      "required": 1,
      "range": [
        "2004-05-01",
        "2007-02-01"
      ]
    }
  },
```

We can change the validation behavior by changing the values of `datatype` or,
in the `validation` property, `required` or `range`, or `limit_to_list`.

Note that date range values are strings in yyyy-mm-dd format,
data types are single-character strings--
(N)umber, (D)ate, (B)oolean, (T)ext or (S)tring (T and S are equivalent--
and boolean values such as
"required" can be either strict booleans such as `true` and `false` or
integer values `1` or `0`.

#### Custom validation formulas

Hopefully, by now you have a bit of an idea as to how to edit your dictionary
to generate basic data type, range and non-blank checks.

However, you may also need to run checks that are more complex. For this reason,
`validate` supports the following additional
validation checks (these and other details are formally defined in the dictionary specification available at
[dictionary schema](https://github.com/liquidaty/zsvhub-cli/main/schemas/dictionary-definition.json) ):

* required_when: a formulaic condition defining when this column should be non-blank
  - For example, to require a Zip Code if State is Blank, we could add to the
    Zip Code validation: 
```
"required_when": "[State] = null"
```
    (note that we can use either the dictionary column `[State]`
    or the raw column `*.[State]`)

* blank_when: a formulaic condition defining when this column should blank
  - For example, to require Next Rate Change Date
    to be blank when Arm/Fixed Flag is not 'arm',
    we could add to the Next Rate Change Date validation:
```
"blank_when": "[Arm/Fixed Flag] != 'arm'"
```
  - Note: it is generally better to convert typical "flag" columns to boolean
    values so that we can apply the same validation logic to a multitude of
    input formats (e.g. "arm" vs "fixed", "true" vs "false", "yes" vs "no", etc).
    We demonstrate how to do this in the [domains](../domains/README.md) tutorial

* formula: a formulaic condition defining when this column value is considered valid.
  When the formula is evaluated,
  the current column value can be referenced with the special empty column name `[]`,
  and any column can be referenced from its dictionary name
  (e.g. `[Orig. Sched. P&I]` or `[State]`).

  - For example, to require that the original mortgage payment be within
    1% of the calculated monthly interest, in the case of an interest-only loan,
    or level-payment amount, in the case of other loans, we could add to the
    Orig. Sched. P&I validation:
```
"formula": "abs(1 - []/if([Interest Only Term] > 0, [Original Rate]/1200 * [Original Balance], pmt([Original Rate]/1200,[Original Amortization Term],[Original Balance]))) < 0.01"
```

(For more detail using functions and formulas, refer to the [formula documentation]
(https://github.com/liquidaty/zsvhub-cli/main/doc/formula.md))

After making these changes, when we run the validation, we can run:

```
validate loans.csv -d loans_dictionary_final.json
```

and see the following new validations:

* Zip Code not blank and [State]=NULL (0 results)
* Next Rate Change Date blank and [Arm/Fixed Flag] != "arm" (1,984 results)
* Orig. Sched. P&I invalid: not(abs(...)) (35 results)

### Generating a breakout report in XLSX or XLSM format

So far we have only looked at validation results in aggregate, i.e. for each validation
rule, the total number of exceptions. The next logical step is, of course, to review
any rule's exceptions in detail.

The screenshots at the top if this page were taken from a macro-enabled workbook
generated by `validate`. The workbook format offers a number of features:

- a summary worksheet with a table containing, for each validation that was checked, its name and the number of assets failing the check
- a data worksheet containing, for each row of data:
  - each column in the dictionary (or alternatively, in a specified export [to do: add https://github.com/liquidaty/zsvhub-cli/main/doc/export.md],
   but that, along with various other features when using dictionaries, is a separate topic)
  - a column for each validation, with a TRUE/FALSE value indicating whether that validation passed or failed
  - each column in the original raw data
- for each validation rule with at least one violation, a break-out tab that contains asset-level data filtered to only include those rows
  that violated the rule, and only those columns that were used in the rule calculation,
  both in dictionary and raw form
- Option to save as macro-enabled workbook (XLSM), which will include built-in VBA macros to add colors and borders to data tabs, and to optionally
  add a tab with a table containing, for each validation violation, the related asset id and validation rule

To save our validation results in a workbook, we can just use the `-o` option with an xlsx file name:

```
validate loans.csv -d loans_dictionary_final.json -o x.xlsx

# or, to save a macro-enabled workbook:
validate loans.csv -d loans_dictionary_final.json -o x.xlsm
```

Notes:
* you don't need Excel to view the XLSX or XLSM contents-- just use `2csv` (run `2csv help` for details)
* the macro-enabled workbook uses our own [VBA module](https://github.com/liquidaty/zsvhub-cli/main/demos/validation/vxProject.bin),
or we could also substitute in own VBA module using the `--vba` option)

<!--
## Creating validation sets outside of the dictionary

Coming soon! You can also create validation sets outside of the dictionary,
either as a generated set of rules based on an
[export](../../docs/export.md)
or a [standalone validation set](VALIDATION_SET.md),
each of which is either a rule or a reference to another generated set of
validations or another validation set.

Coming soon! Validation sets each belong to a domain, which is a collection of objects which can all be
dynamically referenced in formulas.
For more details, see the validation set documentation [ to do: https://github.com/liquidaty/zsvhub-cli/main/docs/validation_set.md].

## Coming soon! Automatically generate a validation report using a predefined dictionary or validation set
If a [domain already exists](https://github.com/liquidaty/zsvhub/blob/main/demos/domains/README.md) for our data on zsvhub.com,
this is a super simple solution. We just map our data and generate a report-- for example:
-->
