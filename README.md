# AAP-Multi-machine Cluster Installation along with Load Balancer, Postfix and Slack integration for AAP on RHEL 8

Here in this multi-machine cluster, we have included 3 Controller nodes, 1 Database node, 2 Automation Hub nodes and an execution node. 
You can create the cluster as per your requirements as well.
To start with the installation lets go ahead and take a look at the prerequisites we are going to need.

## Prerequisites:
| Sr. No.  | Package Name |
| ------------- | ------------- |
| 1.  | openssl |
| 2.  | haproxy |
| 3.  | curl |
| 4.  | wget |
| 4.  | vim |
| 5.  | telnet |


Everything is done on RHEL 8.6 (Minimal Installation) x86_64 on VMWare ESXi.

Specs used for VMs in order to do the installation are,

3 Controller nodes with 4C/8T | 8 GB RAM | 30GB SSD each.

2 Ansible Automation Hub nodes with 4C/8T | 8 GB RAM | 30 GB HDD each.

1 Database node with 2C/4T | 4 GB RAM | 30 GB HDD

1 Execution node with 2C/4T | 4 GB RAM | 30 GB HDD

  
Kindly follow path as given below for smooth installation,

1. [AAP Multi-Machine Cluster Installation](https://github.com/methos28/AAP-Cluster/blob/main/AAP%20multi-node%20cluster%20installation.md)
2. [HAProxy Installation for Load Balancing](https://github.com/methos28/AAP-Cluster/blob/main/HAProxy%20Installation%20for%20Load%20Balancing.md)
3. [Postfix Installation for AAP SMTP Notifications](https://github.com/methos28/AAP-Cluster/blob/main/Postfix%20Installation%20for%20AAP%20SMTP%20Notifications.md)
4. [Slack Notification for Ansible Automation Platform](https://github.com/methos28/AAP-Cluster/blob/main/Slack%20Notification%20for%20Ansible%20Automation%20Platform.md)

> References:
>
> https://youtu.be/TkiGgUkn_PI
> 
> https://www.digitalocean.com/community/tutorials/haproxy-ssl-tls-warning-setting-tune-ssl-default-dh-param-to-1024-by-default
> 
> https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts
> 
> https://100things.wzzrd.com/2021/07/09/Loadbalanced-Ansible-Tower-Cluster.html
> 
> https://www.digitalocean.com/community/tutorial_series/common-haproxy-errors
> 
> https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/install_haproxy_example1
