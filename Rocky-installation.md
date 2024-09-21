**Setting Up Rocky Linux in KVM**

1\. Prerequisites

* **Ensure KVM is Installed:**  
  * Verify that KVM and necessary packages (qemu-kvm, libvirt, etc.) are installed.  
* **Check Virtualization:**  
  * Ensure your CPU supports virtualization and it's enabled in the BIOS.
  ---

 2\. Download Rocky Linux ISO

 **URL:** [Rocky Linux Downloads](https://rockylinux.org/download)  
  ---

3\. Create a Virtual Machine in KVM  
* **Open Virt-Manager:**  
  * Launch the Virt-Manager GUI.  
* **Create New Virtual Machine:**  
  * Click on "Create a new virtual machine."  
* **Select Installation Method:**  
  * Choose "Local installation media (ISO image or CDROM)."  
* **Choose the ISO File:**  
  * Browse to the downloaded Rocky Linux ISO file.
  ---

4\. Configure VM Settings

* **Set Memory and CPU:**  
  * Allocate sufficient RAM and CPU cores based on your requirements (e.g., 2 GB RAM, 2 CPU cores).  
* **Storage Configuration:**  
  * Create a new virtual disk (e.g., 50 GB in my case).  
* **Network Configuration:**  
  * Select a network bridge (e.g., `br0`) or NAT based on your network needs.
  ---

5\. Begin Installation

* **Start the VM:**  
  * Power on the virtual machine.  
* **Follow Installation Prompts:**  
  * Select the installation language.  
  * Choose "Install Rocky Linux" and proceed with the installation wizard.
  ---

6\. Configure Rocky Linux

* **Set Root Password:**  
  * Configure the root password as prompted.  
* **Create User:**  
  * Set up a non-root user account.  
* **Select Installation Destination:**  
  * Choose the virtual disk for installation.  
* **Network Configuration:**  
  * Set up network parameters if needed.  
* **Begin Installation:**  
  * Click "Begin Installation" to start the process.
  ---

7\. Post-Installation Steps

* **Reboot the VM:**  
  * Once the installation is complete, reboot the virtual machine.  
* **Login:**  
  * Use the created user credentials to log in.  
* **Update System:**  
  * Run the following command to ensure all packages are up to date:  
    `sudo dnf update`  
  ---

  
