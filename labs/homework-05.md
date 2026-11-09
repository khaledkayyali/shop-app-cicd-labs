# Homework 5: Raise the bar

**After Session 5 · before Session 6 · about 2.5 hours · 100 points**

In class you added quality and security checks to shop-app. Here you make them stricter, and you bring the same idea to your devops-journal.

## Part A: Finish shop-app (20 points)

Labs 11 to 14 are done. `main` requires `lint`, `test` and `security`, Dependabot and CodeQL are on, and your project is on SonarQube Cloud.

## Part B: Branch coverage (25 points)

Line coverage asks "did this line run?". Branch coverage also asks "did every `if` go **both** ways?".

1. On a branch called `ci/branch-coverage`, add `--cov-branch` to the pytest command in CI and raise the limit to `--cov-fail-under=100`.
2. Run it on your computer first. If coverage is under 100%, read the `Missing` column, write the missing tests, and run again.
3. Open a pull request and merge it when it is green.

In your answers, write which branch was missing, if any, and which test you added for it.

## Part C: A Markdown linter for your journal (30 points)

Your `devops-journal` is all Markdown, so Markdown is the code to check.

1. Add a new job called `markdown` to `journal-checks.yml` from Homework 4. It runs this command (Node.js is already installed on GitHub's runners):

```bash
npx --yes markdownlint-cli2@0.24.0 "**/*.md"
```

2. Run the same command on your computer, or push and read the log. Fix every problem it finds. Many can be fixed for you with `--fix` after `markdownlint-cli2@0.24.0`.
3. If one rule does not suit your journal, turn it off in a file called `.markdownlint.yaml`, and explain in one line why. For example, `MD013: false` turns off the line length rule.
4. Prove it works: open a pull request that adds a heading with no space after `#` (like `#Notes`). The check must go red. Fix it, push, and merge when it is green.

## Part D: Four short questions (25 points)

Answer each in 3 to 6 sentences, in your own words.

1. What does "shift left" mean? Give one example from this session for each place a problem can be caught: in your editor, at commit, on the pull request, and after merge.
2. In one sentence each, explain what these do, and name the tool we used for each: a linter, a formatter, SAST, dependency scanning.
3. A teammate's pull request adds `# nosec` to silence Bandit and lowers `--cov-fail-under` from 90 to 50, "to make CI green". Write the review comment you would leave.
4. Shop-app has 100% coverage. Give an example of a bug that could still be in it, and explain how a test could catch it.

## Submit in Google Classroom

- the link to your `shop-app` pull request for Part B
- the link to your `devops-journal` repository and to the pull request with the red and green runs
- your answers to Part D

---

Previous: [Lab 14](lab-14-quality-gate.md) · [All labs](../README.md)
