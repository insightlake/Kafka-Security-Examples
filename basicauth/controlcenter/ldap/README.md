# Control Center LDAP Basic Auth Integration

Control center provides two roles currently 

Administrators - Read/Write. Admins can create new plugins, Run KSQL commands, new topics etc..

Restricted - View only



There are two ways security can be provisioned:

File based - We provision username and passwords in a file and roles. not very secure..
LDAP based - Control center uses LdapLoginModule (JAAS) based mechanism

For SSL create truststores

Cut and paste the 2 certs in 2 separate files ldap1.pem and ldap2.pem

Create jks using

keytool -keystore kafka.ldap.truststore.jks -alias LDAPRoot

When importing use 2 aliases

keytool -keystore kafka.ldap.truststore.jks -alias LDAPRoot1 -import -file ldap1.pem

keytool -keystore kafka.ldap.truststore.jks -alias LDAPRoot2 -import -file ldap2.pem

#-> openssl s_client -showcerts -host LDAP-HOST.com -port 3269

---
Certificate chain
0 s:/CN=HOST1
   i:/DC=COM/DC=VSERVE/CN=VSERVE Internal Online CA C3
-----BEGIN CERTIFICATE-----
XXXXXXXX
-----END CERTIFICATE-----
1 s:/DC=COM/DC=VSERVE/CN=Root CA C3
   i:/DC=COM/DC=VSERVE/CN=Root Certification Authority 2
-----BEGIN CERTIFICATE-----
XXXXXXXXXX
-----END CERTIFICATE-----
---



export CONTROL_CENTER_OPTS="-Djava.security.auth.login.config=/etc/confluent-control-center/c3.jaas -Dcom.sun.jndi.ldap.object.disableEndpointIdentification=true -Djavax.net.ssl.trustStore=/secrets/kafka.ldap.trustore.jks -Djavax.net.ssl.trustStorePassword=Confulent -Djavax.net.debug=SSL”



Control Center Properties file

####Added for LDAP Authentication ######

# The name of the configuration block in the jaas configuration

confluent.controlcenter.rest.authentication.realm=c3

# http authentication type

confluent.controlcenter.rest.authentication.method=BASIC

# The name of the group object to search

confluent.controlcenter.rest.authentication.roles=GRP-Admins,GRP-View

confluent.controlcenter.auth.restricted.roles=GRP-View
