1. KVM installation source :- https://ubuntu.com/blog/kvm-hyphervisor

   ## **KVM Installation on Ubuntu**

   ### **1\. Update Package Index**

* **Command:** `sudo apt update`  
* **Explanation:** This step ensures that the system’s package manager is aware of the latest available software versions and updates from the repositories.  
  ---

  ### **2\. Verify Virtualization Support**

* **Command:** `egrep -c '(vmx|svm)' /proc/cpuinfo`  
* **Explanation:** This checks if hardware virtualization is enabled on the system. The output should be greater than 0 to confirm that the CPU supports either Intel VT-x (vmx) or AMD-V (svm).  
* **Important:** If virtualization is not enabled, it must be turned on in the BIOS.  
  ---

  ### **3\. Install KVM and Related Packages**

1. **Command:**  
   bash  
   Copy code  
   `sudo apt install -y qemu-kvm virt-manager libvirt-daemon-system virtinst libvirt-clients bridge-utils cpu-checker`  
*   
* **Explanation:** This command installs the KVM hypervisor, along with essential tools like Virt-Manager (for GUI management), `libvirt` (virtualization library), and `bridge-utils` (for networking). It also includes the CPU checker for hardware compatibility.  
  ---

  ### **4\. Verify KVM Compatibility**

* **Command:** `kvm-ok`  
* **Explanation:** This checks whether the system supports and is ready to use KVM. A positive confirmation ensures that KVM can be used for virtualization.  
  ---

  ### **5\. Start and Enable the libvirt Service**

2. **Command:**  
   bash  
   Copy code  
   `sudo systemctl enable --now libvirtd`  
3. `sudo systemctl start libvirtd`  
* **Explanation:** This starts the `libvirtd` service, which manages virtual machines, and ensures it starts automatically on boot.  
  ---

  ### **6\. Add User to the kvm and libvirt Groups**

4. **Command:**  
   bash  
   Copy code  
   `sudo usermod -aG kvm $USER`  
5. `sudo usermod -aG libvirt $USER`  
* **Explanation:** Adding the user to the `kvm` and `libvirt` groups allows non-root users to manage KVM without needing superuser privileges. This is crucial for user access control and security.  
  ---

  ### **7\. Reboot or Re-login**

* **Action:** Log out and log back in or reboot the system.  
* **Explanation:** This step ensures that the group changes take effect, granting the user necessary permissions to manage virtual machines.  
  ---

  ### **Conclusion**

* **Key Point:** By following these steps, we’ve successfully installed and configured KVM on Ubuntu, enabling us to create and manage virtual machines efficiently.  
* **Impact:** KVM is now fully operational, and the system is ready for virtualization workloads.