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

**Set your domain name on LDAP DB.**<br />
[root@ldapserver ~]# slappasswd<br />
New password:<br />
Re-enter new password:<br />
**{SSHA}E1rizZbX2PijOZrh0JYb0E8VwZF+jshy<br />**

[root@ldapserver ~]# vi chdomain.ldif<br />

[root@ldapserver ~]# cat chdomain.ldif<br />
dn: olcDatabase={1}monitor,cn=config<br />
changetype: modify<br />
replace: olcAccess<br />
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"<br />
  read by dn.base="cn=Manager,dc=vinil,dc=com" read by * none<br />

dn: olcDatabase={2}hdb,cn=config<br />
changetype: modify<br />
replace: olcSuffix<br />
olcSuffix: dc=vinil,dc=com<br />

dn: olcDatabase={2}hdb,cn=config<br />
changetype: modify<br />
replace: olcRootDN<br />
olcRootDN: cn=Manager,dc=vinil,dc=com<br />

dn: olcDatabase={2}hdb,cn=config<br />
changetype: modify<br />
add: olcRootPW<br />
olcRootPW: **{SSHA}E1rizZbX2PijOZrh0JYb0E8VwZF+jshy<br />**

dn: olcDatabase={2}hdb,cn=config<br />
changetype: modify<br />
add: olcAccess<br />
olcAccess: {0}to attrs=userPassword,shadowLastChange by<br />
  dn="cn=Manager,dc=vinil,dc=com" write by anonymous auth by self write by * none<br />
olcAccess: {1}to dn.base="" by * read<br />
olcAccess: {2}to * by dn="cn=Manager,dc=vinil,dc=com" write by * read<br />

[root@ldapserver ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif<br />
SASL/EXTERNAL authentication started<br />
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth<br />
SASL SSF: 0<br />
modifying entry "olcDatabase={1}monitor,cn=config"<br />

modifying entry "olcDatabase={2}hdb,cn=config"<br />

modifying entry "olcDatabase={2}hdb,cn=config"<br />

modifying entry "olcDatabase={2}hdb,cn=config"<br />

modifying entry "olcDatabase={2}hdb,cn=config"<br />


[root@ldapserver ~]# vi basedomain.ldif<br />

[root@ldapserver ~]# cat basedomain.ldif<br />
dn: dc=vinil,dc=com<br />
objectClass: top<br />
objectClass: dcObject<br />
objectclass: organization<br />
o: Vinil Com<br />
dc: vinil<br />

dn: cn=Manager,dc=vinil,dc=com<br />
objectClass: organizationalRole<br />
cn: Manager<br />
description: Directory Manager<br />

dn: ou=People,dc=vinil,dc=com<br />
objectClass: organizationalUnit<br />
ou: People<br />

dn: ou=Group,dc=vinil,dc=com<br />
objectClass: organizationalUnit<br />
ou: Group<br />

[root@ldapserver ~]# ldapadd -x -D cn=Manager,dc=vinil,dc=com -W -f basedomain.ldif<br />
Enter LDAP Password:<br />
adding new entry "dc=vinil,dc=com"<br />

adding new entry "cn=Manager,dc=vinil,dc=com"<br />

adding new entry "ou=People,dc=vinil,dc=com"<br />

adding new entry "ou=Group,dc=vinil,dc=com"<br />
