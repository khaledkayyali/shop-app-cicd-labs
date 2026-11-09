# Lab 11: How much of the code do the tests run?

**Session 5 · 25 minutes · project: shop-app**

**Goal:** measure test coverage, make CI fail when coverage drops under 90%, and close the gap with new tests.

**Words you will need:** coverage · pytest-cov · virtual environment · `--cov-fail-under` · job summary

## Step 1: A virtual environment (setup, type exactly)

Today you run the tools on your own computer too. A virtual environment keeps the project's tools inside the project folder, so they never clash with other projects.

```bash
cd ~/shop-app
git switch main
git pull
sudo apt install -y python3-venv     # Ubuntu or WSL only, once
python3 -m venv .venv
source .venv/bin/activate
```

Your prompt now starts with `(.venv)`. Every time you open a new terminal for shop-app, run `source .venv/bin/activate` again.

Add these lines to `.gitignore`, so Git never commits the environment or the coverage files:

```text
.venv/
.coverage
coverage.md
coverage.xml
```

## Step 2: Measure coverage

1. Create a branch called `ci/coverage`.
2. Add `pytest-cov` as a new line in `requirements.txt`, then install everything:

```bash
pip install -r requirements.txt
```

3. Run the tests with coverage:

```bash
pytest --cov=pricing --cov-report=term-missing
```

**Question 21:** what percentage did you get? What does the `Missing` column show, and what would it mean if it showed `28-32`?

## Step 3: Coverage in CI

In `.github/workflows/ci.yml`, change the **Run tests** step of the `test` job so that it:

1. measures coverage of `pricing`
2. shows the missing lines
3. also writes a Markdown report to `coverage.md`
4. **fails** if coverage is under 90%

*Hint: run `pytest --help` and search for `--cov-report` and `--cov-fail-under`. The Markdown report option is `--cov-report=markdown:coverage.md`.*

Then add this step at the end of the `test` job (type exactly). It shows the report on the run's summary page, even when the tests fail:

```yaml
      - name: Coverage summary
        if: always()
        run: cat coverage.md >> "$GITHUB_STEP_SUMMARY"
```

Commit everything, push, and open a pull request.

**Check:** every check is green. Open the run and scroll to the **Summary**: you see the coverage table. Merge the pull request, then switch to `main` and pull.

## Step 4: New code, no tests

1. Create a branch called `feature/shipping`.
2. Add this function at the end of `pricing.py` (copy exactly):

```python
FREE_SHIPPING_MIN = 200


def shipping_cost(total):
    """Return the shipping cost in ILS. Free from 200 ILS."""
    if total >= FREE_SHIPPING_MIN:
        return 0
    if total >= 100:
        return 15
    return 30
```

3. Run the coverage command from Step 2 on your computer, then commit, push, and open a pull request.

**Question 22:** which job failed on the pull request, even though every test passed? What coverage did you get, and which lines were missing?

## Step 5: Close the gap

1. Create `tests/test_shipping.py` with **one** test only: shipping is free at 200 ILS. Run coverage locally. Is it enough?
2. Add tests for the other two cases until coverage is back above 90%.
3. Push to the same branch. When everything is green, merge.

**Question 23:** you reached 100%. Does that mean `shipping_cost` has no bugs? Give an example of a bug that 100% coverage would not catch.

## Done when

`main` is green, the summary page shows the coverage table, and you can answer Questions 21 to 23.

---

Next: [Lab 12](lab-12-ruff-and-pre-commit.md) · [All labs](../README.md)
