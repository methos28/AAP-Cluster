## Installation:

1. Do SSH login to VM.
2. Update yum
3. Make sure to add all IPs and their hostname to your 'hosts' file which is located in ```/etc/hosts```
4. Download 'Ansible Automation Platform 2.2.0 Setup Bundle' of which version is '2.2 for RHEL 8' from https://access.redhat.com/downloads/content/480?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW
5. You'll need to use curl command inorder to download that bundle to VM.
6. Extract the file and go to the directory of that AAP bundle.
```
tar xvzf <ansible_bundle_file>
```
```
cd <ansible_bundle_files>
```
6. Follow the following inventory syntax for creating multi-machine cluster installation. 
```
[automationcontroller]
192.168.1.10 node_type=control
192.168.1.20 node_type=control
192.168.1.30 node_type=control

[automationcontroller:vars]
peers=execution_nodes

[execution_nodes]
192.168.1.201 node_type=execution

[automationhub]
192.168.1.101
192.168.1.102

[automationcatalog]

[database]
192.168.1.100

[all:vars]
admin_password='admin123'

pg_host='192.168.1.100'
pg_port='5432'

pg_database='awx'
pg_username='awx'
pg_password='dbadmin123'
pg_sslmode='prefer' # set to 'verify-full' for client-side enforced SSL

registry_url='registry.redhat.io'
registry_username='yourusername'
registry_password='yourpassword'

receptor_listener_port=27199

automationhub_admin_password='ahadmin123'

automationhub_pg_host='192.168.1.100'
automationhub_pg_port='5432'

automationhub_pg_database='automationhub'
automationhub_pg_username='automationhub'
automationhub_pg_password='ahadmin123'
automationhub_pg_sslmode='prefer'

```

> NOTE : Check the file and its format, You will have to add your servers IPs/Hostnames instead of the given ones. Make sure to use either IPs or hostnames at a time as using both at the same time does not work well.
> 
> NOTE : There are Two ways to install this cluster. Most preferred one is with HTTPS, but you can install with HTTP as well if you want to run your AAPs and AHs on port 80 as internal port.
> 
> NOTE : Before you install your Cluster you need to run AAP-Fix.yml file to fix some possible errors that might come while installing the cluster.

7. Create ```/var/lib/pulp``` in Private Automation Hub node

8. Install AAP using following command.
```
ANSIBLE_BECOME=true ./setup.sh (with HTTPS support)
``` 
```
ANSIBLE_BECOME=true ./setup.sh -e nginx_disable_https=true -- -b (without HTTPS support)
```

9. Now you might get error, then follow this resolution
https://access.redhat.com/solutions/6970135

10. Only procceed further after successful installtion.
