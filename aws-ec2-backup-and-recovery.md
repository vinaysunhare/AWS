# EC2 Backup, EBS Snapshots, and AMI

## 1. Why Do We Need EC2 Backups?

If an EC2 instance is accidentally deleted, crashes, or its data becomes corrupted, we need a way to recover the data and restore the server.

As a DevOps Engineer, it is important to take regular backups to prevent data loss and support disaster recovery.

We can use **EBS Snapshots and AMIs** to back up and recover our EC2 instances.

## 2. What Is an EBS Snapshot?

When we launch an EC2 instance, we usually attach an EBS (Elastic Block Store) volume to store data. We can create a backup of this volume using an **EBS Snapshot**.

- EBS snapshots are used for backup, disaster recovery, and data protection.
- Snapshots are stored in AWS-managed storage backed by Amazon S3.
- They use incremental backups, meaning only the changed blocks are saved after the first snapshot.
- We can restore data by creating a new EBS volume from a snapshot.
- We can use snapshots to restore, migrate, or clone EBS volumes.
- We can copy snapshots to another AWS Region for disaster recovery.
- If we do not need to access old snapshots frequently, we can move eligible snapshots to the **EBS Snapshot Archive** to reduce storage costs.
- If an EBS snapshot is accidentally deleted, we may be able to recover it using the **AWS Recycle Bin**, provided a suitable retention rule was configured before deletion.

## 3. What Is an AMI?

AMI stands for **Amazon Machine Image**. It is a template used to launch new EC2 instances.

After configuring an EC2 instance, we can create an AMI containing the operating system, installed software, and configuration needed to launch similar instances.

**Example:**

Suppose we have configured a critical production server with all the required software and settings. If the server is accidentally deleted or becomes misconfigured, setting up a new server from scratch may take a lot of time.

To avoid this, we can create an AMI of the configured EC2 instance. If required, we can use that AMI to launch a new EC2 instance with the same base configuration.

We can also copy an AMI to another AWS Region and use it to launch EC2 instances there.

## 4. Difference Between EBS Snapshot and AMI

- **EBS Snapshot:** Used to back up and restore an EBS volume and its data.
- **AMI:** Used as a template to launch new EC2 instances with a particular operating system, software, and configuration.

Both are important for backup, recovery, disaster recovery, and infrastructure management.
