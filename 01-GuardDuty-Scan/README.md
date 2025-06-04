# 🛡️ AWS GuardDuty – Malware Scanning Lab (On-Demand)

This lab demonstrates how to detect malware infections on Amazon EC2 instances using **Amazon GuardDuty Malware Protection**. The scan was triggered manually via the **on-demand scan** feature, simulating a real-world threat scenario using the [EICAR test file](https://www.eicar.org/).

---

## 🎯 Objectives

- Simulate a malware infection on an EC2 instance using a non-malicious but detectable test file.
- Trigger an on-demand malware scan with Amazon GuardDuty.
- Analyze scan results and understand how AWS detects and isolates infected volumes.
- Practice safe and auditable threat simulation in the cloud.

---

## 🧪 Lab Summary

| Resource                | Value                                          |
|-------------------------|------------------------------------------------|
| **Instance Name**       | `infected-vm`                                  |
| **Instance ID**         | `i-0b48cd87b6234794d`                          |
| **AMI**                 | `Amazon Linux 2023` (`ami-0ae9f87d24d606be4`)  |
| **Instance Type**       | `t2.micro`                                     |
| **Region**              | `us-east-2` (Ohio)                             |
| **Scan Type**           | On-demand                                      |
| **Scan ID**             | `bd6af10b51aebd4dbe1ca6be8e5c5382`             |
| **Result**              | ✅ `Infected`                                  |
| **Files Scanned**       | 41,244                                          |
| **Data Scanned**        | 1.63 GB                                         |
| **Volume ID**           | `vol-0e3d1a2321c53a086`                         |
| **Scan Duration**       | ~8 minutes                                     |
| **Launch Time**         | `2025-06-03T18:10 CST`                          |

---

## 🧱 Architecture

```plaintext
+-------------+     SSH      +---------------+     GuardDuty
|   Analyst   | <----------> | infected-vm   | <---------------------+
+-------------+              | (EC2 t2.micro)|                       |
                             +---------------+                       |
                                    |                                |
                                    +---> EICAR file injected        |
                                    |                                |
                             +---------------+                       |
                             | EBS Volume    |                       |
                             +---------------+                       |
                                    |                                |
                          +----------------------+                  |
                          | GuardDuty Malware Scan| <---------------+
                          +----------------------+
```

## ⚙️ Step-by-Step Execution

1. **Create EC2 Instance**

   AMI: Amazon Linux 2023 (x86_64)  
   Instance type: t2.micro  
   Key pair: malware-lab-key.pem  
   Security Group: Allow SSH (22), HTTP (80)

2. **Connect to Instance**

```bash
chmod 400 malware-lab-key.pem
ssh -i malware-lab-key.pem ec2-user@13.59.196.188
```

3. **Download Malware Simulation File (EICAR)**

```bash
wget https://secure.eicar.org/eicar_com.zip
```

This file is harmless but mimics malware behavior. GuardDuty recognizes it as malicious.

4. **Run On-Demand Scan**

Go to:  
Amazon GuardDuty → Malware Protection → EC2 Malware Scans → Start new on-demand scan  
Enter the instance ARN.

5. **Review Scan Findings**

After ~8 minutes, the scan result appeared as:

- Status: Completed  
- Result: Infected  
- Scan Logs: CloudWatch → /aws/guardduty/malware-scan-logs

---

## 🔬 Sample Findings

| Field           | Value                     |
|------------------|---------------------------|
| Malware Family   | EICAR-Test-File           |
| File Path        | /home/ec2-user/eicar_com.zip |
| Resource Type    | EC2 Instance              |
| Threat Severity  | Medium                    |
| Scan Trigger     | Manual (On-demand)        |

---

## 📸 Screenshots

- Malware Scan Result  
- Scan Details Panel  
- Exported JSON (Scan Log)

---

## 📘 Key Learnings

- GuardDuty can inspect EBS volumes directly without an agent.
- Malware scans can be triggered manually or automatically.
- Suspicious volumes are logged and indexed in CloudWatch.

---

## 📌 Security Considerations

- Instance deployed in isolated VPC/subnet.
- Used EICAR test file – not actual malware.
- Instance terminated after scan; volume not reused.
- SSH access limited to analyst IP.
