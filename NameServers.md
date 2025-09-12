# Adding custom DNS servers
## Installing **resolvconf**
``` bash
sudo apt update
sudo apt install resolvconf
```
Testing work of **resolvconf**
``` bash
sudo systemctl status resolvconf.service
```
If not running
``` bash
sudo systemctl start resolvconf.service
```
``` bash
sudo systemctl enable resolvconf.service
```
``` bash
sudo systemctl status resolvconf.service
```
## Adding your name servers
``` bash
sudo nano /etc/resolvconf/resolv.conf.d/head
```
``` nano
nameserver 77.88.8.8
nameserver 8.8.8.8
```
Restarting
``` bash
sudo reboot
```
## Checking service running
``` bash
sudo systemctl status resolvconf.service
```
Checking name servers
``` bash
cat /etc/resolv.conf
```
