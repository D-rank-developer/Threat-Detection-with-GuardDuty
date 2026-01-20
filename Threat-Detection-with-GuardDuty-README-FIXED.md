# Threat Detection with Amazon GuardDuty (OWASP Juice Shop)

## Tools Used
- **Amazon GuardDuty** (Findings + Malware Protection)
- **AWS CloudShell** (attacker simulation terminal)
- **Amazon S3** (sensitive data storage)
- **Amazon CloudFront** (public entry point to web app)
- **AWS CloudFormation** (deploys the lab infrastructure)
- **OWASP Juice Shop** (vulnerable web application)

---

## Goals Achieved
- Deployed a cloud security lab using **AWS CloudFormation**.
- Accessed OWASP Juice Shop via **CloudFront** and performed web attacks.
- Demonstrated how web attacks can expose **AWS instance credentials**.
- Used temporary credentials in **CloudShell** to access **S3** resources.
- Investigated **GuardDuty findings** for credential abuse.
- Enabled **Malware Protection** and validated detection using an **EICAR test file**.

---

## Why GuardDuty is Important (Machine Learning / Anomaly Detection)
Amazon GuardDuty is essential for cloud security because it continuously analyses AWS logs (CloudTrail events, VPC Flow Logs, DNS logs, S3 data events) and detects threats using:
- **Threat intelligence** (known malicious indicators)
- **Behaviour baselining** (anomaly detection)
- **Pattern-based detection** (known attacker techniques)

This means GuardDuty can detect unusual behaviour like **credential exfiltration** and suspicious access to sensitive data.

---

# Step-by-Step Walkthrough (Image 1 → 47)

> Your screenshots are already ordered in the document from **image1 → image47**. I followed that order below.

---

## Part A — Deploy the Web App Lab Environment (CloudFormation)

### 1) Open CloudFormation and start stack creation
- Go to **CloudFormation → Stacks → Create stack**.
![Open CloudFormation stacks page](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image1.png)

### 2) Choose to create a new stack
- Select **With new resources (standard)**.
![Create stack - choose new resources](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image2.png)

### 3) Upload the provided CloudFormation template
- Upload the YAML template file (e.g., `guardduty-setup.yaml`).
![Upload CloudFormation template file](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image3.png)

### 4) Confirm the template is uploaded
- Ensure the template name appears and proceed.
![Template selected successfully](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image4.png)

### 5) Enter stack name and configure settings
- Provide a stack name and keep default settings.
![Provide stack name and configuration](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image5.png)

### 6) Configure rollback behaviour (important)
- Select rollback settings (this was part of the troubleshooting later).
![Rollback options](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image6.png)

### 7) Acknowledge capabilities and create the stack
- Tick **IAM capabilities** and click **Create stack**.
![Acknowledge IAM capability](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image7.png)

### 8) Monitor stack events while resources are created
- Confirm status shows **CREATE_IN_PROGRESS**.
![Stack events in progress](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image8.png)

---

## Part B — Verify Resources & Access OWASP Juice Shop

### 9) Confirm the stack deployment has completed
- Check status becomes **CREATE_COMPLETE**.
![CloudFormation stack complete](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image9.png)

### 10) Review the resources deployed by the stack
- This includes **CloudFront, EC2 Web Server, S3 bucket, IAM role**.
![CloudFormation resources tab](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image10.png)

### 11) Open CloudFormation Outputs
- Copy the **JuiceShop URL** from Outputs.
![CloudFormation outputs](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image11.png)

### 12) Open OWASP Juice Shop through CloudFront
- Confirm the web app loads successfully.
![OWASP Juice Shop accessible](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image12.png)

---

## Part C — Enable GuardDuty (Baseline Detection)

### 13) Open GuardDuty
- Navigate to **GuardDuty** in AWS.
![GuardDuty summary page](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image13.png)

### 14) Enable GuardDuty
- Turn GuardDuty on for your region.
![Enable GuardDuty](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image14.png)

---

## Part D — Attack the Web App (SQLi + Command Injection)

### 15) View Juice Shop product page (target is login/admin access)
![Juice Shop product page](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image15.png)

### 16) Go to the login screen
![Juice Shop login page](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image16.png)

### 17) Perform SQL Injection (bypass login)
- Use a SQL injection payload in the email field.
![SQL injection payload on login](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image17.png)

### 18) Confirm successful admin login challenge
- The success banner confirms login bypass.
![SQL injection success banner](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image18.png)

### 19) Open admin portal / area after login
![Admin portal accessible](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image19.png)

### 20) Identify command injection challenge area
![Command injection section](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image20.png)

### 21) Run command injection
- Send payload to execute server-side command.
![Command injection payload execution](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image21.png)

### 22) Confirm output from command injection
- Output proves arbitrary command execution.
![Command injection result](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image22.png)

---

## Part E — Extract AWS Credentials from the Web App

### 23) Open the credentials page
- View exposed AWS credentials in the app.
![Credentials page showing AWS keys](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image23.png)

### 24) Identify AccessKeyId / SecretAccessKey / Token / Expiration
![Credentials values visible](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image24.png)

---

## Part F — Continue Attack Using AWS CloudShell

### 25) Understand the attacker flow (diagram)
- Shows: CloudFormation → Web app → SQLi/Command Injection → Steal AWS creds → GuardDuty detection.
![Attack chain diagram](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image25.png)

### 26) Open AWS CloudShell
![Open AWS CloudShell](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image26.png)

### 27) Download the credentials JSON using wget
![wget credentials.json](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image27.png)

### 28) Display/format the JSON using cat + jq
![cat + jq to read JSON](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image28.png)

### 29) Extract required fields (AccessKeyId / Secret / Token)
![Extract credentials fields](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image29.png)

---

## Part G — Configure AWS CLI Profile (`stolen`) and Access S3

### 30) Confirm credentials content in CloudShell
![Credentials JSON content](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image30.png)

### 31) Set the stolen profile region
- Example: `aws configure set profile.stolen.region us-east-1`.
![Configure region in stolen profile](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image31.png)

### 32) Configure stolen profile keys and token
- Configure access key, secret key, and session token.
![Configure stolen profile credentials](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image32.png)

### 33) Use AWS CLI to copy sensitive S3 object
- `aws s3 cp s3://<bucket>/secret-information.txt . --profile stolen`.
![Copy secret file from S3](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image33.png)

### 34) Confirm secret data was downloaded
- Viewing the file proves unauthorised data access was possible.
![Secret file content visible](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image34.png)

---

## Part H — Investigate GuardDuty Findings (Credential Abuse Detection)

### 35) Open GuardDuty findings list
![GuardDuty findings list](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image35.png)

### 36) View finding type for credential exfiltration
- Finding: `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS`.
![Credential exfiltration finding type](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image36.png)

### 37) Inspect finding details
- Shows **resource affected** and attacker context.
![Finding details - affected resources](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image37.png)

### 38) Review action performed (S3 GetObject)
![Finding details - GetObject action](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image38.png)

---

## Part I — Enable Malware Protection and Upload EICAR Test File

### 39) Go to Malware Protection in GuardDuty
![Malware protection settings](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image39.png)

### 40) Enable Malware Protection for S3
![Enable malware protection](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image40.png)

### 41) Confirm Malware Protection is enabled
![Malware protection enabled](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image41.png)

### 42) Upload EICAR test file into the S3 bucket
![S3 bucket upload test malware file](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image42.png)

### 43) Confirm the file exists in S3 bucket
![S3 bucket objects include EICAR](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image43.png)

### 44) Verify GuardDuty new finding
- Finding: `Object:S3/MaliciousFile`.
![GuardDuty malicious file finding](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image44.png)

### 45) View finding details
- Confirms GuardDuty detected the EICAR test file.
![Malicious file finding details](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image45.png)

---

## Final Notes / Lessons Learnt
- Web vulnerabilities can escalate into **cloud credential exposure**.
- Temporary credentials can expire, so region + token handling is critical.
- GuardDuty provides strong threat visibility using **ML + anomaly detection**.
- Malware Protection catches suspicious S3 uploads (validated using EICAR).

---

## References
- Screenshots stored in repo: `AWS files/image1.png → image47.png`
