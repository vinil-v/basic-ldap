# How To Install and Configure a Basic LDAP Server

The following parameters are used to configure the ldap server <br />

**Domain**:	vinil.com<br />
**Domain DN**:	dc=vinil,dc=com<br />
**LDAP Server**:	ldapserver.vinil.com<br />
**OS Version** : Red Hat Enterprise Linux Server release 7.9 (Maipo)<br />

**Installing packages required to configure Ldap server:<br />**

[root@ldapserver ~]# yum -y install openldap-servers openldap-clients<br />

**copying the sample config file and starting the service<br />**
[root@ldapserver ~]# cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG<br />
[root@ldapserver ~]# chown ldap. /var/lib/ldap/DB_CONFIG<br />
[root@ldapserver ~]# systemctl start slapd<br />
[root@ldapserver ~]# systemctl enable slapd<br />


**Set OpenLDAP admin password.<br />**
	
[root@ldapserver ~]# slappasswd<br />
New password:<br />
Re-enter new password:<br />
**{SSHA}lQiyFGZXw4Uk3F2Bic74EbShG3Fl6C57<br />**

specify the password generated above for "olcRootPW" section <br />

[root@ldapserver ~]# vi chrootpw.ldif<br />
[root@ldapserver ~]# cat chrootpw.ldif<br />
dn: olcDatabase={0}config,cn=config<br />
changetype: modify<br />
add: olcRootPW<br />
olcRootPW: **{SSHA}lQiyFGZXw4Uk3F2Bic74EbShG3Fl6C57<br />**

[root@ldapserver ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif<br />
SASL/EXTERNAL authentication started<br />
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth<br />
SASL SSF: 0<br />
modifying entry "olcDatabase={0}config,cn=config"<br />

**Import basic schemas<br />**

[root@ldapserver ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif<br />
SASL/EXTERNAL authentication started<br />
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth<br />
SASL SSF: 0<br />
adding new entry "cn=cosine,cn=schema,cn=config"<br />

[root@ldapserver ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif<br />
SASL/EXTERNAL authentication started<br />
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth<br />
SASL SSF: 0<br />
adding new entry "cn=nis,cn=schema,cn=config"<br />

[root@ldapserver ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif<br />
SASL/EXTERNAL authentication started<br />
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth<br />
SASL SSF: 0<br />
adding new entry "cn=inetorgperson,cn=schema,cn=config"<br />
