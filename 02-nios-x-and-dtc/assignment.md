---
slug: nios-x-and-dtc
id: xfewfjl4ixe9
type: challenge
title: Configure NIOS-X, Deploy Application in AWS and Migrate
teaser: Configure NIOS-X authoritative DNS, deploy the cloud application in AWS using
  Terraform + IPAM, and set up DNS Traffic Control to shift traffic from on-prem to
  the cloud.
notes:
- type: video
  url: https://www.youtube.com/embed/vU3mKSJfS2s?si=B4_dZzddLGdVX2SJ
tabs:
- id: i1zjkhjc3mgp
  title: Jumpstation
  type: terminal
  hostname: shell
- id: tdmaqnyzw82b
  title: Editor
  type: code
  hostname: shell
  path: /root/infoblox-lab
- id: k8vpqxhwky23
  title: AWS Console
  type: browser
  hostname: aws-console
- id: mzsklbvftruq
  title: Infoblox Portal
  type: browser
  hostname: infoblox
- id: ucj4jh2hzhh4
  title: Windows Client
  type: service
  hostname: rdpclient
  path: /#/client/c/WindowsClient?username=instruqt&password=Passw0rd!
  port: 8080
- id: k72qxemo2t2t
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

You’ve confirmed the on-premises app is running and DNS is in place. Now it’s time to:
- Extend authoritative DNS into AWS using Infoblox NIOS-X,
- Deploy the cloud application using Terraform and IPAM automation,
- Use DNS Traffic Control (DTC) to migrate traffic without breaking anything.

This is the full cloud migration experience — from deployment to DNS-based traffic steering — delivered with policy alignment, automation, and zero infrastructure baggage.


⸻

# 📘 Use Case Story: Zero-Touch Cloud Expansion

Alex is ready. He’s got the Terraform files prepped and his IPAM integration wired. Now the final tasks are:
	•	Stand up a DNS service using NIOS-X
	•	Deploy the app to AWS with the right IP and DNS
	•	Use DTC to shift traffic cleanly over time

And he wants no downtime, no broken links, and full observability.

⸻

# ⚙️ What You’ll Do in This Step

In this challenge, you’ll:
- 🔹 Step 1: Configure NIOS-X for Authoritative DNS
- 🔹 Step 2: Deploy App in AWS via Terraform + IPAM
- 🔹 Step 3: Configure DTC and Perform Traffic Shift


⸻
# 💼 Business Value

This step showcases the end-to-end migration path, powered by Universal DDI:
- ✅ Rapid, authoritative DNS
- ✅ Automated IP allocation and app provisioning via Terraform
- ✅ Granular traffic steering and cutover with full DNS control
- ✅ No surprises — because policy and visibility travel with the app

You’re not just moving workloads — you’re modernizing the entire app delivery chain.

## Login to your cloud account consoles
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

🔹 Step 1: Configure NIOS-X for Authoritative DNS
==

🎯 Goal

Set up Infoblox NIOS-X as the authoritative DNS service for your migration zone.Making sure that DNS service has been enabled on the Infoblox NIOS-X.


Ensure your NIOS-X-as-a-Service instance is online, healthy, and ready to be assigned as the authoritative DNS server for your application zone.

🛠 Actions
1.	Access the Infoblox CSP Portal
- 	Go to: Infoblox Portal to the left
- 	Click Configure → Servers
2.	Locate your NIOS-X server
- You should see your instance (e.g., ZTP_demo-token_...)
- Look for the green dots indicating system health
3.	Validate key health indicators
- ✅ Platform Management: Online
- ✅ Application Management: Online
- ✅ Status: “Online”
- Server IP Address: Should show a valid internal IP (e.g., 10.100.200.200)
- NAT IP (optional): Recorded if using NAT
- Version: v4.3.10 or later
- Check the Last Seen timestamp is recent (≠ stale)


	![Screenshot 2025-07-21 at 10.20.09.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/42beb9da1b58c605d8498ac17fa04b6f/assets/Screenshot%202025-07-21%20at%2010.20.09.png)


✅ Validation

If all indicators are green and the status is Online, you’re ready to proceed with authoritative DNS configuration.

📍Phase 2: Enable the DNS Service

Enable the DNS service on your running NIOS-X instance so it can serve authoritative DNS zones.

```run
cd  /root/infoblox-lab/app-migration-niosx/terraform/scripts
python3 enable_service_dns.py
```

![Screenshot 2025-07-21 at 10.33.33.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/b9f52f50761e3a8319ddea824c91cdff/assets/Screenshot%202025-07-21%20at%2010.33.33.png)

Head over to Configure → Servers, expand your NIOS-X server, and you’ll see that the DNS service is now active and showing as “Online” — you’re good to go.


![Screenshot 2025-07-21 at 10.34.51.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/dd3488512cf9f54f3dda70aa1cfa7f19/assets/Screenshot%202025-07-21%20at%2010.34.51.png)


Now navigate to **Configure → Networking → DNS.**
Under the default DNS view, locate the zone **infolab.com** and click Edit.

In the Authoritative Servers section, make sure to select your NIOS-X server and move it to the right-hand column to assign it as the authoritative server for this zone.

![Screenshot 2025-07-21 at 10.40.17.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/88d9bc1412262d76808fda01425a532c/assets/Screenshot%202025-07-21%20at%2010.40.17.png)


As a final step, refresh the page. You’ll now see that your NIOS-X server is listed as the authoritative server for the infolab.com zone — confirming that the delegation is complete.

![Screenshot 2025-07-21 at 10.40.42.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/268783d1b3e5e1e710bf12651fbf804d/assets/Screenshot%202025-07-21%20at%2010.40.42.png)



🔹 Step 2: Deploy App in AWS via Terraform + IPAM
==

Now it’s time to deploy the application stack to the new AWS environment, using IPAM automation from Infoblox Universal DDI.

Alright, here’s where the magic kicks in.

Alex and Sam already did the heavy lifting by carving out the right Federated Realms and Address Blocks mapped strictly to AWS — no IP overlaps, no policy confusion, just clean, cloud-native boundaries.

And because they’re not into click-ops, everything’s wired up with Terraform + Universal DDI. So yeah — no IP spreadsheets, no DNS tickets, no hand-holding.

Your job? Just trigger the Terraform stack and let Universal DDI take care of:
- Subnet selection ✅
- Next-available IP assignment ✅
- EC2 provisioning ✅
- DNS registration ✅

```run
cd  /root/infoblox-lab/app-migration-niosx/terraform/app-lab/
terraform init
```

Lets run apply after initilization of the providers.

```run
cd  /root/infoblox-lab/app-migration-niosx/terraform/app-lab/
terraform apply --auto-approve
```


We’re deploying the same app stack from on-prem into AWS, but the right way — no clunky lift-and-shift, no IP collisions, and none of the drama that comes with live VM moves.

This is that active/active swim lane model — the real MVP of app migrations.

🚫 We’re not faking IPs.
🚫 We’re not forcing legacy constraints.
✅ We’re replicating the app with clean policy and full DNS visibility.

(Yeah, storage replication is still a thing — but that’s for another lab.)


# ✅ Validation

Once your A record is created and pointing to your deployed app in AWS, the final test is from the Windows client. You’ll manually configure the DNS settings to use Infoblox and then test the app’s URL.

⸻

🎯 Goal
- Manually set the DNS server on your Windows client to 10.100.0.200 (your NIOS-X DNS)
- Resolve and access app-cloud.infolab.com
- Verify the app responds from the new cloud location


# 	🛠 Instructions

✅ Step 1: Confirm Network Connection

![Screenshot 2025-07-21 at 11.17.11.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/02aa49cccb302077e21c4a04c9e6ad80/assets/Screenshot%202025-07-21%20at%2011.17.11.png)

Make sure your Windows instance shows it’s connected to the Internet.

✅ Step 2: Go to Network Settings

![Screenshot 2025-07-21 at 11.17.28.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/c428d5d9f458db3bdd1823709df7b321/assets/Screenshot%202025-07-21%20at%2011.17.28.png)

Under Status, click Change adapter options.


✅ Step 3: Modify Adapter Settings

![Screenshot 2025-07-21 at 11.17.45.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/88ea44b390b1766e3895f2f6f0c30390/assets/Screenshot%202025-07-21%20at%2011.17.45.png)


1.	Right-click on your active Ethernet adapter (e.g., Ethernet 3)
2.	Select Properties


✅ Step 4: Set Infoblox DNS IP

![Screenshot 2025-07-21 at 11.17.55.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/79a0c8af809d465909b5b099d4e1cfa2/assets/Screenshot%202025-07-21%20at%2011.17.55.png)

![Screenshot 2025-07-21 at 11.18.05.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/87baeba773a88b8416abb0d395171426/assets/Screenshot%202025-07-21%20at%2011.18.05.png)


1. Select Internet Protocol Version 4 (TCP/IPv4) → click Properties
2. Choose:
✅ Obtain IP address automatically
✅ Use the following DNS server address
Enter:
- Preferred DNS: 10.100.0.200

> [!IMPORTANT]
> DNS Server IP: 10.100.0.200

- Leave alternate empty
- Click OK, then Close

✅ Step 5: Launch the Browser & Access App


Open Microsoft Edge or any browser and go to:

```
http://app-cloud.infolab.com:5080
```
![Screenshot 2025-07-21 at 11.18.20.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/f366be302b2e460c4bdd72a6e0e5e2e7/assets/Screenshot%202025-07-21%20at%2011.18.20.png)
![Screenshot 2025-07-21 at 11.19.39.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/bed5b7bd544ef5e5e3dd8457ffaa3b02/assets/Screenshot%202025-07-21%20at%2011.19.39.png)


You should see the “Hi folks!” portal confirming:

- Hostname: new-location
- IP Address: 10.100.0.10

> [!NOTE]
> 🎉 Boom — this confirms DNS is resolving via Infoblox and the app is running from the AWS deployment!

🔹 Step 3: Configure DTC and Perform Traffic Shift
==

🎯 Goal

We will create a DTC pool that includes two application endpoints and gradually shift traffic from the on-prem instance to the cloud-deployed one, ensuring seamless user experience without disruption.
1.	First, we’ll define two DTC servers:
- **Srv1**: Represents the on-prem IIS application, running at IP** 10.100.0.120**
- **Srv2**: Represents the cloud-hosted application post-migration, with IP **10.100.0.10**
2.	Both servers will be added to the DTC pool.
3.	During migration, we will move away from using static IPs. Instead, we will assign an FQDN (fully qualified domain name) to the new cloud application (Srv2) — enabling flexible identity and cloud-native scaling.
4.	Traffic will be gradually shifted from Srv1 to Srv2, allowing for a controlled, low-risk transition with no downtime.


🛠 Actions

### ✅ Step 1: Access the DNS Traffic Control Section

From the Infoblox UI:
	•	Navigate to DTC : Configure→ Networking →DNS → DTC → DTC Servers

![Screenshot 2025-07-21 at 11.39.55.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/aeb3121c24af8fb6f3097cc74648c11b/assets/Screenshot%202025-07-21%20at%2011.39.55.png)


### ✅ Step 2: Create a DTC Servers

1.	Click Create DTC Server

![Screenshot 2025-07-21 at 11.42.29.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/dc3e0ec297cbad3a643f973d9f44ff14/assets/Screenshot%202025-07-21%20at%2011.42.29.png)

2.	Click on “Create DTC Server,” assign a name, and leave the remaining configuration settings as shown in the screenshot below.

![Screenshot 2025-07-21 at 11.43.23.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/e5a978ed898d50251a46d935c247dfe5/assets/Screenshot%202025-07-21%20at%2011.43.23.png)

3. Clikc "Save&Close"

4. Repeat the previous steps ( 1. - 3. )  to create another DTC Server.
Just make sure to assign a new name and keep the rest of the configuration consistent with image below.

![Screenshot 2025-07-21 at 11.45.21.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/b55a6e250792b9913eb3981f1e162011/assets/Screenshot%202025-07-21%20at%2011.45.21.png)


![Screenshot 2025-07-21 at 11.46.31.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/8261cdaa2c65aa5e025799c362257b94/assets/Screenshot%202025-07-21%20at%2011.46.31.png)


### ✅ Step 3: Create a DTC Pool

Select DTC Pools under DTC option

![Screenshot 2025-07-21 at 11.48.09.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/9d9dc986b77411cc913c774f882126a3/assets/Screenshot%202025-07-21%20at%2011.48.09.png)

1.	Click Create Pool
2.	Give it a name
```
Lab
```
3.	Set the Load Balancing Method to **Ratio**


> [!NOTE]
> NOTE: Enable the “Override default TTL” option, then set your record’s TTL value to 10 seconds.

![Screenshot 2025-07-21 at 11.50.03.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/8e7fe8e9375d625bbb7435b220759689/assets/Screenshot%202025-07-21%20at%2011.50.03.png)

4.	Under Servers, click Add Server twice:
- Member 1: Srv1 (e.g., 10.0.0.10) - weight 100
- Member 2: Srv2 (e.g., 10.100.0.10) - weight 1

👉 This means: almost all DNS responses will return the IP of Srv1 ( which is on-prem application ) from this pool — unless Srv1 is down or fails health checks.

![Screenshot 2025-07-21 at 12.31.15.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/6fbb0868aba54e8a66eaf3c646ce31c1/assets/Screenshot%202025-07-21%20at%2012.31.15.png)


5. Click Next
6. Select HTTP health checks and move it to the right

![Screenshot 2025-07-21 at 11.54.13.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/f11e268c3f616ce8a1410c51df6a4df4/assets/Screenshot%202025-07-21%20at%2011.54.13.png)

7. Click Next
8. Click Save&Close

### ✅ Step 4: Add a Health Check  ( EDIT)
1.	Go to DTC → DTC Health Checks
2.	Edit the existing HTTP check

![Screenshot 2025-07-21 at 11.56.55.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/38d017b42991036a7b5b343d3be87e9a/assets/Screenshot%202025-07-21%20at%2011.56.55.png)

In the health check settings, change the port to 5080 instead of the default 80, as shown in the image below.

![Screenshot 2025-07-21 at 11.58.52.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/1ed8f4dead3fdbb91b3fd98650d6421f/assets/Screenshot%202025-07-21%20at%2011.58.52.png)

3.Click Save&Close


### ✅ Step 5: Create DTC Policy
1.	Go to DTC → DTC Polices
2.	Click Create DTC Policy

![Screenshot 2025-07-21 at 12.01.46.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/1a5f4a5cff9bdfbe29bf0af1da6a5e32/assets/Screenshot%202025-07-21%20at%2012.01.46.png)

3. Give it the name Lab and keep the settings as you see below




![Screenshot 2025-07-21 at 12.02.35.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/b7b96ba71eaefc138f9bb34600e2bcd4/assets/Screenshot%202025-07-21%20at%2012.02.35.png)

⸻

🧠 Understanding Ratio-Based Load Balancing

Ratio-based load balancing allows precise control over how DNS traffic is distributed across DTC pools or members, using numeric weights. Unlike round-robin, this method doesn’t evenly rotate — it routes traffic proportionally based on the weight assigned.

✅ In Our Lab Scenario
- We’ve created one DTC Pool with a total weight of 100
- This pool contains two backend members:
- 🏠 On-prem instance
- ☁️ AWS-deployed app

Inside this pool, we’re leveraging the DTC Pool’s internal ratio mechanism to gradually shift traffic between the two servers during migration

⸻

4. Click Next
5. Select the appropriate pool that we created in the one of previous steps and keep the settings as you see below

![Screenshot 2025-07-21 at 12.03.54.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/0fcfd48e709529e40de6985f0d841222/assets/Screenshot%202025-07-21%20at%2012.03.54.png)

6. Click Next
7. Click Save&Close


### ✅ Step 6: Create DTC LBDN

1.	Go to DTC → LBDNs

![Screenshot 2025-07-21 at 12.04.54.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/76233fe185c1ac634d20a2b9ae26d9ee/assets/Screenshot%202025-07-21%20at%2012.04.54.png)

2. Click Create LBDN and keep the settings as you see on the picture below

![Screenshot 2025-07-21 at 12.37.13.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/332a10ae25666731ffb52b479757d580/assets/Screenshot%202025-07-21%20at%2012.37.13.png)

3. Click Save&Close



## 🧪 Step: Validate Legacy Application & Prepare for Traffic Shift

With the DTC configuration complete, we’ve initially set 100% of traffic to go to the on-prem legacy application (old IIS server).

✅ What to Do
1.	Open the App in the Client Browser
- In the left-hand Instruqt window, which runs your Windows Client, open Internet Explorer (or Edge if available).
- Go to:

```
http://app.infolab.com:5080
```

![Screenshot 2025-07-21 at 12.14.48.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/bb9b4ef228198068c041d93a52cb63de/assets/Screenshot%202025-07-21%20at%2012.14.48.png)



> [!NOTE]
> You should see the old version of the app.


2.	Run DNS Resolution Ratio Script:

- On the same Windows client, open PowerShell.

![Screenshot 2025-07-21 at 13.10.52.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/35382c4ea2e9ddc9489c6520d9a8f4c1/assets/Screenshot%202025-07-21%20at%2013.10.52.png)

> [!IMPORTANT]
> NOTE:💡 Navigate to your Downloads folder before running the script.

```run
cd $HOME\Downloads
```

![Screenshot 2025-07-24 at 20.58.38.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/d99ba42f908ed24064d4939be60f80cb/assets/Screenshot%202025-07-24%20at%2020.58.38.png)

- Run the following script to simulate 50 DNS queries:

```run
.\test.ps1
```

If you encounter a policy or permission error, use the above command to bypass execution restrictions temporarily.

```run
powershell -ExecutionPolicy Bypass -File .\test.ps1
```


This will give you a summary of how many DNS responses point to each backend IP.
Example output:

![Screenshot 2025-07-21 at 12.52.16.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/02f4abe15c82c3d5b21ac843fd293225/assets/Screenshot%202025-07-21%20at%2012.52.16.png)


> [!IMPORTANT]
> ✅ This confirms all traffic is going to the legacy server (as expected).


🧠 What’s Next?

We’re ready to shift traffic gradually to the new app hosted in AWS. But to save time in this lab, we’ll go straight to a 50/50 DTC weight ratio and observe how the resolution splits between both endpoints.


## 🔁 Step: Shift Traffic to 50/50


Now that we validated all traffic is hitting the on-prem app, it’s time to update the DTC server weights and start splitting the traffic.

⚙️ Actions:
1.	Go to the DTC Pool.
2.	Edit the server weights:
- Srv1 (on-prem): 50
- Srv2 (AWS): 50
3.	Save the changes.


✅ Validate 50/50 Split

1. Rerun the script( be in the same folder **Dowloads** in the powershell cmd and always flush dns caching values ):

```run
ipconfig /flushdns
```

```run
.\test.ps1
```

2.	You should now see a roughly even distribution between both IPs:
Example output:

![Screenshot 2025-07-21 at 12.53.18.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/ac8c381e68f1c32af0a0e2d4b3e62127/assets/Screenshot%202025-07-21%20at%2012.53.18.png)


## ✅ Step: Complete Migration to New App (100% Cloud)


Once you’re happy with the test, push the full cutover:

Actions:
1.	Go back to the DTC Pool.
2.	Set weights:
- Srv1 (on-prem): 1
- Srv2 (AWS): 100
3.	Rerun the script after flushing the dns cash:

```run
ipconfig /flushdns
```

```run
.\test.ps1
```

You should now see:

![Screenshot 2025-07-21 at 12.54.21.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/257f33bad8b9508e84d50037915cc637/assets/Screenshot%202025-07-21%20at%2012.54.21.png)

4.	After closing the browser and deleting the browser cash - reopen the browser at http://app.infolab.com:5080 — this time, you should only see the new application UI.


![Screenshot 2025-07-21 at 12.18.44.png](https://play.instruqt.com/assets/tracks/mvbwpzb5c9dc/27497bd80a91399f6ddc36db9c60885b/assets/Screenshot%202025-07-21%20at%2012.18.44.png)




💡 Mission accomplished — DNS-based traffic migration complete using Infoblox DTC.





