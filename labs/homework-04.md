# Homework 4: Pipelines of your own

**After Session 4 · before Session 5 · about 2.5 hours · 100 points**

In class you followed the labs. Here you build pipelines with less help: one test-first feature in shop-app, and a robot that checks your devops-journal.

## Part A: Finish shop-app (20 points)

Labs 7 to 10 are done. `main` is protected, the last **CI** run on `main` is green, and your GitHub Pages site is live with your own sentence from Lab 10, Step 7.

## Part B: Coupons, test first (25 points)

In real teams you often write the test before the code. The test fails (red), then you write the code until it passes (green). This is called test-driven development (TDD).

The shop needs coupon codes. Build a function `apply_coupon(price, code)` in `pricing.py`:

- `WELCOME10` gives 10% off. `VIP20` gives 20% off.
- The code works in small letters too: `vip20` is the same as `VIP20`.
- An unknown code raises a `ValueError`.

Work on a branch called `feature/coupons`, in this order:

1. **Red first.** Create `tests/test_coupon.py` with at least three tests, one for each rule above. Do **not** touch `pricing.py` yet. Commit, push, and open a pull request. The checks must go red.
2. **Then green.** Write `apply_coupon` in `pricing.py`. Use `apply_discount` inside it instead of repeating the math. Push to the same branch until every check is green.
3. Merge the pull request.

The pull request must show at least one red run followed by a green one. That history is what you are graded on.

## Part C: A robot for your journal (30 points)

In your `devops-journal` repository, create `.github/workflows/journal-checks.yml`. Write it yourself, using the shop-app workflows as examples.

**It runs:**

1. on every push to `main`
2. on every pull request
3. by hand, with a required text input called `reason` that is printed in the log
4. automatically every Monday at 06:00 UTC

**It checks, and fails if a check fails:**

1. `README.md` exists.
2. No `.md` file in the repository is empty.
3. No `.env` file was committed.

**Then:**

1. Add the workflow's status badge to the top of your `README.md`. (In the **Actions** tab, open the workflow, click the **...** menu, then **Create status badge**.)
2. Prove it works: open a pull request that adds an empty file `notes.md`. The check must go red. Then add a line to `notes.md`, push, and merge when it is green.

*Hints: the schedule uses `cron`, and crontab.guru helps you write the line. The hand trigger is `workflow_dispatch` with `inputs`. Inside a `run:` script, `test -f README.md` fails when the file is missing, and `[ ! -s file ]` is true when a file is empty. Pass the `reason` input to your script through `env:` instead of writing `${{ ... }}` inside the script, and think about why in Question 3.*

## Part D: Four short questions (25 points)

Answer each in 3 to 6 sentences, in your own words.

1. Explain the difference between continuous integration, continuous delivery and continuous deployment, using shop-app as the example. Which one did Lab 10 build?
2. What is a runner? Where did your jobs run, and what was on it before `actions/checkout`? What happens to it when the job ends?
3. A teammate writes a database password directly in a workflow file "because the repository is private". Give two reasons this is a bad idea, and say what to do instead. Bonus: why did we pass `reason` through `env:` in Part C?
4. Why do we run CI on pull requests, and not only on pushes to `main`?

## Optional

The free course **Hello GitHub Actions** on GitHub Skills (skills.github.com) repeats today's basics in about 30 minutes.

## Submit in Google Classroom

- the link to your `shop-app` repository and the link to the coupons pull request
- your GitHub Pages URL
- the link to your `devops-journal` repository and to the pull request with the red and green runs
- your answers to Part D

---

Previous: [Lab 10](lab-10-ship-it.md) · Next session: [Lab 11](lab-11-coverage.md) · [All labs](../README.md)
