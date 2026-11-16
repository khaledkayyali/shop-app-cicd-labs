# Lab 15: Your AWS account

**Session 6 · 20 minutes · AWS Console and CloudShell**

**Goal:** sign in to AWS, find your way around the console, open a terminal inside AWS, and see for yourself what your user may and may not do.

**Words you will need:** account · region · Availability Zone (AZ) · IAM user · group · policy · ARN · CloudShell

Your instructor sent you a private message with three things: the **sign-in URL**, your **username** (`student-<yourname>`) and a **first password**. Never share them, and never paste them in Discord or GitHub.

**One rule for all AWS labs:** every name you create starts with your first name in lowercase, for example `sara-web`. Many students share this account. The name tells everyone whose resource it is.

## Step 1: Sign in

1. Open the sign-in URL. Enter your username and first password.
2. AWS asks you to choose a new password. Choose a strong one and keep it in a password manager.
3. Top right, open the region menu and choose **Europe (Frankfurt) eu-central-1**. Always check this before you start a lab: your user can only work in this region.

**Question 31:** open the account menu (your username, top right). What is the **Account ID**? Which region is selected, and what is its code?

## Step 2: Who are you, and what may you do?

1. In the search bar at the top, type **IAM** and open it.
2. **Users**, then your user `student-<yourname>`. Open the **Permissions** tab.
3. Your permissions come from the group `students`. Open the policy called **course-limits** and click **JSON**.
4. Copy your user's **ARN** from the **Summary** box.

**Question 32:** read the JSON policy. Name two things you **may** do and two things you **may not** do. Which line stops you from starting a big, expensive server?

## Step 3: A terminal inside AWS

CloudShell is a small Linux machine inside AWS, already signed in as you, with the AWS CLI installed. Nothing to install on your laptop.

1. Click the **CloudShell** icon (`>_`) in the top bar and wait for the prompt.
2. Run, one at a time:

```bash
aws sts get-caller-identity
aws configure list
aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output table
```

**Question 33:** what does the first command print, and does the `Arn` match the one from Step 2? How many Availability Zones does Frankfurt have? In your own words, what is the difference between a region and an Availability Zone?

## Step 4: Least privilege, from the inside

Run these three commands. Two of them are **supposed** to fail.

```bash
aws ec2 describe-instances --query 'Reservations[].Instances[].InstanceId'
aws iam create-user --user-name hacker-test
aws ec2 describe-instances --region us-east-1
```

Read each error message carefully.

**Question 34:** which commands failed, and what did each error say? Why does the course account block these two actions? Who benefits from the rule "give every user only the permissions they need"?

## Done when

You can sign in with your own password, CloudShell works, and you can answer Questions 31 to 34.

---

Next: [Lab 16](lab-16-s3-storage.md) · [All labs](../README.md)
