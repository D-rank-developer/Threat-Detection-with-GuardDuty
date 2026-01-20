# Threat Detection with Amazon GuardDuty (OWASP Juice Shop)

## Tools Used
- **Amazon GuardDuty**
- **Amazon CloudFront**
- **Amazon S3**
- **AWS CloudFormation**
- **OWASP Juice Shop**

---

## Project Goals Achieved
- Deployed a full web app environment using **CloudFormation** (VPC, EC2 web server, S3, CloudFront).
- Simulated realistic web attacks on **OWASP Juice Shop** (SQL injection + command injection).
- Demonstrated how exposed **instance credentials** can be abused to access **S3**.
- Verified **GuardDuty findings** for credential exfiltration and suspicious AWS activity.
- Enabled **GuardDuty Malware Protection**, uploaded an **EICAR** test file, and confirmed an **S3 malware finding**.
- Documented the full attacker-to-defender workflow with screenshots for a portfolio-ready GitHub repo.

---

## Why GuardDuty Matters (and its Machine Learning Value)
GuardDuty is essential in cloud security because it continuously monitors AWS activity (e.g., API calls, network flow, DNS) and raises **findings** when behaviour looks suspicious.  
Its detection includes **anomaly detection** (behaviour baselining), **threat intelligence**, and **pattern-based detections**, helping security teams identify compromised credentials, unusual API access, and malware-related events quickly.

---

# Step-by-Step Walkthrough (Images 1–47)

> The screenshots below are in the exact order **image1 → image47**.

---

## 1) Deploy the environment with CloudFormation

- **Open CloudFormation** and start creating the stack from the provided template.  
![CloudFormation - start stack creation](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image1.png)

- **Confirm the stack parameters** and proceed.  
![CloudFormation - confirm parameters](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image2.png)

- **Create the stack** and wait for resources to deploy.  
![CloudFormation - create stack](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image3.png)

- **Verify stack status = CREATE_COMPLETE**.  
![CloudFormation - stack complete](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image4.png)

- **Review the deployed resources** (VPC, EC2, S3, CloudFront).  
![CloudFormation - resources created](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image5.png)

---

## 2) Access OWASP Juice Shop through CloudFront

- **Open the CloudFront distribution URL** created by the stack.  
![CloudFront distribution URL](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image6.png)

- **Confirm Juice Shop loads successfully** in the browser.  
![OWASP Juice Shop loaded](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image7.png)

- **Locate the login page** (this is the target for testing).  
![Juice Shop login page](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image8.png)

---

## 3) Enable GuardDuty (threat detection baseline)

- **Open GuardDuty** in the AWS console.  
![GuardDuty console](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image9.png)

- **Enable GuardDuty** for the account/region used in this lab.  
![Enable GuardDuty](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image10.png)

- **Confirm GuardDuty is active** and ready to generate findings.  
![GuardDuty enabled](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image11.png)

---

## 4) Attack #1 — SQL Injection (bypass login)

- **Attempt SQL injection** on the login form using a crafted payload.  
![SQL injection attempt](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image12.png)

- **Verify authentication is bypassed** (login success without valid credentials).  
![SQL injection success](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image13.png)

- **Confirm the app session is active** after bypassing login.  
![Authenticated session after SQLi](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image14.png)

---

## 5) Attack #2 — Command Injection (execute server-side commands)

- **Navigate to the vulnerable feature** used for command injection testing.  
![Command injection target feature](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image15.png)

- **Insert a command injection payload** into the vulnerable input.  
![Command injection payload](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image16.png)

- **Verify command execution output** returned by the application.  
![Command injection output](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image17.png)

---

## 6) Extract AWS credentials exposed by the web app

- **Open the credentials/diagnostics page** inside Juice Shop after exploitation.  
![Credentials page](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image18.png)

- **Identify AccessKeyId, SecretAccessKey, Token, Expiration** in the output.  
![AWS keys and token displayed](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image19.png)

- **Copy the credentials safely for the lab test** (do not share publicly).  
![Copy credentials for lab](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image20.png)

---

## 7) Continue the attack in AWS CloudShell (outsider simulation)

- **Open AWS CloudShell** to run attacker-style AWS CLI commands.  
![Open CloudShell](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image21.png)

- **Confirm CloudShell starts successfully** (terminal ready).  
![CloudShell terminal ready](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image22.png)

- **Download credentials.json using wget** (from the lab-provided location).  
![wget credentials.json](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image23.png)

- **Use cat + jq to read credentials.json cleanly** (format JSON output).  
![cat and jq to read JSON](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image24.png)

---

## 8) Configure a new AWS CLI profile called `stolen`

- **Create the profile** named `stolen`.  
![Create stolen profile](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image25.png)

- **Set AccessKeyId and SecretAccessKey** for the profile.  
![Configure access key and secret](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image26.png)

- **Set the Session Token** (required for temporary credentials).  
![Configure session token](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image27.png)

- **Set the correct default region** for the profile.  
![Configure region](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image28.png)

- **Verify the profile identity** using STS.  
![sts get-caller-identity](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image29.png)

---

## 9) Use stolen credentials to access S3 data

- **Check the bucket location** (region must match to avoid errors).  
![get-bucket-location](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image30.png)

- **List the bucket contents** to confirm access.  
![list S3 bucket objects](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image31.png)

- **Copy the secret file from S3** into CloudShell.  
![copy secret file from S3](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image32.png)

- **Open the downloaded file** to verify sensitive data exposure.  
![view the secret file](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image33.png)

---

## 10) Review GuardDuty Findings (detection and investigation)

- **Open GuardDuty Findings** after performing the credential abuse.  
![GuardDuty findings list](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image34.png)

- **Locate the finding type**: `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS`.  
![Credential exfiltration finding type](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image35.png)

- **Review the affected resource** (IAM role / instance credentials).  
![Affected resource details](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image36.png)

- **Review the action** (S3 object retrieval).  
![Action - GetObject](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image37.png)

- **Review attacker details** (location/IP—often shown as AWS-based when using CloudShell).  
![Attacker location and IP](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image38.png)

---

## 11) Extra: Enable GuardDuty Malware Protection

- **Open Malware Protection settings** in GuardDuty.  
![GuardDuty malware protection settings](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image39.png)

- **Select Enable** to turn on malware scanning.  
![Enable malware protection](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image40.png)

- **Confirm Malware Protection is enabled**.  
![Malware protection enabled](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image41.png)

---

## 12) Upload a malware test file (EICAR) and verify detection

- **Prepare the EICAR test file** for upload (safe test file).  
![EICAR test file ready](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image42.png)

- **Upload the EICAR file to the S3 bucket**.  
![Upload EICAR to S3](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image43.png)

- **Wait briefly and refresh GuardDuty findings**.  
![Refresh findings](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image44.png)

- **Confirm finding type**: `Object:S3/MaliciousFile`.  
![S3 malicious file finding](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image45.png)

- **Open the finding details** and read the simplified description (info icon).  
![Finding details and info description](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image46.png)

- **Final verification screenshot** showing successful detection and project completion.  
![Final verification](https://github.com/D-rank-developer/Threat-Detection-with-GuardDuty/blob/3bb9fc455b6eb2ec7d399ab45a8053d74c175f56/AWS%20files/image47.png)

---

## Key Takeaways
- Web app vulnerabilities can lead to **credential exposure**, which can quickly escalate into **cloud data access** (S3).
- GuardDuty helps by detecting **unusual behaviour** (anomaly detection) and generating **findings** for investigation.
- Malware Protection adds an extra layer by identifying malicious uploads to S3 (validated using **EICAR**).

---

## References
- Screenshots are stored in the repo under `AWS files/` (image1 → image47).
