# Lab 8: Tests that run themselves

**Session 4 · 25 minutes · project: shop-app**

**Goal:** add real code and tests to shop-app, make GitHub run the tests on every push and every pull request, and watch a red check catch a bug before it reaches `main`.

**Words you will need:** CI · pytest · `actions/setup-python` · `pull_request` · `pip install -r`

## Step 1: Add the code (copy exactly)

Today is about automation, not Python, so the code is given. Create these four files in `~/shop-app`.

`pricing.py`

```python
"""Price helpers for Shop App. All prices are in ILS."""

VAT_RATE = 0.18


def apply_discount(price, percent):
    """Return the price after a percent discount."""
    if percent < 0 or percent > 100:
        raise ValueError("percent must be between 0 and 100")
    return round(price * (100 - percent) / 100, 2)


def add_vat(price, rate=VAT_RATE):
    """Return the price with VAT added."""
    return round(price * (1 + rate), 2)


def cart_total(prices):
    """Return the sum of all prices in the cart."""
    return round(sum(prices), 2)
```

`tests/test_pricing.py`

```python
import pytest

from pricing import add_vat, apply_discount, cart_total


def test_discount_ten_percent():
    assert apply_discount(100, 10) == 90


def test_discount_zero_percent():
    assert apply_discount(50, 0) == 50


def test_discount_rejects_bad_percent():
    with pytest.raises(ValueError):
        apply_discount(100, 150)


def test_add_vat():
    assert add_vat(100) == 118


def test_cart_total():
    assert cart_total([10, 20.5, 5]) == 35.5
```

`pytest.ini` (tells pytest where the code and the tests are)

```ini
[pytest]
pythonpath = .
testpaths = tests
```

`requirements.txt` (the tools the project needs)

```text
pytest
flake8
```

If you installed the tools, run `python3 -m pytest -v` now. You should see `5 passed`. Then commit everything with the message `Add pricing module and tests` and push.

## Step 2: Write the CI workflow (fill the blanks)

Create `.github/workflows/ci.yml`. Fill in the three blanks marked `___`.

```yaml
name: CI

on:
  push:
    branches: [main]
  ___:                 # 1. also run for pull requests into main
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-python@v6
        with:
          python-version: "3.13"
      - name: Install dependencies
        run: ___       # 2. install everything listed in requirements.txt
      - name: Run tests
        run: ___       # 3. run pytest and show each test name
```

`hello.yml` from Lab 7 runs on every push and has done its job. Delete it with Git (hint: Session 2 has a command that deletes a file and stages the deletion in one go).

Commit both changes and push. In the **Actions** tab, open the **CI** run, then the **Run tests** step.

**Check:** the log ends with `5 passed`, and **Hello** did not run this time.

## Step 3: A bug tries to sneak in

A teammate wants bigger discounts and changes the math by mistake.

1. Create and move to a branch called `feature/bigger-discount`.
2. In `pricing.py`, change `(100 - percent)` to `(100 - percent * 2)`. Commit with the message `Double the discount` and push the branch.
3. On GitHub, open a pull request from `feature/bigger-discount` into `main`.
4. Wait for the check at the bottom of the pull request. Click **Details** on the red check and read the log of **Run tests**.

**Question 14:** how many tests failed and how many passed? Which test failed, and what two numbers did pytest compare?

## Step 4: Fix it in the same pull request

1. Undo the bad commit on your branch in a safe way that keeps history (hint: Session 3, the command we use for commits that are already pushed).
2. Push the branch again. **Do not** open a new pull request.
3. Watch the pull request page: the check runs again and turns green.
4. Merge the pull request and delete the branch on GitHub.
5. Back on your computer: switch to `main` and pull.

**Question 15:** you did not open a new pull request after the fix. Why did the tests run again?

## Done when

The last **CI** run on `main` is green and the pull request shows a red run followed by a green one.

---

Previous: [Lab 7](lab-07-first-workflow.md) · Next: [Lab 9](lab-09-faster-and-stricter.md) · [All labs](../README.md)
