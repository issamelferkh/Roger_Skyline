# Roger Skyline 1

## Contents
- [Introduction](#intro)
- [VM Part](#vmpart)
- [Network and Security Part](#Network_and_Security_Part)
- [Create non-root User](#Create_non_root_User)
- [Network Config](#Network_Config)
- [SSH Config](#SSH_Config)
- [Firewalling and Security Config]($Firewalling_and_Security_Config)
- [Script Config]($Script_Config)
- [Web Part]($Web_Part)
- [Deployment Part](#Deployment_Part)

## Introduction <a id="intro"></a>
This project let you install a Virtual Machine, discover the basics about system and network administration as well as a lots of services used on a server machine.

## VM Part <a id="vmpart"></a>
1. Install a Virtual Machine (VM) with the Linux OS of your choice (Debian Jessie, CentOS 7...) in the hypervisor of your choice (VMWare Fusion, VirtualBox...).
	- Use VirtualBox -> Add OS -> Settings -> Storage -> IDE -> Choose your OS (Ubuntu Server 18.04.3 LTS)
2. A disk size of 8 GB.
	- HDD -> 8Go -> virtual hard disk -> VDI -> Fixed Size
	- RAM -> 1024 Mo
3. Have at least one 4.2 GB partition.
	- to check if we had at least one partition: ```$lsblk```	
4. It will also have to be up to date as well as the whole packages installed to meet the demands of this subject.
	- ```$sudo apt upgrade```
	- ```$sudo apt update```

## Network and Security Part <a id="Network_and_Security_Part"></a>

### Create non-root User <a id="Create_non_root_User"></a>
1. Create the user ```$sudo adduser username```
2. Add the user to sudo group ```$usermod -a -G sudo user1```

### Network Config <a id="Network_Config"></a>
We don’t want you to use the DHCP service of your machine. You’ve got to configure it to have a static IP and a Netmask in \30.
1. Config @IP in ```$vim /etc/netplan/50-cloud-init.yaml```
	- Our @IP getway is `10.11.254.254`
			Address:   10.11.254.254<br />
			Netmask:   255.255.255.252 = 30<br />
			Wildcard:  0.0.0.3<br />
			=><br />
			Network:   10.11.254.252/30<br />
			Broadcast: 10.11.254.255<br />
			HostMin:   10.11.254.253<br />
			HostMax:   10.11.254.254<br />
			Hosts/Net: 2<br />
	- So the only @IP available with mask 30 is `10.11.254.253`

2. Restart Network with ```$sudo netplan --debug apply```
3. change web redirection ```$vim /etc/apache2/sites-available/website.conf```

### SSH Config <a id="SSH_Config"></a>
1. You have to change the default port of the SSH service by the one of your choice.
	- Open SSH config file ```$vim /etc/ssh/sshd_config```
	- Change default port ```#port 22``` to ```port 222```
	- Change protocol ```Protocol 2, 1``` to ```Protocol 2```, ssh2 is more secure that ssh1.
	- Root should not be allowed to access ```PermitRootLogin yes``` to ```PermitRootLogin no```
	- Disable password login ```#PasswordAuthentication yes``` to ```PasswordAuthentication no``` and ```ChallengeResponseAuthentication yes``` to ```ChallengeResponseAuthentication no```
	- Enable Pub key ```#PubKeyAuthentication no``` to ```PubKeyAuthentication yes```
	- Restart Service SSH ```$systemctl restart ssh```
	- Try logging with new port ```$ssh -p 222 user@10.12.254.253```

2. SSH access HAS TO be done with publickeys.
	- Generate SSH Keys in clien ```$ssh-keygen```  
	- Copy pub key to server ```$ssh-copy-id -p 222 user1@10.12.254.253``` 
	- Try logging with user1 ```$ssh -p '222' 'user1@10.12.254.253'``` 
	- Doc => http://go2linux.garron.me/linux/2010/10/ssh-public-key-only-login-authentication-788/

3. SSH root access SHOULD NOT be allowed directly, but with a user who can be root.
	- Create ~.ssh folder for user2 ```$mkdir /home/user2/.ssh```
	- Change permission ```$chmod 777 /home/user2/.ssh```
	- opy pub key to home/.ssh user2 ```$cp authorized_keys /home/user2/.ssh/```
	- Change Owner ```$chown user2:user2 .ssh``` and ```$chown user2:user2 authorized_keys```
	- Try logging with user2 ```$ssh -p '222' 'user2@10.12.254.253'```

### Firewalling and Security Config <a id="Firewalling_and_Security_Config"></a>
1. You have to set the rules of your firewall on your server only with the services used outside the VM.
- The Services used outside my Server:
	- http 80
	- https 443
	- FTP 21
	- SSH 222

- I used iptables rules
	- To List the Rules ```$iptables -L```
	- To Flush Rules ```$iptables -F```
	- Delete chaines ```$iptables -X```
	- Delete rule ```$iptables -D INPUT (number of rule)```

	- Allow input trafic for Connection already established ```iptables -A INPUT -m conntrack --ctstate ESTABLISHED -j ACCEPT```
	- Allow outside services
	```
	iptables -A INPUT -p tcp --dport 222 -j ACCEPT
	iptables -A INPUT -p tcp --dport 80 -j ACCEPT
	iptables -A INPUT -p tcp --dport 443 -j ACCEPT
	iptables -A INPUT -p tcp --dport 21 -j ACCEPT
	iptables -A INPUT -p tcp --dport 25 -j DROP
	```

	- Save Iptables Rules
	```
	$sudo apt-get install iptables-persistent
	$service netfilter-persistent start
	$iptables-save >/etc/iptables/rules.v4
	```

2. You have to set a DOS (Denial Of Service Attack) protection on your open ports of your VM.
- Set DDOS protection rule for all open tcp ports
``` $iptables -A INPUT -p tcp -m connlimit --connlimit-above 3 -j REJECT --reject-with tcp-reset```
- Set DDOS protection for the port 80 and 443
```$iptables -I INPUT -p tcp --dport 80 -m connlimit --connlimit-above 20 -j DROP```
```$iptables -I INPUT -p tcp --dport 443 -m connlimit --connlimit-above 20 -j DROP```
- Set DDOS protection for the new SSH port "222"
```$iptables -I INPUT -p tcp --dport 222 -m state --state NEW -m recent --set```
```$iptables -I INPUT -p tcp --dport 222 -m state --state NEW -m recent  --update --seconds 60 --hitcount 4 -j DROP```
- Test SSH Brute Force
```$while true ; do nc 10.12.100.229 222 & ; done```

3. You have to set a protection against scans on your VM’s open ports.
- Set Portscan protection on open ports of your VM.
	- List open ports ```$netstat -tulpn | grep LISTEN```
	- I used psad, look at this [doc](https://www.digitalocean.com/community/tutorials/how-to-use-psad-to-detect-network-intrusion-attempts-on-an-ubuntu-vps). 

4. Stop the services you don’t need for this project.
- Show all services ```$service --status-all``` or ```$systemctl list-units --type service | grep running```
- Stop service ```$systemctl restart service_name```

### Script Config <a id="Script_Config"></a>
1. Create a script that updates all the sources of package, then your packages and qui log l’ensemble dans un fichier nommé /var/log/update_script.log.
```$vim /root/script/update_script```
```
sudo apt-get -y update >> /var/log/update_script.log
sudo apt-get -y upgrade >> /var/log/update_script.log
```

2. Create a scheduled task for this script once a week at 4AM and every time the machine reboots.
```$vim /etc/crontab```
```
0  4    * * 1   root    sh /root/script/update_script
```

3. Make a script to monitor changes of the /etc/crontab file and sends an email to root if it has been modified. 
```
md5sum /etc/crontab file > /root/script/origine_cron

md5sum /etc/crontab file >> /root/script/new_cron
if(new_cron != origine_cron)
{
  new_cron > origine_cron
  send mait to root that cron file is modified 
}
```

4. Create a scheduled script task every day at midnight.
```$vim /etc/crontab```
```
0  0    * * *   root    sh /root/script/monitor_script
```

## Web Part <a id="Web_Part"></a>
1. Set a web server (Nginx or Apache) who should BE available on VM’s @IP or host (init.login.com for exemple).
- Install Apache ```$apt-get install apache2```

2. Set a self-signed SSL on all of your services
- Install OpenSSL ```$apt-get install openssl```
- Create ssl folder if not existe ```$mkdir /etc/apache2/ssl ```
- Auto-signer ton certificat ```$openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes```
- Enable SSL mode on apache2 ```$a2enmod ssl```
- Creat VirtualHost for 443 port ```$vim /etc/apache2/site-availables/website.conf```
```
<VirtualHost *:80>
ServerName      10.12.100.8
# On redirige le port HTTP vers le port HTTPS
Redirect        / https://10.12.100.8
</VirtualHost>

<VirtualHost *:443>
ServerName      10.12.100.8
DocumentRoot    /var/www/html

SSLEngine on
SSLCertificateFile    /etc/apache2/ssl/cert.pem
SSLCertificateKeyFile /etc/apache2/ssl/key.pem
# Facultatif, ici, on dit qu'on accepte tout les protocoles SSL sauf SSLv2 et SSLv3 (dont on accepte que le TLS ici)
SSLProtocol all -SSLv2 -SSLv3
# Facultatif, on dit que c'est le serveur qui donne l'ordre des algorithmes de chiffrement pendant la négociation avec le client
SSLHonorCipherOrder on
# Facultatif, algorithme de chiffrement disponibles (ne pas être trop méchant sinon beaucoup de navigateur un peu ancien ne pourront plus se connecter)
SSLCipherSuite ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES
</VirtualHost>
```

- Enable the VirtualHost ```$a2ensite website.conf```
- Disable Other VirtualHost default configs ```$a2dissite 000-default.conf```
- Restart Apache ```$service apache2 restart```

3. Set a webapp (with any language you wan) from those choices -> login page.
- Put your code source in the WWW folder ```$cd /var/www/html/```

## Deployment Part <a id="Deployment_Part"></a>
1. Propose a functional solution for deployment automation.
- Install FTP Server ```$apt-get install vsftpd```
- Add user for FTP Server ```$adduser ftpuser```
- Change home folder ```$usermod -d /var/www ftpuser```
- Change owner ```$chown ftpuser:ftpuser /var/www/``` and ```$chown ftpuser:ftpuser /var/www/html/```
- Config vsftp in ```$vim /etc/vsftpd.conf```
2. Create back file config ```$cp /etc/vsftpd.conf /etc/vsftpd.conf.bak```
3. Allow ftp users to write to the server ```write_enable=YES``` in ```$vim /etc/vsftpd.conf /etc/vsftpd.conf``` file.
4. Deny ftp users browsing outside ```chroot_local_user=YES```
5. Give the correct permissions to the uploaded files ```local_umask=022```
6. Force ftp to show files begin with dot like ".htaccess" add to the bottom this line ```force_dot_files=YES```
7. Bypass the writable check in ftp server add to the bottom this line ```allow_writeable_chroot=YES```
