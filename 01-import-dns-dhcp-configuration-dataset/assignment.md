---
slug: import-dns-dhcp-configuration-dataset
id: p0kn4dd8cyxb
type: challenge
title: Review Architecture and Deployed Resources
teaser: Review the current on-prem deployment and validate existing DNS and IP configurations
  in Infoblox Universal DDI.
notes:
- type: video
  url: https://cdn.instruqt.com/assets/instruqt/2024-036%20Instruqt%20101_Version%203.0.mp4
tabs:
- id: sgy0mqiags7c
  title: jumpstation
  type: terminal
  hostname: shell
- id: spuetmczpihw
  title: Editor
  type: code
  hostname: shell
  path: /root/infoblox-lab
- id: 6uxxvpzhdkm5
  title: AWS Console
  type: browser
  hostname: aws-console
- id: syy7wt48higa
  title: Windows Client
  type: service
  hostname: rdpclient
  path: /#/client/c/WindowsClient?username=instruqt&password=Passw0rd!
  port: 8080
- id: y2tkxzglkfyw
  title: Infoblox Portal
  type: browser
  hostname: infoblox
- id: 7gqgetppyocq
  title: Lab Diagram
  type: browser
  hostname: lab
difficulty: ""
timelimit: 0
lab_config:
  default_layout_sidebar_size: 0
enhanced_loading: null
---
# 🔍 Overview


As organizations modernize, migrating applications to the cloud is no longer optional — it’s strategic. But “lifting and shifting” an app is only part of the picture. To ensure availability, visibility, and operational continuity, CloudOps and NetOps teams must rethink how IP addressing, DNS, and traffic steering are handled during and after migration.

Infoblox Universal DDI changes the game — delivering automated IPAM, centralized DNS visibility, and intelligent DNS Traffic Control across hybrid environments. No more relying on static IP reservations, scattered DNS entries, or guesswork when it comes to cutover plans.

This lab simulates a real-world application migration from a legacy on-prem deployment to a new AWS environment, with Universal DDI guiding every step.


# 📘 Use Case Story: Modernizing with Control

Meet Alex, a CloudOps engineer at a fast-growing SaaS company. His team is tasked with migrating a legacy IIS-based web application from their data center to AWS — but with a twist:
1. The migration must be gradual, with no downtime.
2. DNS cutover must be controlled, and auditable.
3. IP provisioning should be automated, and cloud-native.

Partnering with their NetOps lead, Sam, Alex decides to leverage Infoblox Universal DDI to handle both the IP allocation and DNS Traffic Control needed for this seamless transition.

Here’s the current state:
- The application is running on VM on-prem.
- IP and DNS are manually managed.
- There is no visibility or automation across environments.

Before deploying to AWS, Alex needs to validate the current setup — and you’ll do the same now.


🧱 Infrastructure Context

You’ve been assigned a sandbox environment that simulates:
- An on-prem VPC with a pre-deployed Flask application
- DNS settings managed via Infoblox Universal DDI
- Real IPAM and DNS data tied to this “on-prem” workload

This setup mirrors what many enterprises still rely on — giving you a realistic baseline to work from.


🧠 Why This Matters

Successful migrations don’t start with Terraform — they start with visibility. Knowing the existing state of DNS, IP reservations, and DHCP configurations ensures:
- Fewer surprises post-deployment
- A reliable rollback path
- Confidence when shifting traffic with DNS Traffic Control


# 💼 Business Value

Most cloud migrations fail to deliver full value because of fragmented network services — IP address collisions, DNS misconfigurations, and lack of visibility stall progress and kill confidence.

With Infoblox Universal DDI, organizations gain:
- ✅ Frictionless IP provisioning across on-prem and cloud — no more manual IP planning spreadsheets.
- ✅ Centralized DNS control that spans legacy and modern workloads — with automation and consistency.
- ✅ Smarter migration execution with DNS Traffic Control — shift workloads without breaking apps or user trust.
- ✅ Reduced operational risk by eliminating configuration drift and manual handoffs between NetOps and CloudOps.
- ✅ Audit-ready compliance — every IP, DNS record, and traffic shift is tracked in a single control plane.

By integrating Infoblox Universal DDI into your cloud migration workflow, you’re not just lifting and shifting — you’re modernizing the way core network services are delivered, secured, and governed.

> [!IMPORTANT]
> **NOTE:** This environment is *real*! AWS Cloud Accounts have been created for each student. No bitcoin mining, please! :)

In this section we will:
1) Review the cloud architecture
2) Login to your cloud account console's
3) Deploy resources onto your cloud regions
4) Create your Infoblox Portal user




---

## 1) Review Cloud Architecture
===

First lets review the cloud architecture that has been provision for your Infoblox Lab experience.

Navigate to the *Lab Diagram* tab above and review the diagram. This is what we're building today!




## 2) Login to your cloud account consoles
===

🔐 Access Instructions

Using the credentials below, log in to the AWS  Web Consoles.

---
# AWS Credentials ☁️

🔐 Logging In to the AWS Console.

👉 First, open the “AWS Console” tab on the left-hand side of your Instruqt lab environment. This will launch the AWS login page in a new browser panel.

![Screenshot 2025-07-12 at 11.23.29.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/86d80bec0e3af0161dbb62f6e26e2626/assets/Screenshot%202025-07-12%20at%2011.23.29.png)

Then follow these steps:
1.	Select “IAM Account”.
On the login screen, choose IAM Account (not root).

![Screenshot 2025-07-12 at 11.23.29.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/86d80bec0e3af0161dbb62f6e26e2626/assets/Screenshot%202025-07-12%20at%2011.23.29.png)

2.	Enter the AWS Account ID, AWS IAM username, and password by copying and pasting the values from the section below.

📝 Note: Avoid the root account login — this lab is configured for IAM users only.

**AWS Account ID**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_ACCOUNT_ID" hostname="shell" ]]
```

**AWS Username**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_USERNAME" hostname="shell" ]]
```

**AWS Password**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_PASSWORD" hostname="shell" ]]
```
---

## 3) Deploy resources onto your cloud regions
===

🧱 Deploying the Lab Infrastructure

Now that you’ve logged into both cloud consoles, it’s time to deploy the infrastructure that reflects the architecture shown in the lab diagram.

👉 Switch back to the “>_ Shell” tab in the left-side panel of your Instruqt lab to proceed.

### 1. Deploy AWS resources in EU

⚙️ Pre-deployed Infrastructure(AWS)

To save time and avoid long waits during the lab, core resources have already been provisioned in the background using Terraform. You don’t need to deploy everything from scratch.

You can verify this pre-deployment in two ways:

•	🔍 AWS Console – Navigate to the relevant services (EC2, VPC, TGW, etc.) and confirm that the resources are already in place according to the LAB Diagram.

•	🧾 Terraform Output – Run terraform output in the repo directory to view key variables and resource info like instance IPs, VPC IDs, etc.

```run
cd ~/infoblox-lab/Infoblox-PoC/terraform
terraform output
```

If you’re curious, the actual Terraform code used is available in the **Editor** window of this lab. Feel free to explore it and see how each resource is defined and managed.


## 4) Create Admin User to your Infoblox Portal Dashboard
===


> [!NOTE]
> Note: Use your Business Email for User Creation

Your user account and sandbox have already been created. The next step is to set up your password and activate your account.

> [!IMPORTANT]
> NOTE: If your account has already been activated on the Infoblox Portal, please proceed to the password reset steps outlined in Section 2.

### Section 1

1. Please check the inbox of the email account you used to register for the Infoblox Lab.
2. You will receive an email with the subject “Infoblox User Account Activation.” Open this email and click on the “Activate Account” button to proceed.

![Screenshot 2025-04-01 at 11.15.44.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/93d7e021e28c20d105ca34aace35d149/assets/Screenshot%202025-04-01%20at%2011.15.44.png)

4. A new browser window or tab will open, prompting you to create a new password. Please enter your desired password to complete the setup.

![Screenshot 2025-04-01 at 11.19.01.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/0fd5315d47a283fce641de81b289ac64/assets/Screenshot%202025-04-01%20at%2011.19.01.png)

5. Once you’ve set your password, close the newly opened tab or window. We’ll be logging in through the Instruqt tab labeled "Infoblox Portal".
6. In the Instruqt tab labeled "Infoblox Portal", log in using the credentials you set up in the previous steps.
7. After logging in, please mark the first window as shown in the example below. This confirms that you have successfully accessed the Infoblox Portal.

![Screenshot 2025-04-01 at 11.01.03.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/a082b5c7276e1c2e27eaaf7bee832089/assets/Screenshot%202025-04-01%20at%2011.01.03.png)

7. In the upper-left corner of the Infoblox Portal, click on the drop-down menu. Use the “Find Account” field to search for your sandbox by entering the "Sandbox-ID" shown below. It is important that you select your specific Sandbox-ID from the list and click on it to proceed.

**Your Sandbox ID**
```
[[ Instruqt-Var key="Sandbox_ID" hostname="shell" ]]
```

---

![Screenshot 2025-07-18 at 09.16.24.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/09e41ec1cf57a6f2cbe5d2c47721a26b/assets/Screenshot%202025-07-18%20at%2009.16.24.png)

---

### Section 2

✅ Existing User

1.	Go to Infoblox Portal TAB on the left.
2.	Login using your existing email address and password.
3.	Once authenticated, the lab tenant will be automatically added to your list of available tenants (you’ll see it in the top-right tenant switcher).

![Screenshot 2025-07-18 at 09.16.24.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/09e41ec1cf57a6f2cbe5d2c47721a26b/assets/Screenshot%202025-07-18%20at%2009.16.24.png)

In order to RESET the password follow the steps below:

1. Please navigate to the Infoblox Portal page by clicking on the Infoblox Portal tab within the Instruqt lab environment. This will direct you to the appropriate login interface.
2. Once you’re on the Infoblox Portal page, click on “Need Assistance” located at the bottom of the login form.

![Screenshot 2025-04-01 at 10.52.47.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/0bacd7dc4193c2770df54b65c7eceb3a/assets/Screenshot%202025-04-01%20at%2010.52.47.png)

3. After clicking on “Need Assistance”, select “Forgot Password” from the available options to initiate the password reset process.

![Screenshot 2025-04-01 at 10.52.57.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/3971464a872c428ee8879f3d7287b201/assets/Screenshot%202025-04-01%20at%2010.52.57.png)

4. You will receive an email with the subject “Account Password Reset.” Open this email and click on the “Reset Password” button to proceed with setting a new password.

![Screenshot 2025-04-01 at 11.42.21.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/263657baa43cefce4e8fc2062d2371f1/assets/Screenshot%202025-04-01%20at%2011.42.21.png)

5. Once you’ve set up your password, please return to "Section 1" of the instructions and continue from Step 4 onward.




## Time for the Next Challenge

Now we've inspected the playing field its game time. Click **NEXT**!

