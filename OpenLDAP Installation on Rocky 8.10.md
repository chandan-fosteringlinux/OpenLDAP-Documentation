## **OpenLDAP Installation on Rocky 8.10**
OpenLDAP is an open-source implementation of the Lightweight Directory Access Protocol (LDAP) used for accessing and managing directory information services over a network.

### **1\. Enable EPEL Repository**

**Command:**  
`sudo dnf install epel-release`

* **Purpose:** Install the Extra Packages for Enterprise Linux (EPEL) repository to access additional packages.

---

 ### **2\. Verify EPEL Repository**

  **Command:**  
  `dnf repolist`  
* **Purpose:** Check that the EPEL repository is enabled and available for use.

  ---

 ### **3\. Update System Packages**

  **Command:**  
  `sudo dnf update -y`  
* **Purpose:** Ensure all installed packages are up to date before proceeding. 

  ---

 ### **4\. Configure LTB Project Repository**

  **Command:**  
  `nano /etc/yum.repos.d/ltb-project.repo`  
  **Add the following configuration:**  
  `[ltb-project]`  
  `name=LTB project packages`  
  `baseurl=https://ltb-project.org/rpm/openldap26/$releasever/$basearch`  
  `enabled=1`  
  `gpgcheck=1`  
  `gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-LTB-project`  
  
* **Purpose:** Add the LTB project repository to access OpenLDAP packages.  

  ---

 ### **5\. Import GPG Key for LTB Project**

  **Command:**  
  `rpm --import https://ltb-project.org/documentation/_static/RPM-GPG-KEY-LTB-PROJECT-SECURITY`  
* **Purpose:** Import the GPG key for package signing verification.  

  ---

 ### **6\. Install OpenLDAP and Related Packages**

  **Command:**  
  `yum install openldap-ltb openldap-ltb-contrib-overlays openldap-ltb-mdb-utils`  
* **Purpose:** Install the main OpenLDAP packages and additional utilities.  

  ---

 ### **7\. Install OpenLDAP Clients**

  **Command:**  
  `yum install openldap-clients`  
* **Purpose:** Install the OpenLDAP client utilities for interacting with the LDAP server.  
