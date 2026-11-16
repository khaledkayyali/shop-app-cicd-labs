# Lab 16: Store files in S3

**Session 6 · 25 minutes · CloudShell and the S3 console**

**Goal:** create a bucket, upload shop-app to it, keep old versions of a file, share one file safely with a link that expires, and delete it all.

**Words you will need:** bucket · object · key · versioning · private by default · presigned URL

Work in **CloudShell** (`>_` in the top bar), in region **eu-central-1**.

## Step 1: Your bucket

Bucket names are unique in the whole world, so add a random number. Replace `sara` with your first name:

```bash
ME=sara
BUCKET=shop-app-$ME-$RANDOM
echo "export ME=$ME BUCKET=$BUCKET" >> ~/.bashrc
echo $BUCKET
aws s3 mb s3://$BUCKET
```

The `~/.bashrc` line saves the two names, so they still work if CloudShell restarts. If it does, run `source ~/.bashrc`.

## Step 2: Upload shop-app

CloudShell has Git. Clone your own shop-app (it is public), put your name into the page, and upload:

```bash
git clone https://github.com/<your-username>/shop-app.git
sed "s/__STORE__/$ME's shop/; s/__COMMIT__/served from S3/" shop-app/site/index.html > index.html
aws s3 cp index.html s3://$BUCKET/site/index.html
aws s3 sync shop-app/ s3://$BUCKET/code/ --exclude ".git/*"
aws s3 ls s3://$BUCKET --recursive --human-readable
```

If your shop-app has no `site/` folder yet (it comes from Lab 10), make a small page instead of the `sed` line: `echo "<h1>$ME's shop</h1>" > index.html`.

Now open the **S3** console, find your bucket, and click through the folders `site/` and `code/`. S3 has no real folders: the "folder" is just the start of each object's name, its **key**.

## Step 3: Keep old versions

1. Turn on versioning:

```bash
aws s3api put-bucket-versioning --bucket $BUCKET --versioning-configuration Status=Enabled
```

2. Upload a file twice, with different content:

```bash
echo "version 1" > notes.txt
aws s3 cp notes.txt s3://$BUCKET/notes.txt
echo "version 2" > notes.txt
aws s3 cp notes.txt s3://$BUCKET/notes.txt
aws s3api list-object-versions --bucket $BUCKET --prefix notes.txt \
  --query 'Versions[].[VersionId,IsLatest,LastModified]' --output table
```

3. Get the old version back. *Hint: `aws s3api get-object` takes `--bucket`, `--key`, `--version-id` and a file name to save to. The old version is the one where `IsLatest` is `False`.*
4. Delete the file with `aws s3 rm s3://$BUCKET/notes.txt`, then list the versions again.

**Question 35:** after the delete, how many versions of `notes.txt` are still there, and what is a **delete marker**? Why do teams turn versioning on for important buckets? What does it cost them?

## Step 4: Private by default, shared on purpose

1. In the S3 console, open `site/index.html` and copy its **Object URL**. Open it in a new browser tab. What do you see?
2. Now make a link that works for 10 minutes only:

```bash
aws s3 presign s3://$BUCKET/site/index.html --expires-in 600
```

3. Open that long link. Your shop page shows.

**Question 36:** why did the Object URL fail? Why is a link that expires safer than making the whole bucket public?

## Step 5: Clean up

A bucket with versioning cannot be deleted while any version is inside it. The console has a button that removes every version for you:

1. S3 console, select your bucket, click **Empty**, type `permanently delete`, confirm.
2. Select the bucket again, click **Delete**, type the bucket name, confirm.
3. In CloudShell: `aws s3 ls | grep $ME` prints nothing.

## Done when

Your bucket is gone, and you can answer Questions 35 and 36.

---

Previous: [Lab 15](lab-15-your-aws-account.md) · Next: [Lab 17](lab-17-your-first-server.md) · [All labs](../README.md)
