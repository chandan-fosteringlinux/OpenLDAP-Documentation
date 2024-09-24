# **OpenLDAP Configuration**

## **1\. Introduction to slapd.conf**

The `slapd.conf` file is essential for configuring the OpenLDAP server (slapd daemon). 

* **Database Management:** Defines how data is stored and accessed.  
* **Access Control:** Sets permissions for users and groups.  
* **Replication Settings:** Configures data synchronization between servers.
* **Path To Configuration File:** /usr/local/openldap/etc/openldap/slapd.conf

## **2\. Schema Inclusions**

Schemas are crucial as they define the structure of directory entries. Our configuration includes:

```
include /usr/local/openldap/etc/openldap/schema/core.schema
include /usr/local/openldap/etc/openldap/schema/cosine.schema  
include /usr/local/openldap/etc/openldap/schema/inetorgperson.schema  
include /usr/local/openldap/etc/openldap/schema/nis.schema
 ```
    

These schemas provide standard attributes for user accounts and organizational units (OUs).

## **3\. PID and Args Files**

* **PID File:** `pidfile /usr/local/openldap/var/run/slapd.pid` stores the server's process ID.  
* **Args File:** `argsfile /usr/local/openldap/var/run/slapd.args` logs the server's startup parameters.

 ## **4\. Module Loading**

Modules extend the server's functionality. Our configuration specifies:

``` 
modulepath /usr/local/openldap/libexec/openldap  
moduleload back_mdb.la  
moduleload syncprov.la
```

* **Module Path:** Indicates where dynamic backend modules are stored.  
* **Loaded Modules:** `back_mdb` for modern databases and `syncprov` for synchronization.

 ## **5\. Database Configuration**

The configuration manages internal databases:

```
database config  
access to * by dn.exact="cn=Manager,dc=kna,dc=in" write by * read  
```

This allows the Manager to make configuration changes while ensuring read access for others.

## **6\. MDB Database Definitions**

```
database mdb  
suffix "dc=kna,dc=in"  
rootdn "cn=Manager,dc=kna,dc=in"  
rootpw chandan
directory /usr/local/openldap/var/openldap-data
```
      
* **MDB:** This is a fast and efficient database type.  
* **Suffix:** Defines the root of our directory tree.  
* **Manager Credentials:** Specifies the Manager user and password.

 ## **7\. Indexing**


`index objectClass eq`  
    
Indexing enhances search efficiency on the `objectClass` attribute.

## **8\. Replication Configuration**

**Server 1 Replication Configuration**

```
serverID 1
overlay       syncprov
syncprov-checkpoint     100      10
syncprov-sessionlog     100    

syncrepl     rid=001
             provider=ldap://<Server2_IP>:389
             type=refreshAndPersist
             interval=00:00:01:00
             searchbase="dc=kna,dc=in"
             scope=sub
             schemachecking=off
             bindmethod=simple
             binddn="cn=Manager,dc=kna,dc=in"
             credentials=chandan
mirrormode TRUE
``` 

**Server 2 Replication Configuration**

```
serverID 2
overlay       syncprov
syncprov-checkpoint     100      10
syncprov-sessionlog     100    

syncrepl     rid=002
             provider=ldap://<Server1_IP>:389
             type=refreshAndPersist
             interval=00:00:01:00
             searchbase="dc=kna,dc=in"
             scope=sub
             schemachecking=off
             bindmethod=simple
             binddn="cn=Manager,dc=kna,dc=in"
             credentials=chandan

mirrormode TRUE
```    

The provided configuration sets up an LDAP replication using the syncprov overlay, enabling a refresh-and-persist sync mode from a specified provider, with session logging and checkpointing to manage replication state, while mirroring changes in real-time.


## **9\. Monitor Database**

`database monitor`

This database tracks server performance and status.

## **10\. Validation and Dynamic Configuration**

### **Validating Configuration**

* **Command:** `slaptest -u`  
  * This checks for any syntax errors or configuration issues in the `slapd.conf` file, ensuring the server starts smoothly.

### **Transitioning to Dynamic Configuration**

* **Command:** `slaptest -f /etc/openldap/slapd.conf -F /etc/openldap/slapd.d`  
  * This command validates our configuration file and generates dynamic configuration files in `/etc/openldap/slapd.d`.

## **11\. Setting Permissions**

**Command:** `chown -R ldap: /etc/openldap/slapd.d`  

This command changes the ownership of configuration files to the LDAP user, enhancing security and ensuring only the server process can modify them.

## **Conclusion**

The `slapd.conf` file orchestrates the entire functionality of our OpenLDAP server, from database management to access control and replication.

---

# **User and Organizational Unit Management in LDAP**

## **1\. LDAP Structure Overview**

### **Organizational Units (OUs)**

OUs help organize users based on roles or departments, such as:

**Contractor:** `ou=contractor,dc=kna,dc=in`  
**Intern:** `ou=intern,dc=kna,dc=in`  
**Employee:** `ou=employee,dc=kna,dc=in`

### **User Entries**

Each user entry includes attributes like `uid`, `cn`, `sn`, `mail`, and `userPassword`.

## **2\. Creating Base DN and Organizational Units**

### **Example Base DN Creation**
```
 ldapadd -x -D "cn=Manager,dc=kna,dc=in" -W <<EOF
  dn: dc=kna,dc=in
  objectClass: domain
  dc: kna
  EOF
``` 

### **Example OU Creation**
```
 ldapadd -x -D "cn=Manager,dc=kna,dc=in" -W <<EOF
  dn: ou=contractor,dc=kna,dc=in
  objectClass: organizationalUnit  
  ou: contractor
  EOF
```    

This adds an OU for contractors, which can be replicated for other roles.

## **3\. Adding Users to OUs**

```
ldapadd -x -D "cn=Manager,dc=kna,dc=in" -W <<EOF
dn: uid=shrikant.kshitij12,ou=contractor,dc=kna,dc=in
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
uid: shrikant.kshitij12
cn: Shrikant Kshitij
sn: Kshitij
telephoneNumber: 1234567892
mail: shrikant.kshitij12@example.com
userPassword: shrikant@123
jpegPhoto: shrikant.jpg
postalAddress: Contractor Address 2
EOF
```
This command creates a user entry with relevant attributes.

## **4\. Configuring Access Control Lists (ACLs)**

ACLs dictate who can access or modify specific attributes.

### **ACL for `userPassword`**
```
 olcAccess: {0}to attrs=userPassword,shadowLastChange
   by self write
   by anonymous auth
   by * none
```
    

This ensures that users can manage their own passwords while restricting others.

### **Group-based Access Control**
```
olcAccess: {1}to dn.regex="^uid=.*,ou=contractor,dc=kna,dc=in$"
by dn.regex="^uid=.*,ou=contractor,dc=kna,dc=in$" read
by * none  
```

### **Adding Group Entries**
```
ldapadd -x -D "cn=Manager,dc=kna,dc=in" -W <<EOF
 dn: cn=contractors,ou=groups,dc=kna,dc=in
 objectClass: posixGroup
 cn: contractors
 gidNumber: 5000
 memberUid: shrikant.kshitij
 EOF 
```
This creates a group for contractors, facilitating easier access management.

## **5\. Best Practices**
* Regularly update user entries and maintain accurate group memberships.  
* Periodically review and adjust ACLs as organizational needs evolve.  
* Keep clear documentation of OUs, user entries, and ACLs for audits and reference.

 ## **Conclusion**
This presentation covered the creation of OUs, user entries, and ACL configurations in LDAP, emphasizing best practices for effective user management.

