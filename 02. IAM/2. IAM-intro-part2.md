# Enable Billing Access to Billing Console for IAM Users

## Step 1: Enable IAM Access from Root Account

1. Login to AWS Console using the Root User  
   URL: https://aws.amazon.com/console

2. Open the Billing Account Settings  
   Navigate to: https://console.aws.amazon.com/billing/home#/account

3. Enable IAM Access to Billing Information  
   - Find the section titled **"IAM User and Role Access to Billing Information"**  
   - Check the box: **"Activate IAM Access"**

4. Click **"Update"** to save changes

---

# Attach Billing Access Policy to IAM User

## Step 1: Open IAM Console
URL: https://console.aws.amazon.com/iam/

## Step 2: Select IAM User
- Click on **"Users"** in the left navigation panel
- Select the IAM user who needs billing access

## Step 3: Attach Billing Permissions
- Click on **"Add permissions"**
- Choose **"Attach policies directly"**
- Search and select one of the following policies:
  - `AWSBillingReadOnlyAccess` (view-only access)
  - `AWSBillingFullAccess` (view and manage billing)

## Step 4: Apply the Policy
- Click **"Next"**, then **"Add permissions"**

The IAM user can now access billing information using their IAM login URL.
