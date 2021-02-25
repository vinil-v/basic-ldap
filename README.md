# How To Install and Configure a Basic LDAP Server

The following parameters are used to configure the ldap server <br />

**Domain**:	vinil.com<br />
**Domain DN**:	dc=vinil,dc=com<br />
**LDAP Server**:	ldapserver.vinil.com<br />
**OS Version** : Red Hat Enterprise Linux Server release 7.9 (Maipo)<br />

**Installing packages required to configure Ldap server:<br />**

yum -y install openldap-servers openldap-clients<br />


cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG<br />
chown ldap. /var/lib/ldap/DB_CONFIG<br />
systemctl start slapd<br />
systemctl enable slapd<br />


**Set OpenLDAP admin password.<br />**
	
slappasswd<br />
New password:<br />
Re-enter new password:<br />
{SSHA}lQiyFGZXw4Uk3F2Bic74EbShG3Fl6C57<br />

vi chrootpw.ldif<br />
cat chrootpw.ldif<br />
dn: olcDatabase={0}config,cn=config<br />
changetype: modify<br />
add: olcRootPW<br />
olcRootPW: {SSHA}lQiyFGZXw4Uk3F2Bic74EbShG3Fl6C57<br />
ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif<br />
SASL/EXTERNAL authentication started<br />
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth<br />
SASL SSF: 0<br />
modifying entry "olcDatabase={0}config,cn=config"<br />
