# openstack-ldap
Ldap integration with Openstack-Mitaka
# Make changes in keystone.conf file in [identity] section.
vi /etc/keystone/keystone.conf
# add these two options in [identity] section.
domain_specific_drivers_enabled = True
domain_config_dir = /etc/keystone/domains

:wq ( Save & quit )

# Now you need to create domains directory under /etc/keystone

mkdir -p /etc/keystone/domains

# Then make a file with the name of keystone.DOMAIN_NAME.conf ( Replace DOMAIN_NAME with your local ldap server domain name )
# make sure domain name should be same as you mention in LDAP server

# Now make the file under /etc/keystone/domains

vi /etc/keystone/domains/keystone.example.conf       ( Here I have replaced DOMAIN_NAME with example )

# Add the follwing entries in keystone.example.conf file.

[identity]
driver = ldap

[assignment]
driver = sql

[ldap]
url = ldap://ldap server ip or hostname
user = cn=Manager,dc=example,dc=com
password = badpasswd
query_scope = sub

user_tree_dn = "dc=example,dc=com"
user_objectclass = posixAccount
user_id_attribute = uid
user_name_attribute = cn
user_mail_attribute = ldapuser1@example.com
user_pass_attribute = badpasswd
user_enabled_attribute = enabled
group_tree_dn = "ou=Group,dc=example,dc=com"
#group_objectclass = organizationalUnit
group_name_attribute = Group
group_objectclass = posixGroup


user_allow_create = false
user_allow_update = false
user_allow_delete = false
group_allow_create = false
group_allow_update = false
group_allow_delete = false
