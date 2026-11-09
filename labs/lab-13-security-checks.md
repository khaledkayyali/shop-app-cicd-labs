# Lab 13: Security checks

**Session 5 · 25 minutes · project: shop-app**

**Goal:** catch a security mistake in the code and a vulnerable package before they reach `main`, and let GitHub keep your dependencies up to date.

**Words you will need:** SAST · Bandit · pip-audit · CVE · Dependabot · CodeQL · required check

You write this lab's YAML yourself. Work on a branch called `ci/security`.

## Step 1: A security job

Add a third job called `security` to `ci.yml`, next to `lint` and `test`. It must:

1. check out the code and set up Python 3.13
2. install `bandit` and `pip-audit`
3. scan the code: `bandit -r . -x ./tests` (tests are skipped on purpose)
4. scan the packages: `pip-audit -r requirements.txt`

Then make `ready` wait for `security` too.

Try both scans on your computer first (`pip install bandit pip-audit`). On your computer, also skip the virtual environment, or Bandit scans thousands of library files: `bandit -r . -x ./tests,./.venv`. Push, open a pull request, and merge when it is green.

## Step 2: A dangerous pull request

A teammate adds an admin report and copies an old package version from a tutorial.

1. Create a branch called `feature/admin-report`.
2. Create `admin.py` (copy exactly):

```python
import subprocess

ADMIN_PASSWORD = "admin123"


def run_report(name):
    """Print a sales report for one product."""
    return subprocess.run(f"echo Report for {name}", shell=True)
```

3. Add this line to `requirements.txt`: `requests==2.19.1`
4. Commit, push, and open a pull request.

**Question 26:** the `security` job is red. List what Bandit found, with the severity of each problem. How many known vulnerabilities did pip-audit find, and which version of `requests` fixes them?

## Step 3: Fix it in the same pull request

1. The password must not be in the code. Read it from an environment variable called `ADMIN_PASSWORD` instead. *Hint: `os.environ.get`.*
2. The report does not need a shell at all. Return the text `Report for <name>` with plain Python, and remove `subprocess`.
3. Upgrade `requests` to a version with no known vulnerabilities.
4. Push to the same branch. Merge when everything is green.

## Step 4: Make security required

In **Settings**, then **Branches**, edit your rule for `main` and add `security` to the required status checks.

## Step 5: Dependabot

Dependabot opens pull requests for you when your packages or actions have a newer version.

1. Create a branch, then the file `.github/dependabot.yml` (copy exactly):

```yaml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

2. Push, open a pull request, and merge it.
3. In **Settings**, open **Code security** (on some accounts it is called **Advanced Security**). Turn on **Dependabot alerts** and **Dependabot security updates**.
4. Wait a few minutes, then open the **Pull requests** tab.

**Question 27:** which pull requests did Dependabot open, and what does each one change? Before you merge one, what must be true on that pull request?

## Step 6: CodeQL

1. In the same **Code security** settings page, find **Code scanning**, then **CodeQL analysis**, click **Set up**, choose **Default**, and enable it.
2. Open the **Actions** tab: a CodeQL run starts. When it finishes, open the **Security** tab, then **Code scanning**.

**Question 28:** today you met five security tools: Bandit, pip-audit, Dependabot, CodeQL and secret scanning. In one line each, what does each one look at?

## Done when

`main` is protected by `lint`, `test` and `security`, Dependabot and CodeQL are on, and you can answer Questions 26 to 28.

---

Previous: [Lab 12](lab-12-ruff-and-pre-commit.md) · Next: [Lab 14](lab-14-quality-gate.md) · [All labs](../README.md)
