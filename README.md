KRB5 
=========

This role is will generate a krb5.conf file in a fully configurable method using all builtin directives for configuring Kerberos on a managed system

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------

The roles that are used within this role are all of the configurable directives for Kerberos. These varaiables are sorted by the sections in the krb5.conf file.  

| Variable | Section | Data Type | Default Value |
|:---------|:--------|:---------:|:-------------:|
| includeddir| None | List |'/etc/krb5.conf.d/', '/var/lib/sss/pubconf/krb5.include.d/'|
| default_keytab_name  |libdefaults| String | omit |
| default_realm | libdefaults | String | omit |
| default_tgs_enctypes | libdefaults | String | omit |
| default_tkt_enctypes | libdefaults | String | omit |
| permitted_enctypes | libdefaults | String | omit |
| allow_weak_crypto | libdefaults | Boolean | false |
| clockskew | libdefaults | String | omit |
| ignore_acceptor_hostname |libdefaults | String | omit |
| k5login_authoritative |libdefaults | Boolean | true |
| k5login_directory | libdefaults | String | omit |
| kdc_timesync |libdefaults | Boolean | true |
| kdc_req_checksum_type | libdefaults | String | omit |
| ap_req_checksum_type | libdefaults | String | omit |
| safe_checksum_type | libdefaults | String | omit |
| preferred_preauth_types |libdefaults | String |'17, 16, 15, 14'|
| ccache_type |libdefaults | String | omit |
| dns_lookup_kdc |libdefaults | Boolean | true |
| dns_lookup_realm |libdefaults | Boolean | false |
| dns_fallback |libdefaults | String | omit |
| realm_try_domains |libdefaults | Boolean | false |
| extra_addresses | libdefaults | String | omit |
| udp_preference_limit | libdefaults | String | omit |
| verify_ap_req_nofail |libdefaults | Boolean | false |
| ticket_lifetime |libdefaults | String |'1d'|
| renew_lifetime |libdefaults | String | '0'|
| noaddresses | libdefaults | Boolean | true |
| forwardable | libdefaults | Boolean | false |
| proxiable | libdefaults | Boolean | false |
| rdns | libdefaults | Boolean | true |
| plugin_base_dir | libdefaults | String | 'krb5/plugins'|
| krb5_get_tickets libdefaults | Boolean | true |
| krb_run_aklog |libdefaults | Boolean | false |
| aklog_path |libdefaults | String |'$(prefix)/bin/aklog'|
| accept_passwd | libdefaults | Boolean | false |
| realms | libdefaults | Dict | [] |
| domain_realm | libdefaults | List | [] |
| capaths |libdefaults | List | [] |
| database_module |dbdefaults | String | omit |
| ldap_kerberos_container_dn | dbdefaults | String | omit |
| ldap_kdc_dn| dbdefaults | String | omit |
| ldap_kadmind_dn | dbdefaults | String | omit |
| ldap_service_password_file | dbdefaults | String | omit |
| ldap_servers|dbdefaults | String | omit |
| ldap_conns_per_server | dbdefaults | String | '5' |
|database_name | dbmodules | String | omit |
|db_library | dbmodules | String | omit |
|disable_last_success| dbmodules | Boolean | false |
|disable_lockout|dbmodules | Boolean | false |
|ldap_kerberos_container_dn |dbmodules | String | omit |
|ldap_kdc_dn |dbmodules | String | omit |
|ldap_kadmind_dn | dbmodules | String | omit |
|ldap_service_password_file | dbmodules | String | omit |
|ldap_servers|dbmodules | String | omit |
|ldap_conns_per_server | dbmodules | String | omit |

These variables can be be overidden by entering the names and values in a group/host/ file or specifically in the playbook.  

Variable Examples
------------------

With Variable annotates with an empty list do have some specifics on defining the values to override properly. To be specific these are defining the [realms] and [domain_realm] sections of the configurations file.
The following example shows you how these specific overrides:  

To define a realm or realms you would define these realms nested in the 'realms' variable with the values of that specific realm nested within the specific realm:
```yml
realms:
   MYDOMAIN.ORG:
     kdc: 'my_domain_controller.org:88'
     master_kdc: 'my_domain_controller.org:88'
     kpasswd_server: 'my_domain_controller.org:464'
     admin_server: 'my_domain_controller.org:749'
     default_domain: 'mydomain.org'
     pkinit_anchors: 'FILE:/var/lib/ipa-client/pki/kdc-ca-bundle.pem'
     pkinit_pool: 'FILE:/var/lib/ipa-client/pki/ca-bundle.pem'
     # This can be used if you are using and AD Trust
     auth_to_local:
       rule1: 'RULE:[1:$1@$0](^.*@AD.MYDOMAIN.ORG$)s/@AD.MYDOMAIN.ORG/@AD.MYDOMAIN.ORG/'
       rule2: 'DEFAULT'
   
   AD.MYDOMAIN.ORG:
     kdc: 'ad.mydomain.org:88'
     master_kdc: 'ad.mydomain.org:88'
     kpasswd_server: 'ad.mydomain.org:464'
```
Note: It is understood that this configuration may or may not work. This serves as an example to define multiple realms if needed.

To define the domain_realm you would define the variable as a dictionary data type like the following:
```yml
domain_realm:
   subdomain1.mydomain.org: 'MYDOMAIN.ORG'
   .subdomain1.mydomain.org: 'MYDOMAIN.ORG'
   .subdomain2.mydomain.org: 'MYDOMAIN.ORG'
   subdomain2.mydomain.org: 'MYDOMAIN.ORG'
```

Example Playbook
----------------

Including an example of how to use the role and define the overrides in line.:
```yml
    - hosts: servers
      roles:
         - role: krb5
           tags: client
           vars:
            realms:
               MYDOMAIN.ORG:
                 kdc: 'my_domain_controller.org:88'
                 master_kdc: 'my_domain_controller.org:88'
                 kpasswd_server: 'my_domain_controller.org:464'
                 admin_server: 'my_domain_controller.org:749'
                 default_domain: 'mydomain.org'
                 pkinit_anchors: 'FILE:/var/lib/ipa-client/pki/kdc-ca-bundle.pem'
                 pkinit_pool: 'FILE:/var/lib/ipa-client/pki/ca-bundle.pem'
                 # This can be used if you are using and AD Trust
                 auth_to_local:
                   rule1: 'RULE:[1:$1@$0](^.*@AD.MYDOMAIN.ORG$)s/@AD.MYDOMAIN.ORG/@AD.MYDOMAIN.ORG/'
                   rule2: 'DEFAULT'
               
               AD.MYDOMAIN.ORG:
                 kdc: 'ad.mydomain.org:88'
                 master_kdc: 'ad.mydomain.org:88'
                 kpasswd_server: 'ad.mydomain.org:464'

            dbmodules:
              MY.DOMAIN.ORG:
                db_library: 'ipadb.so'
            
            plugins:
              certauth:
                module: 'ipakdb:kdb/ipadb.so'
                enable_only: 'ipakdb'
```

Example Output File
--------------
```
    includedir /etc/krb5.conf.d/
    includedir /var/lib/sss/pubconf/krb5.include.d/
    [libdefaults]
      default_keytab_name = /etc/krb5.keytab
      default_realm = MY.DOMAIN.ORG
      allow_weak_crypto = False
      k5login_authoritative = True
      kdc_timesync = True
      preferred_preauth_types = 17, 16, 15, 14
      dns_lookup_kdc = True
      dns_lookup_realm = False
      realm_try_domains = False
      udp_preference_limit = 0
      verify_ap_req_nofail = False
      ticket_lifetime = 24h
      renew_lifetime = 0
      noaddresses = True
      forwardable = True
      proxiable = False
      rdns = False
      plugin_base_dir = krb5/plugins
    [login]
      krb5_get_tickets = True
      krb_run_aklog = False
      aklog_path = $(prefix)/bin/aklog
      accept_passwd = False
    [realms]
      MYDOMAIN.ORG = {
        kdc = my_domain_controller.org:88
        master_kdc = my_domain_controller.org:88
        kpasswd_server = my_domain_controller.org:464
        admin_server = my_domain_controller.org:749
        default_domain = mydomain.org
        pkinit_anchors = FILE:/var/lib/ipa-client/pki/kdc-ca-bundle.pem
        pkinit_pool = FILE:/var/lib/ipa-client/pki/ca-bundle.pem
        auth_to_local = RULE:[1:$1@$0](^.*@AD.MYDOMAIN.ORG$)s/@AD.MYDOMAIN.ORG/@AD.MYDOMAIN.ORG/
        auth_to_local = DEFAULT
      }
      AD.MYDOMAIN.ORG = {
        kdc = ad.mydomain.org:88
        master_kdc = ad.mydomain.org:88
        kpasswd_server = ad.mydomain.org:464
      }
    [domain_realm]
    [capaths]
    [plugins]
      certauth = {
        module = ipakdb:kdb/ipadb.so
        enable_only = ipakdb
      }
    [dbdefaults]
      ldap_conns_per_server = 5
    [dbmodules]
      MY.DOMAIN.ORG = {
        db_library = ipadb.so
      }
```
License
-------

BSD

Author Information
------------------
David Lewellyn  
Systems Adminstrator  
