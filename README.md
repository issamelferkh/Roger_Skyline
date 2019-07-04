# Roger Skyline 1
## Chapitre V: Partie obligatoire
### V.1 Soyons plus que copains !
Suivre Slash16 sur Facebook, Twitter et Linkedin.
### V.2 Partie VM
#### Creating VM => NOT
- [x] RAM -> 1024 Mo
- [x] HDD -> 8Go -> virtual hard disk -> VDI -> Fixed Size
- [x] Add OS -> Settings -> Storage -> IDE -> Choose your OS (Ubuntu server 18.04.2 (64) in my case)
- [x] Avoir au moins une partition de 4.2 Go.
| list partitions ```lsblk```
- [ ] Updated
- [ ] All packages needed installed
### V.3 Partie Réseau et Sécurité => NOT
- [x] Create a non-root user to connect to the machine and work.
- [x] Use sudo.
- [x] Don't use DHCP -> config IP fixe with Netmask en /30.
| config file ```vim /etc/netplan/50-cloud-init.yaml```
| restart network ```sudo netplan --debug apply```
- [x] Change default port of SSH.
| config file ```vim /etc/ssh/sshd_config``` + restart ssh
| new port ```ssh -p 222 user@10.12.254.253```
- [ ] SSH access HAS TO be done with publickeys.
- [ ] SSH root access SHOULD NOT be allowed.
- [ ] Set rules of your firewall on your server -> only with the services used outside the VM.
- [ ] You have to set a DOS (Denial Of Service Attack) protection on your open ports of your VM.
- [ ] You have to set a protection contre scans on your VM’s open ports.
- [ ] Stop the services you don’t need for this project.
- [ ] Create a script that updates all the sources of package, then your packages and qui log l’ensemble dans un fichier nommé /var/log/update_script.log.
- [ ]  Create a scheduled task for this script once a week at 4AM and every time the machine reboots.
- [ ]  Make a script to monitor changes of the /etc/crontab file and sends an email to root if it has been modified. 
- [ ]  Create a scheduled script task every day at midnight.
## Chapitre VI: Partie optionnelle
### VI.1 Partie Web
- [ ]  Set a web server (Nginx or Apache) who should BE available on VM’s @IP or host (init.login.com for exemple).
- [ ]  You have to set a self-signed SSL on all of your services.
- [ ]  Set a webapp (with any language you wan) from those choices ->login page|display site|wonderful website that blow our minds.
### VI.2 Partie Déploiement
- [ ]  Propose a functional solution for deployment automation.




## Helpful Links 
### Verify your download
- input => ```echo "ea6ccb5b57813908c006f42f7ac8eaa4fc603883a2d07876cf9ed74610ba2f53 *ubuntu-18.04.2-live-server-amd64.iso" | shasum -a 256 --check```
- output => ubuntu-18.04.2-live-server-amd64.iso: OK
### Network config => NOT
- bridge or NAT or ... look at -> https://www.virtualbox.org/manual/ch06.html
## Basic writing and formatting syntax
https://help.github.com/en/articles/basic-writing-and-formatting-syntax#headings

## Tasks
### reorder all project tasks -> order logique dyal lkhedma 
### 3awed 9ra sujet mzn mzn bach nfahmo 
