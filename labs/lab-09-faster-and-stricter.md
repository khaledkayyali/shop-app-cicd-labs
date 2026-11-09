# Lab 9: Faster and stricter

**Session 4 · 25 minutes · project: shop-app**

**Goal:** check code style in parallel with the tests, test on three Python versions at once, add a job that waits for the others, and make GitHub refuse to merge when a check fails.

**Words you will need:** parallel jobs · `strategy` · `matrix` · `needs` · flake8 · branch protection · required status checks

From this lab on, you write the YAML yourself. Use `ci.yml` from Lab 8 as your starting point. Work on a branch called `ci/lint-and-matrix` and open a pull request into `main` when you push.

## Step 1: A lint job

flake8 is a linter: it reads Python code and complains about style problems and mistakes such as unused imports.

In `ci.yml`, add a second job called `lint`, at the same level as `test`. It must:

1. check out the code
2. set up Python 3.13
3. install flake8
4. run `flake8 pricing.py tests/`

*Hint: copy the `test` job, rename it, and change the last two steps. If you installed the tools, run `python3 -m flake8 pricing.py tests/` first. No output means no problems.*

Push and open the pull request.

**Check:** in the run's graph, `lint` and `test` start at the same time.

## Step 2: A matrix

Make the `test` job run three times: on Python `3.12`, `3.13` and `3.14`.

*Hint: add a `strategy:` with a `matrix:` under the `test` job, and give `setup-python` the value `${{ matrix.python-version }}` instead of a fixed version. Version numbers go in quotes.*

Push to the same branch.

**Check:** the pull request now shows `test (3.12)`, `test (3.13)` and `test (3.14)`.

**Question 16:** how many jobs ran in this run? Did they run one after another or at the same time? Why does a real team test on more than one version?

## Step 3: A job that waits

Add a third job called `ready`. It runs only after `lint` **and** `test` succeed, and it prints `Lint and all tests passed. Ready to merge.`

*Hint: `needs` takes a list of job names.*

Push. **Check:** in the graph, `ready` is drawn after the other jobs. When everything is green, merge the pull request, then switch to `main` and pull.

## Step 4: Make the checks required

Right now a red check is only a warning. You could still click **Merge**. Let's change that.

1. On GitHub: **Settings**, then **Branches**, then **Add classic branch protection rule** (or edit your rule for `main` if you already have one).
2. **Branch name pattern:** `main`
3. Tick **Require a pull request before merging**. Untick **Require approvals**, because you work alone and cannot approve your own pull request.
4. Tick **Require status checks to pass before merging**. In the search box, add `lint`, `test (3.12)`, `test (3.13)` and `test (3.14)`. A check only appears in the search after it has run at least once.
5. Tick **Do not allow bypassing the above settings**. Without it, you as the owner can still merge a red pull request.
6. Click **Create** (or **Save changes**).

## Step 5: Prove it

1. Create a branch called `test/lint-fail`.
2. Add the line `import os` at the very top of `pricing.py`. Commit and push, and open a pull request.
3. Read the result: which checks are red and which are green? Try to merge.
4. Fix the problem in the same branch, push, and merge when every check is green.

**Question 17:** the tests passed but GitHub still did not let you merge. Which check failed and what was its error message? Why is it good that a style problem blocks the merge?

## Done when

`main` is protected, the last **CI** run on `main` shows five green jobs, and you can answer Questions 16 and 17.

---

Previous: [Lab 8](lab-08-tests-that-run-themselves.md) · Next: [Lab 10](lab-10-ship-it.md) · [All labs](../README.md)
