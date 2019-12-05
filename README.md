oVirt AAA LDAP
==============

This role deploy oVirt AAA LDAP configuration.

Requirements
------------

Just Ansible.

Role Variables
--------------

* aaa_profile_type: Define profile type, one of: 

   - 389ds
   - iplanet
   - rfc2307-389ds
   - rfc2307-generic
   - rfc2307-openldap
   - rfc2307-rhds
   - ad
   - ipa
   - openldap
   - rfc2307-edir
   - rhds

* aaa_user: User which should be used to search users for auhtorization.
* aaa_password: Password of the search user.
* aaa_profile_name: Name of the profile (visible in login page).
* aaa_ldap: List of LDAP servers or LDAP domain. When more servers are specified, failover policy will be used.
* aaa_ldap_is_domain: Whether aaa_ldap is domain with servers discovered via SRV records or not. Default is false (with exception of aaa_profile_type set to `ad`, then default is true)
* aaa_base_dn: Custom base DN in case user want to set special.
* aaa_legacy_api_authn: Whether to include `/ovirt-engine/api` among paths that trigger HTTP authentication (was necessary before oVirt 4.0). Disabled by default
* aaa_sso_keytab: Path to keytab on ansible control machine which stores principal to use for SSO. This parameter or aaa_sso_remote_keytab is required in case SSO should be deployed. Keytab will be copied to `/etc/httpd/http.keytab`
* aaa_sso_remote_keytab: Path to keytab already present on target machine. Must be accessible and readable by apache.

For example to obtain HTTP keytab for oVirt engine in IPA use following command
```bash
$ ipa-getkeytab --server=ipa.example.com --principal=HTTP/ovirt.example.com --keytab=ovirt.keytab
```

Dependencies
------------

No.

Example Playbook
----------------

This is example of how to deploy IPA with single server:

```yaml
    - name: Deploy oVirt AAA IPA
      hosts: localhost
      gather_facts: no
      vars:
        aaa_profile_type: ipa
        aaa_user: uid=search,cn=users,cn=accounts,dc=example,dc=com
        aaa_password: password
        aaa_profile_name: ipa
        aaa_ldap:
          - ldap.example.com
    
      roles:
        - machacekondra.ovirt-aaa-ldap
```

This is example of how to deploy IPA with failover servers:

```yaml
    - name: Deploy oVirt AAA IPA failover
      hosts: localhost
      gather_facts: no
      vars:
        aaa_profile_type: ipa
        aaa_user: uid=search,cn=users,cn=accounts,dc=example,dc=com
        aaa_password: password
        aaa_profile_name: ipa
        aaa_ldap:
          - ldap1.example.com
          - ldap2.example.com
    
      roles:
        - machacekondra.ovirt-aaa-ldap
```

This is example of how to deploy IPA with SSO:

```yaml
    - name: Deploy oVirt AAA IPA SSO
      hosts: localhost
      gather_facts: no
      vars:
        aaa_profile_type: ipa
        aaa_user: uid=search,cn=users,cn=accounts,dc=example,dc=com
        aaa_password: password
        aaa_profile_name: ipa
        aaa_ldap:
          - ldap1.example.com
          - ldap2.example.com
        aaa_sso_keytab: /path/to/ovirt.keytab
    
      roles:
        - machacekondra.ovirt-aaa-ldap
```

License
-------

BSD

Author Information
------------------

Ondra Machacek (@machacekondra)
