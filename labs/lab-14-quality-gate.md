# Lab 14: A quality gate with SonarQube Cloud

**Session 5 · 15 minutes in class, finish at home · project: shop-app**

**Goal:** connect shop-app to SonarQube Cloud, read its report, and see a quality gate decide on a pull request.

**Words you will need:** quality gate · code smell · bug · vulnerability · security hotspot · technical debt · new code

SonarQube Cloud is free for public repositories. It reads your code and reports bugs, security problems and code that is hard to maintain. Its **quality gate** is a pass or fail decision on every change.

## Step 1: Connect your account

1. Open **sonarcloud.io** and click **Log in**, then **GitHub**. Accept the permissions.
2. Choose **Import an organization**, and pick your GitHub account.
3. When GitHub asks where to install the SonarQube Cloud app, choose **Only select repositories** and pick `shop-app`.
4. Choose the **Free** plan and create the organization.

## Step 2: Analyze shop-app

1. Click **Analyze new project**, tick `shop-app`, and click **Set up**.
2. For **new code**, choose **Previous version**, then create the project.
3. Wait for the first analysis. It runs by itself, with no workflow file. This is called **automatic analysis**.

## Step 3: Read the report

Open your project's **Overview**.

**Question 29:** did the quality gate pass? Write down the numbers for Reliability, Security, Maintainability, Security Hotspots and Duplications. Why does Coverage show nothing, even though your CI measures it?

## Step 4: The gate on a pull request

1. Create a branch called `feature/loyalty`.
2. Add this function to `pricing.py` (copy exactly, the mistake is on purpose):

```python
def loyalty_points(total):
    """Return loyalty points for a purchase."""
    if total > 500:
        return total // 10
    elif total > 100:
        return total // 10
    return 0
```

3. Add tests for it so your coverage stays above 90%. Push and open a pull request.
4. Wait for the SonarQube Cloud check on the pull request, then open its details.

**Question 30:** what did SonarQube Cloud say about `loyalty_points`? Ruff and your tests passed: why did they not catch this?

5. Fix it: a purchase over 500 ILS gets **double** points (`total // 5`). Update the tests, push, and merge when everything is green.

## Bonus: coverage in SonarQube Cloud (advanced)

Automatic analysis cannot read coverage. To send coverage, analysis must run inside your CI:

1. In SonarQube Cloud, **Administration**, then **Analysis Method**: turn **off** automatic analysis.
2. Follow the **GitHub Actions** instructions there: add the `SONAR_TOKEN` secret, add `sonar-project.properties`, and add a job that uses `SonarSource/sonarqube-scan-action@v8`.
3. Make pytest also write `--cov-report=xml`, and tell Sonar where it is with `sonar.python.coverage.reportPaths=coverage.xml`.

## Done when

Your project is on SonarQube Cloud, a pull request showed the quality gate, and you can answer Questions 29 and 30.

## Submit in Google Classroom

The link to your `shop-app` repository, the link to your SonarQube Cloud project, and your answers to Questions 21 to 30.

---

Previous: [Lab 13](lab-13-security-checks.md) · Next: [Homework 5](homework-05.md) · [All labs](../README.md)
