# AAP-Cluster Installation along with Load Balancer and Postfix on RHEL 8

Here in this cluster we have included 3 Controller nodes, 1 Database node and 2 Automation Hubs. You can create the cluster as per your requirements as well.
To start with the installation lets go ahead and take a look at the Prerequisites we are going to need.

## Prerequisites:
| Sr. No.  | Package Name |
| ------------- | ------------- |
| 1.  | openssl |
| 2.  | haproxy |
| 3.  | curl |
| 4.  | wget |
| 4.  | vim |
| 5.  | telnet |
  
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

7. Install AAP using following command.
```
ANSIBLE_BECOME=true ./setup.sh (with HTTPS support)
``` 
```
ANSIBLE_BECOME=true ./setup.sh -e nginx_disable_https=true -- -b (without HTTPS support)
```
8. If there is any errors with related with 'pulp' directory, run add-hosts.yml and restart the installation.
9. Only procceed further after successful installtion.


## Installing HAPROXY for Load Balancing

After the successful installation if you want you can set a load balancer in between them to redirect the incoming traffic.
You need to run the aap-haproxy-fix.yml playbook on the controller nodes first before you edit the configuration file.

> NOTE: Make sure that you have the IPs and Hostnames of your controller servers in the hosts file in /etc/hosts, If you do not add those servers in that file the load balancer will not work.

### Creating Self-Signed Certificate
Enter Commands below:
```
openssl req -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out ./myserver.crt -keyout ./myserver.key
```
```
cat myserver.crt myserver.key > myserver.pem
```
Along with that you need to enter this command as well.
```
sudo openssl dhparam -out /etc/haproxy/dhparams.pem 2048
```

Also make the neccessary changes into your ***HAPROXY.CFG*** file as the file above in the REPO, Enable the Service using :
```
systemctl enable haproxy
```

Run aap-fix-haproxy.yml playbook and check if haproxy works.


## Installing Postfix for AAP Cluster Notifications
Installing Postfix
```
yum install postfix
```

Installing SASL
```
yum install cyrus-sasl-plain
```

Installing Mailx for mail commands
```
yum install mailx
```
Postfix is now installed with the default configuration. 
Use below command to see configs of postfix
```
postconf
```

## Configuring Postfix /etc/postfix/main.cf

Confirm that the myhostname parameter is configured with your server’s FQDN: Path is `/etc/postfix/main.cf` and parameter is
```
myhostname = fqdn.example.com
```
If not present then set one.

Change the inet interfaces as below
```
inet_interfaces = all
```
```
inet_protocols - all
```

Change my destination to respective hostnames
```
mydestination = dblb.prodevans.lan, localhost
```

Change mynetworks to your domain range
```
mynetworks = 192.168.1.0/24
```


Setup the following configs
```
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
message_size_limit = 20480000
```
Change 'may' to 'encrypt' for following command
```
smtp_tls_security_level = encrypt
```

Save the file and restart postfix
```
systemctl restart postfix.service
```

## Add Gmail Username and Password to Postfix
Open or create the ```/etc/postfix/sasl_passwd``` file and add the SMTP Host, username, and password information:
```
[smtp.gmail.com]:587 <youremail@gmail.com>:<yourgmailpassword>  
``` 
Save it and create the hash db file for Postfix by running the postmap command:
```
sudo postmap /etc/postfix/sasl_passwd
```
Secure Your Postfix Hash Database and Email Password Files:
```
sudo chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```
```
sudo chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```

## Start and enable Postfix to run on system boot
```
systemctl enable --now  postfix
```

Check status of Postfix
```
systemctl status postfix
```

## Configuration on AAP side:

Go to the AAP only after all the above successful configs.
Under 'Administration' section, select 'Notifications' from left pane.
Under 'Notification Templates', select `+` green icon to add a new template.

Give a name. select organization and choose `Mail` in TYPE options.

In `HOST`, enter the hostname of the machine.

In `SENDER MAIL`, enter the mail from postfix configs.

In `RECEPIENTS LIST`, enter the recepients emails one by one.

In `PORT`, enter the port of postfix which is 25 in this case.

In `EMAIL OPTIONS`, select `Use TLS`
and save it and test it. It should be working fine.

![image](https://user-images.githubusercontent.com/24843193/188405784-d2938157-de5c-4c97-9189-bd9f9884b5da.png)


 REF_LINKS :
  - https://youtu.be/TkiGgUkn_PI
  - https://www.digitalocean.com/community/tutorials/haproxy-ssl-tls-warning-setting-tune-ssl-default-dh-param-to-1024-by-default
  - https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts
  - https://100things.wzzrd.com/2021/07/09/Loadbalanced-Ansible-Tower-Cluster.html
  - https://www.digitalocean.com/community/tutorial_series/common-haproxy-errors
  - https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/install_haproxy_example1
     
