# Lab 7: Your first workflow

**Session 4 · 15 minutes · project: shop-app**

**Goal:** create a workflow, run it twice (by a push and by a button), read its logs, and break it on purpose.

**Words you will need:** workflow · event · job · step · runner · `uses` · `run`

## Step 1: Make the folder (type exactly)

GitHub only looks for workflows in one place: `.github/workflows/` at the top of the repository.

```bash
cd ~/shop-app
git switch main
git pull
mkdir -p .github/workflows
```

## Step 2: Write the workflow (type exactly)

Create the file `.github/workflows/hello.yml` with this content. Type it yourself instead of copying, so your fingers learn the indentation. Two spaces per level, no tabs.

```yaml
name: Hello

on:
  push:
  workflow_dispatch:

jobs:
  say-hello:
    runs-on: ubuntu-latest
    steps:
      - name: Say hello
        run: echo "Hello from GitHub Actions!"

      - name: Show some context
        run: |
          echo "Repository: ${{ github.repository }}"
          echo "Branch:     ${{ github.ref_name }}"
          echo "Started by: ${{ github.actor }}"
          echo "Event:      ${{ github.event_name }}"

      - name: Files before checkout
        run: ls -la

      - name: Check out the code
        uses: actions/checkout@v5

      - name: Files after checkout
        run: ls -la
```

## Step 3: Push and watch

1. Commit the file with the message `Add hello workflow` and push it.
2. On GitHub, open your repository and click the **Actions** tab.
3. Click the run called **Add hello workflow**, then the job **say-hello**.
4. Click each step to open its log.

**Check:** a green tick next to your commit on the repository's main page.

**Question 12:** what did `ls -la` show in **Files before checkout**, and what did it show after? Why does almost every workflow start with `actions/checkout`?

## Step 4: Run it by hand

In the **Actions** tab, click **Hello** in the left list, then **Run workflow**, choose `main`, and click the green button. Open the new run.

**Question 13:** what did the line `Event:` show in this run, and what did it show in Step 3? Which line of the file allows the button?

## Step 5: Break it on purpose

1. Add one more step at the end of the file, with the name `Oops` and the command `ech "this will fail"` (the missing `o` is on purpose).
2. Commit and push.
3. Open the run. Find the red step and read the error message in the log.
4. Fix the typo, commit, push, and check that the run is green again.

**Bonus:** remove two spaces before `runs-on:` and push. What does GitHub show this time, and how is it different from a failed step? Put the spaces back.

## Done when

The last run of **Hello** is green, and you can answer Questions 12 and 13.

---

Next: [Lab 8](lab-08-tests-that-run-themselves.md) · [All labs](../README.md)
