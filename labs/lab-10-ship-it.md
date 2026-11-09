# Lab 10: Ship it

**Session 4 · 20 minutes in class, finish at home · project: shop-app**

**Goal:** every merge to `main` tests shop-app and then publishes its web page to GitHub Pages. On the way, use a repository variable and a secret.

**Words you will need:** CD · GitHub Pages · artifact · environment · `permissions` · `vars` · `secrets`

`main` is protected since Lab 9, so every change in this lab goes through a branch and a pull request. Start with a branch called `feature/website`.

## Step 1: Add the page (copy exactly)

Create `site/index.html`. The words in capitals between double underscores are placeholders that the pipeline will replace.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width">
  <title>__STORE__</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #F5F4EF;
      color: #0E1A2B;
      padding: 48px 16px;
    }
    main {
      max-width: 640px;
      margin: 0 auto;
      background: #fff;
      border-radius: 12px;
      padding: 32px;
    }
    h1 { color: #255BD0; }
  </style>
</head>
<body>
  <main>
    <h1>__STORE__</h1>
    <p>Welcome to Shop App. Prices are in ILS.</p>
    <p>This page was tested and deployed by GitHub Actions.</p>
    <p>Version: <code>__COMMIT__</code></p>
  </main>
</body>
</html>
```

Commit it with the message `Add website`. Do not push yet.

## Step 2: Turn on Pages

On GitHub: **Settings**, then **Pages**. Under **Build and deployment**, set **Source** to **GitHub Actions**. Your repository must be public for this to work on a free account.

## Step 3: A variable and a secret

On GitHub: **Settings**, then **Secrets and variables**, then **Actions**.

1. **Variables** tab, **New repository variable**. Name: `STORE_NAME`. Value: a name for your shop, such as `Khaled Shop`. Do not use the `/` character.
2. **Secrets** tab, **New repository secret**. Name: `API_TOKEN`. Value: a fake value such as `my-fake-token-123`. Never use a real password in a lab.

## Step 4: The deploy workflow

Create `.github/workflows/deploy.yml`. This part is given (type exactly):

```yaml
name: Deploy site

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # YOUR STEPS GO HERE (see the list below)

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v5
```

Now write the steps of the `build` job, in this order:

1. Check out the code.
2. Set up Python 3.13.
3. Install everything in `requirements.txt`.
4. Run the tests. We never ship code that was not tested.
5. Put the store name and the version into the page (type exactly):

```yaml
      - name: Stamp store name and version
        env:
          STORE_NAME: ${{ vars.STORE_NAME || 'Shop App' }}
        run: |
          sed -i "s/__STORE__/$STORE_NAME/g" site/index.html
          sed -i "s/__COMMIT__/${GITHUB_SHA::7}/" site/index.html
```

6. Try to print the secret (type exactly):

```yaml
      - name: Use a secret (it stays hidden)
        env:
          API_TOKEN: ${{ secrets.API_TOKEN }}
        run: |
          echo "Token length: ${#API_TOKEN}"
          echo "Trying to print it: $API_TOKEN"
```

7. Prepare Pages with the action `actions/configure-pages@v6`.
8. Upload the `site` folder with the action `actions/upload-pages-artifact@v5`. *Hint: it takes one input, `path`.*

## Step 5: Ship

1. Commit `deploy.yml`, push the branch, and open a pull request. Wait for the CI checks, then merge.
2. The merge is a push to `main`, so **Deploy site** starts. In the **Actions** tab, watch `build` and then `deploy`.
3. Open the link shown under the `deploy` job. It looks like `https://<your-username>.github.io/shop-app/`.

**Check:** the page shows your store name and a 7-character version.

**Question 18:** open the log of **Use a secret (it stays hidden)**. What did GitHub print for the token, and what did it print for its length? Why does GitHub do this?

## Step 6: Change without a commit

1. Change the value of `STORE_NAME` in the repository settings.
2. In the **Actions** tab, run **Deploy site** by hand on `main`.
3. Reload your page.

**Question 19:** what changed on the page? Why did you not need a new commit? Compare the version on the page with the latest commit on `main` on GitHub: what is the version?

## Step 7: The full loop

On a new branch, change the line `Welcome to Shop App. Prices are in ILS.` to a sentence of your own. Open a pull request, merge it when the checks are green, and time how long it takes until the new sentence is live.

**Question 20:** about how many minutes passed from merge to live page? Write the full path your change took, from your editor to the live page, in 5 to 7 short steps.

## Done when

Your page is live with your own sentence, and you can answer Questions 18 to 20.

## Submit in Google Classroom

The link to your `shop-app` repository, your GitHub Pages URL, and your answers to Questions 12 to 20.

---

Previous: [Lab 9](lab-09-faster-and-stricter.md) · Next: [Homework 4](homework-04.md) · [All labs](../README.md)
