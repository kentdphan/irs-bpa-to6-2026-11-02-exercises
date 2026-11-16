# Week 3, day 4: reach Databricks from the Workstation's terminal

Three exercises, in order. Each runs in your Google Workstation's terminal, in your course folder: run `git pull` first, then go into the exercise's folder. You use the Databricks CLI, and in exercise 3 Codex. Each exercise uses the profile `course`, which you save before class ("Before Thursday", below) and check in exercise 1. Exercise 2 runs Wednesday's notebook. Exercise 3 reads the tables it writes. The course made up every row. The trainer prepares the day with [`../setup/day-04-setup.md`](../setup/day-04-setup.md).

1. [A tour of the Databricks CLI](exercise-01-cli-tour/README.md). What you do: check your Databricks CLI sign-in and read each command's output and exit status. Where you work: the terminal, in `exercise-01-cli-tour/`, with `notes.md`. Done when: `python3 check.py` prints `Done:`. [Start](exercise-01-cli-tour/) · [Answer](answers/exercise-01-cli-tour/)
2. [Run Wednesday's notebook as a one-off job](exercise-02-run-wednesday-as-a-job/README.md). What you do: run Wednesday's notebook as a one-off job from the terminal and read its final state. Where you work: the terminal, in `exercise-02-run-wednesday-as-a-job/`, with `job.json` and `notes.md`, and the SQL editor for one count. Done when: `python3 check.py` prints `Done:`. [Start](exercise-02-run-wednesday-as-a-job/) · [Answer](answers/exercise-02-run-wednesday-as-a-job/)
3. [Codex checks your three sets, and you approve each command](exercise-03-codex-checks-the-sets/README.md). What you do: judge each Databricks command Codex proposes before it runs, and check its answer in Catalog Explorer. Where you work: Codex in the terminal, in `exercise-03-codex-checks-the-sets/`, with `notes.md`, and Catalog Explorer. Done when: `python3 check.py` prints `Done:`. [Start](exercise-03-codex-checks-the-sets/) · [Answer](answers/exercise-03-codex-checks-the-sets/)

## Before Thursday: the CLI and your profile

Do this once, on your Workstation, before class. The exercises start from it. The CLI goes in your home folder, which a stop of the Workstation keeps.

1. Install the Databricks CLI. In a terminal:

   Terminal:

   ```text
   mkdir -p ~/.local/bin
   curl -fsSL -o /tmp/databricks.zip https://github.com/databricks/cli/releases/download/v1.25.0/databricks_cli_1.25.0_linux_amd64.zip
   python3 -c "import os, zipfile; zipfile.ZipFile('/tmp/databricks.zip').extract('databricks', os.path.expanduser('~/.local/bin'))"
   chmod +x ~/.local/bin/databricks
   grep -q '.local/bin' ~/.bashrc || echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
   ```

   Open a new terminal (**Terminal**, then **New Terminal**). `databricks -v` should print `Databricks CLI v1.25.0`.

2. Sign in, and save the sign-in as the profile `course`. Put the workspace address the trainer sent in place of `<workspace-url>`:

   Terminal:

   ```text
   databricks auth login --host <workspace-url> --profile course
   ```

   The Workstation asks whether to open an external website. Select **Copy**, not **Open**: Open changes the address, and the sign-in then fails with `state mismatch`. Paste the address into a new browser tab and go. Sign in as yourself if the workspace asks. It shows **Authorize as** with your name, and continues by itself.

   The browser then goes to an address that starts with `http://localhost:8020` and says the site cannot be reached: the CLI waits for it on the Workstation, not on your computer. Copy that whole address from the address bar. Open a second terminal and run `curl` with the address in quotes:

   Terminal:

   ```text
   curl "<the address you copied>"
   ```

   The first terminal then says `Profile course was successfully saved`.

3. Check it: `databricks auth profiles` shows `course` with `YES` under `Valid`.
