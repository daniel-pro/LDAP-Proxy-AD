# LDAP-Proxy-AD
OpenLDAP proxy configuration for AD.
I struggled a while to have this config fully working, I collected a lot pieces of information from github, forums and OpenLDAP documentation.
So I hope it could save some time to someone.

The files included in this repo can be used to configure an OpenLDAP as proxy (with Translucent Proxy) for AD.

Since sometimes it's not possible to manage the Posix Attributes in AD, the current configuration allows to store them in a local database and merge them when the CNs match.

You also might need to have "local" OpenLDAP users - which is also possible.

# Pre-req
 - openldap server installed with Translucent Proxy, PCache and RWM extensions

# Configuration

```
mkdir -p /var/lib/ldap/proxy/{cache,conf,data,local_data}

slapadd -F /var/lib/ldap/proxy/conf -n 0 -l proxy-config.ldif

slapd -d 4096 -F /var/lib/ldap/proxy/conf -h "ldap://127.0.0.1:3892/ ldapi://%2Fvar%2Flib%2Fldap%2Fproxy%2Fldapi" &

ldapadd -H ldap://127.0.0.1:3892 -D cn=LDAPManager,dc=vds,dc=enterprise -x -w secretpwd -f proxy-data.ldif
ldapadd -H ldap://127.0.0.1:3892 -D cn=LDAPManager,ou=local,dc=vds,dc=enterprise -x -w secretpwd -f proxy-data-local.ldif
```

# Test
```
ldapsearch -H ldap://127.0.0.1:3892 -b dc=vds,dc=enterprise -x "(sAMAccountName=jsmith)"

```
