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

:wq ( save & exit )

# The above keystone.example.conf file details can be found from LDAP server by running following command on ldap server.

# Run the below command on LDAP server

ldapsearch -x -b 'dc=example,dc=com' '(objectclass=*)'

# Back on your controller node.

# Restart the keystone service ( in my case keystone service is sync with httpd )

systemctl restart httpd

# Now create a domain on your controller node 
# Set your admin envir. on controller node.

openstack domain create example

# Create the project within that domain

openstack project create procloud --domain example

# create a role

openstack role create ladmin

# Add the ladmin role to the procloud project and user:

openstack role add --project procloud --user ldapuser1 ladmin

# If it says user not found then add it's id which you can see by the runningh below cmnd.

openstack user show ldapuser1 --example

The details show like this 

+-----------+------------------------------------------------------------------+
| Field     | Value                                                            |
+-----------+------------------------------------------------------------------+
| domain_id | 83ca640dd0b14947a40f9046d1f84aff                                 |
| id        | d210ae253c4e3f2e60894b899b7d9a7699afd8a8054586c2736a94a9afe82e47 |
| name      | ldapuser1                                                        |
+-----------+------------------------------------------------------------------+

# So you can use user id instead of name "ldapuser1", find the example below

openstack role add --project procloud --user d210ae253c4e3f2e60894b899b7d9a7699afd8a8054586c2736a94a9afe82e47 ladmin

# Because user is available on LDAP server with name "ldapuser1"Openstack-controller identify it by ids.

# Now you are able to access dashboard with ldap user, Access your dashmoard

http://10.0.0.X/dashboard

# put your login creadential of ldapuser & the domain name created on controller "ëxample"


you will get successfully login.

Cheers!!


