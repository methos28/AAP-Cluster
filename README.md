# AAP-Cluster Installation along with Load Balancer on RHEL 8

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
| 5.  | ansible |
| 6.  | telnet |
  
## Installation:
After checking prerequistes lets turn to the Inventory File. I have uploaded it to the repo as well. 
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

Check the file and its format, You will have to add your servers IPs instead of the given ones.
There are Two ways to install this cluster. Most preferred its with HTTPS, but you can install with HTTP as well
> NOTE: Before you install your Cluster you need to run AAP-Fix.yml file to fix some possible errors that might come while installing the cluster.
```
ANSIBLE_BECOME=true ./setup.sh (with HTTPS support)
``` 
```
ANSIBLE_BECOME=true ./setup.sh -e nginx_disable_https=true -- -b (without HTTPS support)
```


## Installing HAPROXY for Load Balancing

After the successfull installation if you want you can set a load balancer in between them to Redirect the incoming traffic.
You need to run the aap-haproxy-fix.yml playbook on the controller nodes first before you edit the configuration file.

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
After which you need to restart the system for which use ```init 6```
> NOTE: Make sure that you have the IPs and Hostnames of your controller servers in the hosts file in /etc/hosts, If you do not add those servers in that file the load balancer will not work.

## Installing Postfix for AAP Cluster Notifications over SMTP
First you need to install postfix along with sasl and MailX for mail commands.
```
yum install postfix cyrus-sasl-plain mailx
```

To see if the postfix is installed or not as well as to see the configuration of postfix you can enter ```postconf``` in terminal.

### Configuring the ***main.cf** 

The file location is ```/etc/postfix/main.cf```. Take a look at the file above in the repo named main.cf and as per the requirements modify it for yourself.

### Add Gmail Username and Password to Postfix
To add your credentials you need to open or create ```/etc/postfix/sasl_passwd``` and add the content below with your details.
```
[smtp.gmail.com]:587 <youremail@gmail.com>:<yourgmailpassword>  
```
After which you need to create hash db file for the same
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
now enable the postfix service using, 
```
systemctl enable postfix
```
Restart it ,
```
systemctl restart postfix
```

### Send a Test Mail using Postfix
```
echo "Test Postfix Gmail SMTP Relay" | mail -s "Postfix Gmail SMTP Relay" youremail@gmail.com
```


 REF_LINKS :
  - https://youtu.be/TkiGgUkn_PI
  - https://www.digitalocean.com/community/tutorials/haproxy-ssl-tls-warning-setting-tune-ssl-default-dh-param-to-1024-by-default
  - https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts
  - https://100things.wzzrd.com/2021/07/09/Loadbalanced-Ansible-Tower-Cluster.html
  - https://www.digitalocean.com/community/tutorial_series/common-haproxy-errors
  - https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/install_haproxy_example1
     

























