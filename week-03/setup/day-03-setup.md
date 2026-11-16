# Set up Wednesday in Databricks (week 3, day 3)

For the trainer. Wednesday's four exercises run in the class's Databricks workspace. Do these steps before class, in order, after Monday's setup (`day-01-setup.md`). The course made up the return files: no file is a real taxpayer. Wednesday's setup makes the volume `course.returns.raw` with 52 return files, and one schema for each learner, `course.learner_NN`, where the learner makes tables.

## Before Wednesday

1. **Get the files.** In your Git folder of the course, pull, so that `week-03/day-03` and `week-03/setup/day-03-setup.ipynb` are there.
2. **Start the compute.** The class's notebook compute must run Databricks Runtime 14.3 or above, or be serverless: the XML reader needs it. Write down its name: learners select it in each exercise.
3. **Open the setup notebook.** Open `week-03/setup/day-03-setup.ipynb` and attach it to the compute. Cell 1's `CLASS_GROUP` is `training-students`. Each learner makes their own schema, `course.learner_NN`, when they run exercise 1's cell 1: the class group holds `CREATE SCHEMA` on `course`. So `LEARNERS` can stay empty. Fill it only to make a learner's schema yourself, with their number and Databricks user name, in the workspace only: do not commit it, as the course repository holds no learner's name or email. You need `CREATE VOLUME` on `course.returns`, and `CREATE SCHEMA` and `MANAGE` on `course`, which `training-admins` holds.
4. **Run cells 1 to 4, the day before class.** On 2026-11-13 the test learner's first read of the volume's files, minutes after cell 2's grant, failed once with `OSError: [Errno 22]` and passed when run again. Cell 3 should print these things:
   - 52 files and 52 rows
   - dependents 77, W-2s 62, tax-change cases 11
   - F-30037: 4 returns; F-30074: 1
   - adjusted gross income 0: `return-F-30111-2021.xml`
   - `IRSW2 is array | DependentDetail is array`
5. **Tell each learner their number.** Each learner types `course.learner_NN` in cell 1 of each exercise notebook. Give each learner a different number: a learner cannot write to a schema that another learner made.
6. **Check the reader path.** Week 2's reader is in your Git folder at `week-02/day-03/answers/exercise-04-a-repeated-element/files/form1040.py`. Exercise 1's test 3 imports it from there.
7. **Make your own tables for the demos.** Run the four answer notebooks, `week-03/day-03/answers/<exercise>/exercise.ipynb`, in order, with `course.learner_00` in cell 1 of each. The demo before exercise 4 runs the five tests on your `train`, `valid` and `test`, and test 5 reads the marks that only exercise 4's cell 4 writes.

## In class

- Demo before exercise 1: read the folder with `_metadata.file_name` named, explode the dependents with `return_id`, and count rows against the files. Leave the W-2s to the learners.
- Demo before exercise 2: add `tax_to_agi` and fill its record row. Show its null on `F-30111-2021`. Leave the W-2 ratio to the learners.
- Demo before exercise 3: build the window with the frame ending at -1 on `agi_amt`, and check the filer F-30037's 2023 value by hand. Exercise 3 uses `wages_amt` and adds `LAG`.
- Demo before exercise 4: split at random, find one filer in two sets, then split by year and run the five tests (exercise 4's cell 5). Use your own schema, `course.learner_00`.
- Short on time: exercise 4 becomes on-your-own work. Thursday needs `train`, `valid` and `test`, so name it at the close.
- Thursday runs `week-03/day-03/wednesday-tables.ipynb` in each learner's Git folder as a one-off job. The jobs file must name that path in the learner's own Git folder, so that the notebook finds the exercise notebooks and week 2's reader beside it.

## After class

Nothing to reset. Each learner's notebooks write with `overwrite`, so a second run replaces their tables. To write the files again, run cells 1 and 2.

## Checked in Databricks

Checked on 2026-11-13 in the class workspace, on Serverless: the setup notebook run in the catalog `course`, and each exercise run by the course's test learner, who made their own schema in exercise 1's cell 1:

1. Cell 2 writes the 52 files to the volume with Python's `open`, and cell 3 prints the numbers of step 4.
2. The XML reader names the columns `ReturnHeader`, `ReturnData` and `CourseLabel`, with `IRSW2` and `DependentDetail` as arrays. Checked again on 2026-11-16 in a new, empty catalog (Monday's setup document says how): cell 3 printed each number of step 4, and the test learner read the 52 files.
3. Exercise 1 from start: cell 3 fails with "cannot be resolved" on `file_name`, and passes once cell 2 names `_metadata.file_name`. Test 3 imports `form1040` by the path it appends.
4. Exercises 1 to 4 reach `Done:` in order with the answers' cells, with the numbers of each step: 48,000 and 0.15 for `F-30037-2023`; train 21 returns with 3 tax-change cases, valid 15 with 3, test 16 with 5.
5. `wednesday-tables.ipynb`, run as a one-off job, runs the four notebooks with `%run` and lists the eight tables; run again, `features` still holds 52 rows.

Each notebook writes its tables with `overwrite` and `overwriteSchema`: exercises 3 and 4 add and drop columns of `features` and `feature_record`, and Delta refuses an overwrite that changes a table's columns without it.
