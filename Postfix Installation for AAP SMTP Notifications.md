# Installing Postfix for AAP Cluster Notifications
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

### For password without 2FA (Not recommended)
Just enter the password as it is in ```/etc/postfix/sasl_passwd/```

### For password with 2FA (Recommended)

1. Go to Google and sign in with your Gmail account.
2. Click on your profile present on right upper side corner.
3. Click on 'Manage your Google Account'.
4. On the left pane, select 'Security'.
5. Scroll down to 'Signing in to Google' section.
6. In that, assuming you have 2FA activated, click on 'App passwords'.
7. In 'Select App', select 'Other' and give name as 'Postfix' and click on 'GENERATE'.
8. The new app password will be generated. It will be shown for once so copy it and use it for 'sasl_passwd'.

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

Add SMTP service to firewall-cmd
```
firewall-cmd --permanent --add-service=smtp
```

Check status of Postfix
```
systemctl status postfix
```

## Send a Test Mail to Verify Postfix Gmail SMTP Relay
```
echo "Test Postfix Gmail SMTP Relay" | mail -s "Postfix Gmail SMTP Relay" youremail@gmail.com
```

To see if mailing is working, use following command
```
tail -f /var/log/maillog
```

## Configuration on AAP side:

Go to the AAP only after all the above successful configs.
Under the 'Administration' section, select 'Notifications' from left the pane.
Under 'Notification Templates', select `+` green icon to add a new template.

Give a name. select organization and choose `Mail` in TYPE options.

In `HOST`, enter the hostname of the machine.

In `SENDER MAIL`, enter the mail from postfix configs.

In `RECEPIENTS LIST`, enter the recepients emails one by one.

In `PORT`, enter the port of postfix which is 25 in this case.

In `EMAIL OPTIONS`, select `Use TLS`
and save it and test it. It should be working fine.

![image](https://user-images.githubusercontent.com/24843193/188405784-d2938157-de5c-4c97-9189-bd9f9884b5da.png)
