## Helpful Links 
### Verify your download
- input => ```echo "ea6ccb5b57813908c006f42f7ac8eaa4fc603883a2d07876cf9ed74610ba2f53 *ubuntu-18.04.2-live-server-amd64.iso" | shasum -a 256 --check```
- output => ubuntu-18.04.3-live-server-amd64.iso: OK

### Network config => NOT
- bridge or NAT or ... look at -> https://www.virtualbox.org/manual/ch06.html
## Basic writing and formatting syntax
https://help.github.com/en/articles/basic-writing-and-formatting-syntax#headings

- List open ports ```netstat -tulpn | grep LISTEN```

- Show all services ```service --status-all```

- crontab  https://crontab.guru/

## questions & remarques
- re-verif /etc/apache2/site-availables/ and /etc/apache2/site-enabled !
- re-config @IP and re-verif all services

portsentry
psad
ufw
fail2ban




atd.service                        loaded active running Deferred execution scheduler
blk-availability.service           loaded active exited  Availability of block devices
lvm2-lvmetad.service               loaded active running LVM2 metadata daemon
lvm2-monitor.service               loaded active exited  Monitoring of LVM2 mirrors, snapshots etc. using dmeventd or progress polling

cloud-config.service               loaded active exited  Apply the settings specified in cloud-config
cloud-final.service                loaded active exited  Execute cloud user/final scripts
cloud-init-local.service           loaded active exited  Initial cloud-init job (pre-networking)
cloud-init.service                 loaded active exited  Initial cloud-init job (metadata service crawler)




snapd.service                      loaded active running Snappy daemon



lxcfs.service                      loaded active running FUSE filesystem for LXC
lxd-containers.service             loaded active exited  LXD - container startup/shutdown
for logical partitions
