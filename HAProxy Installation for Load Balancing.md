# Installing HAPROXY for Load Balancing

After the successful installation if you want you can set a load balancer in between them to redirect the incoming traffic.
You need to run the aap-haproxy-fix.yml playbook on the controller nodes first before you edit the configuration file.

> NOTE: Make sure that you have the IPs and Hostnames of your controller servers in the hosts file in /etc/hosts, If you do not add those servers in that file the load balancer will not work.

## Creating Self-Signed Certificate
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
