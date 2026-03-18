# LAB-8--AWS-IAM-Role-Based-Temporary-S3-Access
Implementing Temporary Access to S3 Using STS AssumeRole

# Step 1: Create IAM Role (Temporary S3 Access Role)

Go to IAM → Roles → Create role

Trusted entity: AWS account

Select: This account

Next → Attach policy:

Attach: AmazonS3ReadOnlyAccess

Role name:  S3TempReadRole

Now IMPORTANT: Edit Trust Relationship

Go inside the role → Trust relationships → Edit

Replace with:

👉 Replace <ACCOUNT-ID> with your AWS account ID
👉 Replace test-user with your IAM username

This step is what allows the user to assume the role.


### Trust Policy for IAM Role (Allow User to Assume Role)

Replace `<ACCOUNT-ID>` and `test-user` with your actual values.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<ACCOUNT-ID>:user/test-user"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```


# Step 2: Create IAM User (No S3 Access)

Go to IAM → Users → Create user

Username:  test-user

Select:

✔ Custom password

✔ AWS Management Console access

Finish creating user.

👉 Do NOT attach any S3 policy here.


# Step 3: Login as User (Test No Access)

Open incognito window

Login using:

IAM user credentials

Now go to S3

👉 You should see:

❌ Access Denied

Good. That means restriction is working.


# Step 4: Give Permission to Assume Role

Now login as Root/Admin

Go to:

IAM → Users → test-user → Add inline policy

Choose JSON and paste:


```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::<ACCOUNT-ID>:role/S3TempReadRole"
    }
  ]
}
```



save 

# Step 5: Switch Role (User Side)

Now go back to incognito (user login)

Click:

Top right → Switch Role

Enter:

Account ID: <ACCOUNT-ID>

Role name: S3TempReadRole

Display name: anything (e.g. S3Access)

Click Switch Role

# Step 6: Verify Access

Now open S3

👉 You should now:

✅ See buckets

✅ Read objects

# What This Lab Proves

IAM user = ❌ No S3 access

Role = ✅ Has S3 access

STS = 🔁 Temporary access switch

What this really means is:
You’re not giving permanent permissions to users, instead you’re giving controlled, temporary access using roles.


# Common Mistakes (why it fails)

If something doesn’t work, check these:

Trust policy not updated ❌

Wrong ARN in inline policy ❌

Role name mismatch ❌

Not using “Switch Role” ❌
