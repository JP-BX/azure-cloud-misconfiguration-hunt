# Azure Cloud Misconfiguration Hunt

## Project Overview

In this project, I performed a Cloud Security Posture Management (CSPM) assessment of a Microsoft Azure environment using Prowler. The goal was to identify cloud security misconfigurations, understand the risks associated with the findings, remediate selected high-severity issues, and validate the changes through a post-remediation security scan.

I deployed an Azure Storage Account in a dedicated lab resource group and configured Prowler in a Kali Linux virtual machine using Docker. Prowler authenticated to Azure using a Microsoft Entra ID service principal with Reader permissions.

The initial security assessment identified multiple configuration findings. I focused on two high-severity Azure Storage findings:

- Shared Key authentication was enabled.
- Public network access was enabled.

I remediated both findings through the Azure Portal and then performed a second Prowler scan to verify that the affected security checks changed from **FAIL** to **PASS**.

## Technologies Used

- Microsoft Azure
- Microsoft Entra ID
- Azure Storage
- Prowler
- Docker
- Kali Linux
- Azure CLI
- Cloud Security Posture Management (CSPM)

## Lab Architecture

The lab was designed to simulate a basic cloud security assessment workflow. I used a Kali Linux virtual machine as my security workstation and Docker to run Prowler. Prowler authenticated to Microsoft Azure using a Microsoft Entra ID service principal with Reader permissions scoped to the lab resource group.

The architecture followed this flow:

**Kali Linux → Docker → Prowler → Microsoft Entra ID Service Principal → Microsoft Azure → Azure Storage Account**

This setup allowed Prowler to securely assess the Azure environment for security misconfigurations without giving the scanning account permission to modify cloud resources.

## Prowler Setup

I used Prowler as the Cloud Security Posture Management (CSPM) tool for this assessment. Prowler is an open-source cloud security assessment tool that evaluates cloud environments for security misconfigurations and compliance-related findings.

Prowler was executed from my Kali Linux virtual machine using Docker. Running Prowler in a Docker container provided a consistent environment without requiring the Prowler dependencies to be installed directly on the Kali system.

I verified that the Prowler Docker image was installed and operational before beginning the Azure assessment. The lab used **Prowler v5.41.0**.
<img width="763" height="427" alt="Screenshot 2026-09-01 195214" src="https://github.com/user-attachments/assets/dc66844e-9ad0-4245-a648-ef553d2996bd" />

## Azure Environment

For the assessment, I created a dedicated Azure lab environment containing an Azure Storage Account named **stcloudsecuritylab01** inside the **rg-cloud-security-lab** resource group.

The storage account was intentionally left with several settings that Prowler could evaluate. Before remediation, **Storage Account Key Access** and **Public Network Access** were enabled.

This provided a controlled environment where I could identify cloud security risks, apply security hardening changes, and validate the results without affecting a production environment.
<img width="1556" height="818" alt="Screenshot 2026-09-01 210226" src="https://github.com/user-attachments/assets/947bcfaa-98f0-4e46-bb51-a6ce4016b3f1" />

## Initial CSPM Assessment

After configuring authentication, I executed Prowler against the Azure environment using a Microsoft Entra ID service principal. The service principal was assigned **Reader** permissions at the lab resource group scope, following the principle of least privilege.

Prowler successfully authenticated to Azure and executed **188 security checks** against the environment. These checks evaluated Azure resources for security misconfigurations, insecure configurations, and alignment with security and compliance frameworks. 

The initial scan established a baseline of the environment's security posture before any remediation changes were made.
<img width="1068" height="657" alt="Screenshot 2026-09-01 225157" src="https://github.com/user-attachments/assets/501daf2a-6447-4321-a093-067443dfa531" />

## Security Findings

### Finding 1: Shared Key Authentication Enabled

Prowler identified that **Shared Key authentication was enabled** on the Azure Storage Account. This finding was classified as **HIGH severity**.

Azure Storage Shared Key authentication uses storage account access keys to authorize access to storage resources. Because these keys can provide broad access to the storage account, exposure or improper management of a key can create significant security risk.

Using identity-based authentication through **Microsoft Entra ID and Azure RBAC** provides more granular access control and reduces reliance on long-lived shared credentials.

Prowler reported the following security check as failed:
**Check:** `storage_account_key_access_disabled`
**Severity:** HIGH
**Initial Status:** FAIL
<img width="1890" height="543" alt="Screenshot 2026-09-01 231618" src="https://github.com/user-attachments/assets/c1661ae4-7bfb-445b-b895-414aedbccef4" />

### Finding 2: Public Network Access Enabled

Prowler identified that **Public Network Access was enabled** on the Azure Storage Account. This finding was classified as **HIGH severity**.

Allowing public network access means the storage account's public endpoint can be reached over the internet. Even when authentication is still required, exposing a cloud resource publicly increases its attack surface compared with restricting access to approved private or trusted network paths.

In a production environment, application dependencies and connectivity requirements would need to be reviewed before disabling public access. In this controlled lab environment, I was able to restrict the storage account's network exposure without affecting production services.

Prowler reported the following security check as failed:

**Check:** `storage_account_public_network_access_disabled`  
**Severity:** HIGH  
**Initial Status:** FAIL

## Remediation

After reviewing the Prowler findings and understanding the associated risks, I remediated the two selected high-severity findings through the Microsoft Azure Portal.

The goal was not simply to make the Prowler checks pass, but to reduce unnecessary authentication and network exposure while applying cloud security hardening principles.

### Remediation 1: Disable Shared Key Authentication

To address the Shared Key authentication finding, I changed the Azure Storage Account configuration to disable **Storage Account Key Access**.

Disabling Shared Key authentication reduces reliance on long-lived storage account keys and encourages the use of identity-based authentication through **Microsoft Entra ID and Azure RBAC**.

After applying the change, I performed another Prowler scan to verify that the security control had been successfully implemented.
<img width="781" height="762" alt="Screenshot 2026-09-01 232900" src="https://github.com/user-attachments/assets/96734f32-f08f-495e-a187-6a1486741ade" />

### Remediation 2: Disable Public Network Access

To address the network exposure finding, I disabled **Public Network Access** on the Azure Storage Account.

This change prevents the storage account from being accessed through its public network endpoint, reducing the resource's exposure to the internet. Restricting public access helps reduce the attack surface and supports a more controlled network security model.

In a production environment, I would first verify application dependencies and connectivity requirements before disabling public access to avoid disrupting legitimate services.
<img width="833" height="484" alt="Screenshot 2026-09-01 233511" src="https://github.com/user-attachments/assets/1a96a1fb-74a8-4b6c-9675-d965980b15f6" />

## Post-Remediation Validation

After implementing the security changes, I performed a second Prowler assessment against the Azure environment.
The purpose of the second scan was to validate that the configuration changes actually resolved the identified security findings rather than assuming the remediation was successful.
The post-remediation assessment confirmed that both selected high-severity checks changed from **FAIL** to **PASS**.

### Shared Key Authentication Validation

The second Prowler scan confirmed that Shared Key authentication had been successfully disabled.

**Check:** `storage_account_key_access_disabled`  
**Severity:** HIGH  
**Before:** FAIL  
**After:** PASS

### Public Network Access Validation

The second Prowler scan confirmed that public network access had been successfully disabled.

**Check:** `storage_account_public_network_access_disabled`  
**Severity:** HIGH  
**Before:** FAIL  
**After:** PASS
<img width="2167" height="726" alt="results" src="https://github.com/user-attachments/assets/eba8faa7-342a-4a1d-8e72-bc84001957c0" />

## Before vs. After
## Before vs. After

The results of the assessment demonstrated the complete CSPM remediation workflow: identify a security misconfiguration, understand the associated risk, apply a security control, and validate the remediation through a second security assessment.

| Security Check | Before | After |
| --- | --- | --- |
| Shared Key Authentication | ❌ FAIL | ✅ PASS |
| Public Network Access | ❌ FAIL | ✅ PASS |

Both selected **HIGH-severity** Azure Storage findings were successfully remediated and verified through the post-remediation Prowler scan.

## Security Lessons Learned

This project reinforced the importance of treating cloud security as a continuous process rather than a one-time configuration task. Effective Cloud Security Posture Management requires identifying exposure, understanding the associated risk, implementing appropriate controls, and validating that remediation actually resolved the finding.

The assessment demonstrated how **Prowler** can provide visibility into Azure security posture by evaluating cloud resources against security best practices and compliance-related controls.

Authentication for the assessment was implemented using a **Microsoft Entra ID service principal** with **Reader permissions scoped to the lab resource group**, demonstrating the use of Azure RBAC and least-privilege access for security tooling.

The remediation process addressed two high-severity areas: **authentication security and network exposure**. Disabling Shared Key authentication reduced reliance on broadly privileged storage credentials, while disabling Public Network Access reduced the externally accessible attack surface of the storage account.

A key part of the project was post-remediation validation. Rather than assuming the configuration changes resolved the findings, I performed a second Prowler assessment and verified that both targeted controls transitioned from **FAIL to PASS**.

Overall, this project demonstrated a practical cloud security workflow:

**Assess → Identify Risk → Remediate → Validate**

<!-- redeploy -->
