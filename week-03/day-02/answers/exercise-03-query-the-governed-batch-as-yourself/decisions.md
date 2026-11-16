# Exercise 3: decisions

For the trainer. Exercise 3, "Predict, then query the governed batch as yourself", follows the demo where the trainer adds a grant, a mask and a filter and runs one query as a reviewer. Learners configure nothing: the program owner kept a 15-minute read-only exercise on 2026-11-13 ("Keep it"). Modelled on Microsoft Learn's exercise "Securing Data in Unity Catalog" (https://microsoftlearning.github.io/mslearn-databricks/Instructions/Exercises/UC-03-Secure.html, about 45 minutes; opened 2026-11-13), its confirm step only: "Confirm you can only view 29,859 rows (where c_nationkey = 21)." Shape: "Predict, then run and compare". The check is `check.py` in the exercise folder.

## 1. The prediction

- **The answer (kept).** "Tax year 2023 only, 10 rows, and tin as a pattern of stars."
- **Reason.** Step 1 says what the filter keeps (tax year 2023 for the learners' group) and what the mask shows (stars), and gives the trainer's rows per year (2023: 10). The prediction is the test: cell 2 then shows whether the controls do what step 1 says.
- **What else the check accepts.** Any prediction of 5 words or more. The debrief compares it with what cell 2 showed.

## 2. What the learner sees

- **The answer.** Tax year 2023 only, 10 rows; `tin` as `***-**-****`. The trainer, a reviewer, sees 2019: 2, 2021: 8, 2022: 12, 2023: 10, and every number.
- **Reason.** The filter function `course.returns.learner_year_filter` returns true for the reviewer group and `tax_year = 2023` for everyone else; the mask `course.returns.tin_mask` returns the number to the reviewer group and `***-**-****` to everyone else (`week-03/setup/day-02-course-data.py`, `governed_statements`). The form follows Databricks, "Manually apply row filters and column masks" (https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks/manually-apply), opened 2026-11-13: `RETURN IF(IS_ACCOUNT_GROUP_MEMBER('admin'), true, region='US')` and `ALTER TABLE users ALTER COLUMN ssn SET MASK ssn_mask`.
- **The notebook lines.** "Row filters and column masks are Unity Catalog access controls that restrict the rows and column values a user can see at query time." "Rows where the function returns `FALSE` are excluded from query results." A column mask "takes the column value as input and returns the original value or a masked version."
- **What else the check accepts.** It queries the table again as the learner: `rows_per_year` must equal each year and count, the year typed as a number or as text; `identifier_seen` must be a value of `tin` the learner sees.

## 3. The grants

- **The answer.** "The learners' group holds SELECT twice: on the table itself, and on the schema course.returns, which the table inherits." Monday's cell 5 grants `SELECT` on the schema, and Tuesday's cell 5 on each batch, so `SHOW GRANTS ON TABLE` lists both rows: `TABLE course.returns.batch_governed` and `SCHEMA course.returns`.
- **Reason.** `SHOW GRANTS` runs for a learner: on 2026-11-13 the course's test learner, a member of `training-students` who owns nothing in `course`, ran cell 3 in the class workspace and got the two rows above (`Principal`, `ActionType`, `ObjectType`, `ObjectKey`). Databricks' page (https://docs.databricks.com/aws/en/sql/language-manual/security-show-grant) names the owner or `MANAGE`; cell 3 keeps the fallback, `course.information_schema.table_privileges`, for a workspace that refuses it: "the `INFORMATION_SCHEMA` only shows users their own grants on the object" (https://docs.databricks.com/aws/en/sql/language-manual/information-schema/table_privileges).
- **What else the check accepts.** Any text of 3 words or more that names `SELECT`.

## 4. Why it differs, or the fallback

- **The answer.** "The row filter shows my group tax year 2023 only, and the mask shows my group a pattern in place of tin."
- **The fallback (no mask or filter).** Every year and every number. Kept in `values-no-mask.json`: "The learners' group could read every identification number: its SELECT on the table and on the schema reaches every row and every number, more than need to know." The notebook line: "Restrict access to SBU data (including PII and FTI) to those IRS personnel who have a need for the information in the performance of their duties."
- **What else the check accepts.** `why_differs` of 8 words or more where the learner sees less than the batch holds; else `fallback` of 10 words or more. The check finds which by what the table gives the learner; cell 4 prints the same choice for the learner before step 4.

## Where it ran

- On the author's computer, 2026-11-13, without Databricks: `check.py` read the setup's rows in SQLite, as the learners' group sees them and, with `--plain`, as a workspace with no mask or filter. Start: `Not yet: 5 left.` No mask: `Done: you see 2019: 2, 2021: 8, 2022: 12, 2023: 10; the identifier as 000-11-4037.` Answer: `Done: you see 2023: 10; the identifier as ***-**-****.` The statements were parsed by sqlglot 30.31.0, dialect `databricks`; it reads `ALTER TABLE ... SET ROW FILTER`, `SET MASK` and `SHOW GRANTS` only as commands, so those three rest on the Databricks pages above.
- In the class workspace, 2026-11-13, on Serverless, with the setup run in the catalog `course` and the course's test learner (a member of `training-students`, outside `training-instructors`): the filter and the mask held (2023: 10; `***-**-****`), `SHOW GRANTS ON TABLE` listed the class group's `SELECT` on the table and on the schema, the start printed `Not yet:` and the answer `Done:`.
- Trainer gap, at the run-through: the trainer, a member of `training-instructors`, sees every year and every number in cell 2.

## Where each quote comes from

`days/day-02/curated.md` in the Program repository, under "Enforce access with grants, column masks and row filters" unless named:

| Quote | Line |
|---|---|
| "So a filter is tested like any other code: ..." | 369 |
| "Row filters and column masks are Unity Catalog access controls ..." | 350 |
| "Rows where the function returns `FALSE` ..."; "takes the column value as input ..." | 353 to 354 |
| "to run a SELECT query on a table, ..." | 335 |
| "Privileges granted on a catalog automatically apply ..." | 337 |
| "Restrict access to SBU data ..." | Decide who may read which data, line 307 |

No Exercise slide in the deck names this exercise: the deck has a demo (slide 56) and a discussion (slide 59) for access control.
