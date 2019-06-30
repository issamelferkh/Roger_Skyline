# roger_skyline_1
## Chapitre V: Partie obligatoire
### V.1 Soyons plus que copains !
Suivre Slash16 sur Facebook, Twitter et Linkedin.
### V.2 Partie VM
#### Verify your download
- input => ```echo "ea6ccb5b57813908c006f42f7ac8eaa4fc603883a2d07876cf9ed74610ba2f53 *ubuntu-18.04.2-live-server-amd64.iso" | shasum -a 256 --check```
- output => ubuntu-18.04.2-live-server-amd64.iso: OK
#### creating VM
- RAM -> 1024 Mo
- HDD -> 8Go -> virtual hard disk -> VDI -> Fixed Size
- Add OS -> Settings -> Storage -> IDE -> Choose your OS (Ubuntu server 18.04.2 (64) in my case)
#### network config:
bridge or NAT or ... look at -> https://www.virtualbox.org/manual/ch06.html
