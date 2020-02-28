# Control Center LDAP Basic Auth Integration

Control center provides two roles currently 

Administrators - Read/Write. Admins can create new plugins, Run KSQL commands, new topics etc..

Restricted - View only



There are two ways security can be provisioned:

File based - We provision username and passwords in a file and roles. not very secure..
LDAP based - Control center uses LdapLoginModule (JAAS) based mechanism

# LDAP Changes
We need to create two groups Group-Admins and Group-Restricted

# JAAS Files

Simple LDAP

```
c3 {
  org.eclipse.jetty.jaas.spi.LdapLoginModule required
  debug="true"
  contextFactory="com.sun.jndi.ldap.LdapCtxFactory"
  hostname="localhost"
  port="389"
  bindDn="cn=insightlake,dc=verticalserve,dc=com"
  bindPassword="secret"
  authenticationMethod="simple"
  forceBindingLogin="false"
  userBaseDn="dc=verticalserve,dc=com"
  userRdnAttribute="sn"
  userIdAttribute="sn"
  userPasswordAttribute="userPassword"
  userObjectClass="inetOrgPerson"
  roleBaseDn="ou=Groups,dc=verticalserve,dc=com"
  roleNameAttribute="cn"
  roleMemberAttribute="uniqueMember"
  roleObjectClass="groupOfUniqueNames";
};
```

Secuure LDAP

```
c3 {
  org.eclipse.jetty.jaas.spi.LdapLoginModule required
  useLdaps="true"
  contextFactory="com.sun.jndi.ldap.LdapCtxFactory"
  debug="true"
  hostname="domain"
  port="3269"
  providerUrl="ldaps://domain:3269"
  bindDn="DN-NAME"
  bindPassword="pass"
  authenticationMethod="simple"
  forceBindingLogin="true"
  userBaseDn="DC=verticalserve,DC=com"
  userRdnAttribute="sAMAccountName"
  userIdAttribute="sAMAccountName"
  userPasswordAttribute="userPassword"
  userObjectClass="user"
  roleBaseDn="ou=Groups,DC=verticalserve,DC=com"
  roleNameAttribute="cn"
  roleMemberAttribute="member"
  roleObjectClass="group";
};
```

# Control Center
export CONTROL_CENTER_OPTS="-Djava.security.auth.login.config=/etc/confluent-control-center/c3.jaas -Dcom.sun.jndi.ldap.object.disableEndpointIdentification=true -Djavax.net.ssl.trustStore=/secrets/kafka.ldap.trustore.jks -Djavax.net.ssl.trustStorePassword=Confulent -Djavax.net.debug=SSL”


```
Control Center Properties file

####Added for LDAP Authentication ######

# The name of the configuration block in the jaas configuration

confluent.controlcenter.rest.authentication.realm=c3

# http authentication type

confluent.controlcenter.rest.authentication.method=BASIC

# The name of the group object to search

confluent.controlcenter.rest.authentication.roles=Group-Admins,Group-Restricted

confluent.controlcenter.auth.restricted.roles=Group-Restricted

```
