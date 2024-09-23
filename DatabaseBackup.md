# **LDAP Backup Automation**

---

## **1\. Overview**

This document outlines the process for backing up an LDAP database and automating the backup using a bash script and cron jobs. The backup script creates a compressed `.ldif.gz` file containing the entire LDAP directory. Cron will be used to schedule automatic backups every 2 hours.

---

## **2\. Backup Script Details**

The backup script utilizes `ldapsearch` to extract LDAP data and compresses it using `gzip`. Below is the script:

`#!/bin/bash`

`# Directory to store backups`

`ldapsearch -H ldap://localhost:389 -D "cn=Manager,dc=my-domain,dc=com" -w 1122 -b "dc=my-domain,dc=com" -LLL | gzip > /home/amaresh2/Desktop/backup_$(date +%Y%m%d_%H%M%S).ldif.gz`

### **File Name Format:**

The backup file will be saved in the specified directory with the filename format:  
`backup_YYYYMMDD_HHMMSS.ldif.gz`

* This format includes the date and time of the backup for easy identification.

---

## **3\. Automating Backups with Cron**

To automate the backup process, we use **cron**, a time-based job scheduler in Unix-like systems. Below is the cron job configuration:

`0 */2 * * * /backup/backup_script/backup_script.sh`

### **Cron Job Details:**

* **Schedule:** Runs in every 2 hours.  
* **Execution:** The specified script path is executed automatically by cron.

---

## **4\. Steps to Set Up the Backup Script**

### **Step 1: Create the Backup Script**

1. Save the backup script as `backup_script.sh` in the directory `/backup/backup_script/`.

Make the script executable:  
`chmod +x /backup/backup_script/backup_script.sh`

### **Step 2: Set Up the Cron Job**

Open the cron editor:

`crontab -e`

Add the following line at the end of the file to schedule the backup:  
`0 */2 * * * /backup/backup_script/backup_script.sh`

Save and exit the editor. Cron will now automatically execute the backup script every 2 hours.

---

## **5\. Restoring Data from Backup**

To restore the LDAP data from a backup, use the following command:

`gunzip -c /home/amaresh2/Desktop/backup_YYYYMMDD_HHMMSS.ldif.gz | ldapadd -x -H ldap://192.168.122.230:389 -D "cn=Manager,dc=kna,dc=in" -w chandan`

### **Explanation:**

* **gunzip \-c:** Decompresses the backup file while outputting the content.  
* **ldapadd:** Adds the entries back to the LDAP server.

### **Note:**

Replace `YYYYMMDD_HHMMSS` with the actual timestamp of the backup you want to restore.

---

## **6\. Conclusion**

With this setup:

* Backups of the LDAP database will automatically be created every 2 hours.  
* The backups will be saved in a compressed `.ldif.gz` format, ensuring data preservation while minimising storage requirements.  
* You can easily restore the data using the provided command.
