- 6[After] Install VM
- 5[After] Network Config
- [final test] SSH Config
- 3[Not Yet] Script Config
- 4[Not Yet] Firewalling and Security Config
- 2[final test] Partie Web
- 1[final test] Partie Déploiement
- 7[Not Yet] All Tests

# Roger Skyline 1
## Chapitre V: Partie obligatoire
### V.1 Soyons plus que copains !
Suivre Slash16 sur 
- [x] Facebook
- [x] Twittter
- [x] Linkedin
### V.2 Partie VM
#### Creating VM => NOT
/Volumes/Storage/goinfre/iel-ferk/Roger_Skyline.vdi
- [x] RAM -> 1024 Mo
- [x] HDD -> 8Go -> virtual hard disk -> VDI -> Fixed Size
- [x] Add OS -> Settings -> Storage -> IDE -> Choose your OS (Ubuntu Server 18.04.3 LTS)
- [x] Avoir au moins une partition de 4.2 Go.
	|-> list partitions ```lsblk```netpl	
- [After] Updated
- [After] All packages needed installed

### V.3 Partie Réseau et Sécurité => NOT
#### V.3.1 Network Config
- [x] Create a non-root user to connect to the machine and work.
	|-> Create User ```adduser```
	|-> Add user to sudo group ```usermod -a -G sudo user1```
- [After] Don't use DHCP -> config IP fixe with Netmask en /30.
	|-> Config @IP in ```vim /etc/netplan/50-cloud-init.yaml```
	|-> Restart Network with ```sudo netplan --debug apply```

#### V.3.2 SSH Config
- [x] SSH config file in ```vim /etc/ssh/sshd_config```
	|-> Change default port ```#port 22``` to ```port 222```
	|-> Change protocol ```Protocol 2, 1``` to ```Protocol 2```, ssh2 is more secure that ssh1.
	|-> Root should not be allowed to access ```PermitRootLogin yes``` to ```PermitRootLogin no```
	|-> Disable password login ```#PasswordAuthentication yes``` to ```PasswordAuthentication no```
		and ```ChallengeResponseAuthentication yes``` to ```ChallengeResponseAuthentication no```
	|-> Pub key enable ```#PubKeyAuthentication no``` to ```PubKeyAuthentication yes```
	|-> Restart Service SSH ```systemctl restart ssh```
	|-> Try logging with new port ```ssh -p 222 user@10.12.254.253``` new port 

- [x] SSH access HAS TO be done with publickeys.
	|-> Generate SSH Keys in clien ```ssh-keygen```  
	|-> Copy pub key to server ```ssh-copy-id -p 222 user1@10.12.254.253``` 
	|-> Try logging with user1 ```ssh -p '222' 'user1@10.12.254.253'``` 
	|-> Doc => http://go2linux.garron.me/linux/2010/10/ssh-public-key-only-login-authentication-788/

- [x] SSH publickeys for new user without passwd.
	|-> Creat ~.ssh folder for user2 ```mkdir /home/user2/.ssh```
	|-> Change permission ```chmod 777 /home/user2/.ssh```
	|-> Copy pub key to home/.ssh user2 ```cp authorized_keys /home/user2/.ssh/```
	|-> Change Owner ```chown user2:user2 .ssh``` and ```chown user2:user2 authorized_keys```
	|-> Try logging with user2 ```ssh -p '222' 'user2@10.12.254.253'```

#### V.3.3 Script Config: Look at -> Script
- [ ] Create a script that updates all the sources of package, then your packages and qui log l’ensemble dans un fichier nommé /var/log/update_script.log.
- [ ]  Create a scheduled task for this script once a week at 4AM and every time the machine reboots.
- [ ]  Make a script to monitor changes of the /etc/crontab file and sends an email to root if it has been modified. 
- [ ]  Create a scheduled script task every day at midnight.

#### V.3.4 Firewalling and Security Config
	
- [ ] Set Firewall rules for allow only the services used outside the VM: Look at -> Firewall
- [ ] Set DOS protection on open ports of your VM: Look at -> Firewall
- [ ] Set Portscan protection on open ports of your VM.
	|-> List open ports ```netstat -tulpn | grep LISTEN```
	|-> http://www.auxnet.org/index.php/the-news/216-how-to-protect-from-port-scanning-and-smurf-attack-in-linux-server-by-iptables
	|-> https://it.toolbox.com/question/how-to-block-port-scanning-tools-and-log-them-with-iptables-051115
- [ ] Stop the services you don’t need for this project.
	|-> Show all services ```service --status-all```
	|-> Run only: http(80), https(443), ssh(222), ftp(21)



## Chapitre VI: Partie optionnelle
### VI.1 Partie Web
- [x]  Set a web server (Nginx or Apache) who should BE available on VM’s @IP or host (init.login.com for exemple).
	|-> Install Apache ```apt-get install apache2```
	|-> WWW Folder is in ```cd /var/www/html/```
- [x]  Set a self-signed SSL on all of your services: Look at -> OpenSSL
- [x]  Set a webapp (with any language you wan) from those choices -> login page.


### VI.2 Partie Déploiement
- [x]  Propose a functional solution for deployment automation.
	|-> Install FTP Server ```apt-get install vsftpd```
	|-> Add user for FTP Server ```adduser ftpuser```
	|-> Change home folder ```usermod -d /var/www ftpuser```
	|-> Change owner ```chown ftpuser:ftpuser /var/www/``` and ```chown ftpuser:ftpuser /var/www/html/```
	|-> Config vsftp in ```vim /etc/vsftpd.conf```
creat bak file config ```cp /etc/vsftpd.conf /etc/vsftpd.conf.bak```
Allow ftp users to write to the server ```write_enable=YES```
Deny ftp users browsing outside ```chroot_local_user=YES```
Give the correct permissions to the uploaded files ```local_umask=022```
Force ftp to show files begin with dot like ".htaccess" add to the bottom this line ```force_dot_files=YES```
Bypass the writable check in ftp server add to the bottom this line ```allow_writeable_chroot=YES```
