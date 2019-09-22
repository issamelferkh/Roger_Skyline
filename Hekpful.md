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
