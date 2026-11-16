# Lab 18: Inside the server

**Session 7 · 25 minutes · EC2 console and EC2 Instance Connect**

**Goal:** log in to a server from your browser without any key on your laptop, ask the server about itself, and add a second disk that you format, fill and back up.

**Words you will need:** SSH · EC2 Instance Connect · prefix list · instance metadata · EBS volume · mount · snapshot

Region **eu-central-1**, and every name starts with your first name.

## Step 1: A server you can log in to

Launch an instance like in Lab 17, with these changes:

| Setting | Value |
|---|---|
| Name | `<yourname>-box` |
| AMI and type | Amazon Linux 2023, **t3.micro** |
| Key pair | **Proceed without a key pair** |
| Security group | Create `<yourname>-ssh-sg` with **one** rule: type **SSH**, source type **Custom**, source: type `ec2-instance-connect` in the box and choose the prefix list `com.amazonaws.eu-central-1.ec2-instance-connect` |
| User data | none |

**Why not SSH from Anywhere?** Port 22 open to the whole internet gets attacked within minutes. This prefix list is the list of addresses AWS uses for browser connections, and nothing else.

Launch it and wait for **2/2 checks passed**. Write down its **Availability Zone** (Details tab, for example `eu-central-1b`). You need it in Step 3.

## Step 2: Log in and look around

Select the instance, click **Connect**, tab **EC2 Instance Connect**, user `ec2-user`, **Connect**. A terminal opens in your browser.

```bash
whoami
cat /etc/os-release | head -3
lsblk
df -h /
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 300")
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-type; echo
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone; echo
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/public-ipv4; echo
```

`169.254.169.254` is the **instance metadata service**: an address that exists inside every EC2 server and answers questions about it. Lab 17's page used it to print the instance ID.

**Question 40:** which Linux and user are you? How big is the root disk, and what is it called in `lsblk`? What instance type and AZ did the metadata service return?

## Step 3: A second disk

1. In the console, **Volumes** (left menu, under Elastic Block Store), **Create volume**: type **gp3**, size **1** GiB, Availability Zone **the same AZ as your instance**, tag `Name` = `<yourname>-data`. Create.
2. Select the new volume, **Actions**, **Attach volume**, choose `<yourname>-box`, device name `/dev/sdf`. Attach.
3. Back in the instance terminal:

```bash
lsblk
sudo mkfs -t xfs /dev/nvme1n1
sudo mkdir /data
sudo mount /dev/nvme1n1 /data
echo "hello from $(hostname)" | sudo tee /data/hello.txt
df -h /data
```

The console calls the disk `/dev/sdf`, but Linux on modern instances calls it `/dev/nvme1n1`. Check the name `lsblk` shows before you run `mkfs`.

**Question 41:** what happens if you try to attach a volume from another Availability Zone? Why do you think EBS works like that?

## Step 4: A backup

1. In the console, select `<yourname>-data`, **Actions**, **Create snapshot**, description `<yourname> lab 18`, tag `Name` = `<yourname>-data-snap`. Create.
2. **Snapshots** (left menu): wait until the status is **Completed**.
3. Open the snapshot, **Actions**, **Create volume from snapshot**. Look at the Availability Zone list, then **Cancel**.

**Question 42:** in which Availability Zones could you create a volume from the snapshot? What does that tell you about where a snapshot is stored, compared with a volume?

## Step 5: Clean up, in this order

1. In the instance terminal: `sudo umount /data`.
2. **Volumes**: select `<yourname>-data`, **Actions**, **Detach volume**. Wait for **Available**, then **Actions**, **Delete volume**.
3. **Snapshots**: select `<yourname>-data-snap`, **Actions**, **Delete snapshot**.
4. **Instances**: terminate `<yourname>-box`. Wait for **Terminated**.
5. **Security Groups**: delete `<yourname>-ssh-sg`.

A volume and a snapshot cost money every hour they exist, even when no server uses them.

## Done when

Everything from this lab is deleted, and you can answer Questions 40 to 42.

---

Next: [Lab 19](lab-19-load-balancer-and-auto-scaling.md) · [All labs](../README.md)
