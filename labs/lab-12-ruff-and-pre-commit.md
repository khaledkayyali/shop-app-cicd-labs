# Lab 12: Clean code, automatically

**Session 5 · 25 minutes · project: shop-app**

**Goal:** replace flake8 with Ruff, let it fix style problems for you, and stop badly formatted code before it even becomes a commit.

**Words you will need:** linter · formatter · Ruff · auto-fix · pre-commit hook

Work on a branch called `ci/ruff`, with your virtual environment active (`source .venv/bin/activate`).

## Step 1: Install and configure Ruff

1. In `requirements.txt`, replace `flake8` with `ruff`. Install again with `pip install -r requirements.txt`.
2. Create `ruff.toml` at the top of the repository (copy exactly):

```toml
line-length = 88

[lint]
# E/W: style, F: real mistakes, I: import order, B: likely bugs
select = ["E", "W", "F", "I", "B"]
```

## Step 2: Practise on a messy file

Create `messy.py` (copy exactly, mistakes included):

```python
import sys
from pricing import cart_total, apply_discount
import os


def summary(prices,discount = 10):
    unused = "nobody uses me"
    total=cart_total(prices)
    return apply_discount(total,discount)
```

1. Run the linter: `ruff check messy.py`. Read each message: it has a code (like `F401`) and a line number.
2. Let Ruff fix what it can: `ruff check --fix messy.py`. Run the check again: what is left?
3. Fix the last problem by hand.
4. Check the formatting: `ruff format --check messy.py`. Then format it: `ruff format messy.py`, and look at the file.
5. Delete `messy.py`.

**Question 24:** which problem could Ruff not fix by itself, and why do you think it refused? What is the difference between `ruff check` and `ruff format`?

## Step 3: Clean your real code

Run `ruff check .` and `ruff format --check .` on the whole repository. Fix everything they find (use `--fix` and `ruff format .`), and check that the tests still pass.

## Step 4: Ruff in CI

Change the `lint` job in `ci.yml` to use Ruff instead of flake8. It needs two checks: the linter, and the formatting check that **fails** if a file is not formatted.

*Hint: install `ruff`, then two `run` steps. Keep the job name `lint`, so your branch protection rule from Lab 9 still finds it.*

Commit, push, open a pull request, and merge it when every check is green. Switch to `main` and pull.

## Step 5: A robot before every commit

CI checks your code after you push. A pre-commit hook checks it **before** the commit is created.

1. Install the tool: `pip install pre-commit`
2. Create `.pre-commit-config.yaml` (copy exactly):

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.17.4
    hooks:
      - id: ruff-check
        args: [--fix]
      - id: ruff-format
```

3. Turn it on for this repository: `pre-commit install`
4. Create a branch called `chore/pre-commit`. In `pricing.py`, change `VAT_RATE = 0.18` to `VAT_RATE=0.18` (remove the spaces). Stage everything and commit.
5. Read what happens. Run `git status` and `git diff`. Then stage again and commit again.
6. Push, open a pull request, and merge it.

**Question 25:** what happened at your first commit? If every student has this hook, why do we still need the `lint` job in CI?

## Done when

CI uses Ruff, the hook runs on every commit, and you can answer Questions 24 and 25.

---

Previous: [Lab 11](lab-11-coverage.md) · Next: [Lab 13](lab-13-security-checks.md) · [All labs](../README.md)
