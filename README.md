# Shop App: course labs

Starter project and lab instructions for the DevOps course, from **Session 4** on.

- **Session 4:** shop-app gets a robot. Every push and pull request is tested, and every merge to `main` publishes a web page.
- **Session 5:** the robot gets stricter: test coverage, Ruff, security scans and a quality gate.
- **Sessions 6 and 7:** shop-app moves to AWS: storage, servers, a load balancer and Auto Scaling.

## Which repository do I work in?

**You already have `shop-app` on GitHub from Labs 1 to 6.** Keep using yours. Read the labs here, and do the work in your own repository.

**You don't have `shop-app`, or yours is broken.** Make your own copy of this one:

1. Click **Use this template** (top right), then **Create a new repository**.
2. Name it `shop-app` and choose **Public**. Pages and branch protection need a public repository on a free account.
3. Clone your copy over SSH and start Lab 7:

```bash
cd ~
git clone git@github.com:<your-username>/shop-app.git
cd shop-app
```

Do not push to this repository. Always work in your own copy.

## The labs

### Session 4: GitHub Actions and CI/CD

| Lab | Topic | Time in class |
|---|---|---|
| [Lab 7](labs/lab-07-first-workflow.md) | Your first workflow | 15 minutes |
| [Lab 8](labs/lab-08-tests-that-run-themselves.md) | Tests that run themselves | 25 minutes |
| [Lab 9](labs/lab-09-faster-and-stricter.md) | Parallel jobs, matrix, required checks | 25 minutes |
| [Lab 10](labs/lab-10-ship-it.md) | Deploy to GitHub Pages | 20 minutes, finish at home |
| [Homework 4](labs/homework-04.md) | Test-first coupons and a robot for your journal | about 2.5 hours |

### Session 5: Code Quality and Security in CI

| Lab | Topic | Time in class |
|---|---|---|
| [Lab 11](labs/lab-11-coverage.md) | Test coverage and a minimum in CI | 25 minutes |
| [Lab 12](labs/lab-12-ruff-and-pre-commit.md) | Ruff and a pre-commit hook | 25 minutes |
| [Lab 13](labs/lab-13-security-checks.md) | Bandit, pip-audit, Dependabot and CodeQL | 25 minutes |
| [Lab 14](labs/lab-14-quality-gate.md) | A quality gate with SonarQube Cloud | 15 minutes, finish at home |
| [Homework 5](labs/homework-05.md) | Branch coverage and a Markdown linter for your journal | about 2.5 hours |

### Session 6: AWS Fundamentals and Services

Use the AWS user your instructor sent you, in region **Europe (Frankfurt) eu-central-1**. Every name you create starts with your first name, and you delete everything at the end of each lab.

| Lab | Topic | Time in class |
|---|---|---|
| [Lab 15](labs/lab-15-your-aws-account.md) | Your AWS account, IAM and CloudShell | 20 minutes |
| [Lab 16](labs/lab-16-s3-storage.md) | Store files in S3 | 25 minutes |
| [Lab 17](labs/lab-17-your-first-server.md) | Your first server on EC2 | 30 minutes |
| [Homework 6](labs/homework-06.md) | The same server from the command line | about 2.5 hours |

### Session 7: AWS Infrastructure

| Lab | Topic | Time in class |
|---|---|---|
| [Lab 18](labs/lab-18-inside-the-server.md) | Inside the server: SSH, metadata, EBS, snapshots | 25 minutes |
| [Lab 19](labs/lab-19-load-balancer-and-auto-scaling.md) | A load balancer and Auto Scaling | 35 minutes |
| [Lab 20](labs/lab-20-self-healing-and-cleanup.md) | Self-healing, scaling and cleanup | 30 minutes |
| [Homework 7](labs/homework-07.md) | Update without downtime | about 2.5 hours |

## Rules

- **YAML uses spaces, never tabs.** Two spaces per level. One wrong space breaks the file.
- **The labs give less help as you go.** Lab 7 gives you the whole file. By Lab 10 you write most of it yourself.
- **Never put a real password or token in a workflow file.** Secrets live in GitHub settings, not in Git.
- **AWS: never share your password, and never create access keys.** Use the console and CloudShell.
- **AWS: clean up after every lab.** A forgotten server or load balancer costs money every hour.

## What is in this repository

| File | What it is |
|---|---|
| `app.py` | The shop app |
| `config.py` | Store name, price and currency |
| `.gitignore` | Keeps secrets, logs, the virtual environment and coverage files out of Git |
| `labs/` | The instructions for Labs 7 to 20 and Homeworks 4 to 7 |

## Submit in Google Classroom

Each lab says what to submit at its end. Questions are numbered across the course: Session 4 has Questions 12 to 20, Session 5 has 21 to 30, Session 6 has 31 to 39, and Session 7 has 40 to 48.
