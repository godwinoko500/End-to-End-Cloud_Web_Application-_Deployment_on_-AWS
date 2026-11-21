# aws-vpc-setup
# AWS VPC & Network Infrastructure Setup
A step-by-step guide to provisioning a custom Virtual Private Cloud (VPC), subnet, and Internet Gateway (IGW) on Amazon Web Services (AWS) for a foundational cloud deployment project.

## 📋 Prerequisites

* An active [AWS Account](https://amazon.com)
* Basic familiarity with the AWS Management Console
* Target Region selected (e.g., Africa (Cape Town) `af-south-1`)

---

## 🏗️ Architecture Overview

| Resource | Name | CIDR / Configuration |
| :--- | :--- | :--- |
| **VPC** | `Zl-t-vpc` | `192.168.0.0/16` |
| **Subnet** | `zl-t-Subnet` | `192.168.10.0/24` |
| **Internet Gateway** | `zl-t-IGW` | Attached to `Zl-t-vpc` |

---

## 🚀 Deployment Steps

### 1. Create a Virtual Private Cloud (VPC)
1. Open the **VPC Dashboard** in the AWS Management Console.
2. Click **Create VPC**.
3. Configure the following parameters:
   * **Name tag:** `Zl-t-vpc`
   * **IPv4 CIDR block:** `192.168.0.0/16`
4. Leave all other settings as default and click **Create VPC**.

> **✅ Verification:** Confirm that `Zl-t-vpc` appears as **Available** in your VPC list.

---

### 2. Create a Subnet
1. In the left navigation menu, select **Subnets** → **Create subnet**.
2. Select `Zl-t-vpc` from the **VPC ID** dropdown.
3. Configure the subnet details:
   * **Subnet name:** `zl-t-Subnet`
   * **Availability Zone:** Choose any available zone
   * **IPv4 CIDR block:** `192.168.10.0/24`
4. Click **Create subnet**.

> **✅ Verification:** Ensure `zl-t-Subnet` is created and associated with `Zl-t-vpc`.

---

### 3. Create & Attach an Internet Gateway (IGW)
1. In the left navigation menu, select **Internet Gateways** → **Create internet gateway**.
2. Enter the **Name tag:** `zl-t-IGW` and click **Create internet gateway**.
3. Select the newly created `zl-t-IGW` from the list.
4. Click **Actions** → **Attach to VPC**.
5. Choose `Zl-t-vpc` and click **Attach internet gateway**.

> **✅ Verification:** Confirm the state of `zl-t-IGW` changes from *Detached* to **Attached**.
