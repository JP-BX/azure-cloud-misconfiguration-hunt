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

## Initial CSPM Assessment

## Security Findings

### Finding 1: Shared Key Authentication Enabled

### Finding 2: Public Network Access Enabled

## Remediation

### Remediation 1: Disable Shared Key Authentication

### Remediation 2: Disable Public Network Access

## Post-Remediation Validation

## Before vs. After

## Security Lessons Learned
<!-- redeploy -->
