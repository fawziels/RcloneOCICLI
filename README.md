# OCI CLI Setup for Rclone on Windows

This guide helps you set up [Oracle Cloud Infrastructure (OCI) CLI](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm) on Windows and configure [Rclone](https://rclone.org/) to interact with OCI Object Storage. It also walks through automating file backups to OCI buckets using Windows Task Scheduler.

## 🧰 Prerequisites

- A Windows machine (Windows Server 2012/2016 or later)
- Administrator access to PowerShell
- An active Oracle Cloud Infrastructure account
- OCIDs for your user, tenancy, and dynamic group (if applicable)

---

## 📦 Installing OCI CLI on Windows

1. **Run PowerShell as Administrator**

2. **Enable script execution:**
   ```powershell
   Set-ExecutionPolicy RemoteSigned
   ```

3. **Force TLS 1.2 support (for older Windows versions):**
   ```powershell
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   ```

4. **Download and run the installer:**
   - With prompts:
     ```powershell
     iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.ps1'))
     ```
   - Without prompts (accepts all defaults):
     ```powershell
     ./install.ps1 -AcceptAllDefaults
     ```

5. **Verify the installation:**
   ```powershell
   oci --version
   ```

6. **Set up your OCI CLI config:**
   ```powershell
   oci setup config
   ```

   Example config (`~/.oci/config`):
   ```
   [DEFAULT]
   user=ocid1.user.oc1..<unique_ID>
   fingerprint=<your_fingerprint>
   key_file=~/.oci/oci_api_key.pem
   tenancy=ocid1.tenancy.oc1.<unique_ID>
   region=us-ashburn-1
   ```

7. **Test CLI setup:**
   ```powershell
   oci iam compartment list
   ```

---

## 🔐 Permissions for Rclone Access

1. **Create a Dynamic Group in OCI Console:**
   ```
   All {instance.id = '<your_instance_OCID>'}
   ```

2. **Create IAM Policies:**
   ```
   Allow dynamic-group <rclone-instances> to manage buckets in tenancy
   Allow dynamic-group <rclone-instances> to manage objects in tenancy
   ```

---

## 🔗 Configuring Rclone for OCI

1. **Run Rclone configuration:**
   ```bash
   rclone config
   ```

2. **Example Rclone usage:**
   - List buckets:
     ```bash
     rclone lsd oci-remote:
     ```
   - List objects in a bucket:
     ```bash
     rclone ls oci-remote:bucket-name
     ```
   - Copy local files to a bucket:
     ```bash
     rclone copy "C:\local\path" "oci-remote:bucket-name"
     ```

---

## ⚙️ Automating Backups with Task Scheduler

1. **Create a `.bat` file with your Rclone command:**
   ```bat
   rclone copy "C:\local\backup" "oci-remote:bucket-name"
   echo Sync complete
   ```

2. **Schedule the backup using Task Scheduler:**
   - Open Task Scheduler (`taskschd.msc`)
   - Click **Create Basic Task**
   - Set the trigger (e.g., daily, weekly)
   - Action: **Start a Program**, select your `.bat` file
   - Finish the wizard and test the task

---

## 📘 Summary

This setup enables:
- Secure and automated backup to Oracle Cloud Object Storage
- Integration between Rclone and OCI via CLI
- Automation of backup tasks using native Windows tools

Feel free to fork, adapt, or suggest improvements!

---
