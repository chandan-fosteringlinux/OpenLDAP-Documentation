# **Setting Up HAProxy Load Balancer for LDAP**

---

## **1\. Overview**

* **HAProxy** is an open-source load balancer.  
* It helps distribute traffic across multiple servers, enhancing **reliability** and **performance**.  
* This guide covers setting up HAProxy to balance traffic between multiple **LDAP servers**.

---

## **2\. Requirements**

* Two or more **LDAP servers** (e.g., VM1, VM2).  
* HAProxy installed on a machine that will act as the **load balancer**.  
* **Root/Admin access** for configuration.

---

## **3\. Steps to Set Up HAProxy**

### **Step 1: Install HAProxy**

Run the following commands to install HAProxy:

`sudo apt-get update`

`sudo apt-get install haproxy -y`

### **Step 2: Configure HAProxy**

* Open the HAProxy configuration file:

`sudo nano /etc/haproxy/haproxy.cfg`

* Add the following configuration:

``` 
global
    log /dev/log    local0
    log /dev/log    local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log global
    mode tcp  # Suitable for LDAP
    timeout connect 10s
    timeout client 1m
    timeout server 1m
    option tcplog  # Log for debugging
frontend ldap_frontend
    bind *:8404  # Listening on port 8404 for LDAP traffic
    default_backend ldap_backend

backend ldap_backend
    balance roundrobin  # Distribute requests evenly
    server ldap1 192.168.122.218:389 check  # LDAP Server 1
    server ldap2 192.168.122.93:389 check  # LDAP Server 2
    
frontend stats_frontend
    bind *:8405  # Stats page on port 8405
    mode http
    stats enable
    stats uri /haproxy?stats
    stats refresh 30s
    stats admin if LOCALHOST 
```

### **Explanation:**

* **frontend:** Handles incoming LDAP traffic on port **8404**.  
* **backend:** Defines the LDAP servers to load balance (VM1, VM2).  
* **balance roundrobin:** Distributes requests evenly across servers.  
* **stats\_frontend:** Exposes a monitoring page for HAProxy at **port 8405**.

---

### **Step 3: Restart HAProxy**

After making changes, restart HAProxy:

`sudo systemctl restart haproxy`

Check HAProxy’s status to ensure it’s running:

`sudo systemctl status haproxy`

---

### **Step 4: Test the HAProxy Setup**

* Use the **ldapsearch** command to verify HAProxy is balancing LDAP traffic correctly:

`Ldapsearch -x -H ldap://localhost:8404 -D "cn=Manager,dc=kna,dc=in" -w chandan -b "dc=kna,dc=in"`

* Access the HAProxy stats page by visiting:

`http://localhost:8405/haproxy?stats`

---

## **4\. Conclusion**

By following these steps:

* **HAProxy** will balance traffic between your LDAP servers.
* If one server goes down, traffic will be automatically redirected to other servers.  
* You can also monitor HAProxy’s performance via the **stats page**.

