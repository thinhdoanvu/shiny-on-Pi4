# Prevent password requirement for sudo chown
This only for chown:
```
chmod u+s /bin/chown
sudo -i
visudo
pi ALL=NOPASSWD:/bin/chown
```
For all apps:
```pi ALL=NOPASSWD: ALL```

## Set static IP Network for Raspb
```
sudo nano /etc/dhcpcd.conf  
# This is for LAN  
interface eth0  
static ip_address=10.9.0.10/28  
static routers=10.9.0.1  
static domain_name_servers=8.8.8.8 10.9.0.1

# This is for WAN
interface wlan0
static ip_address=192.168.0.200/24
static routers=192.168.0.1
static domain_name_servers=8.8.8.8 208.67.222.222
```
## How to auto run script after rebooting
```
chmod 777 /var/www/html/smartagri/*  
crontab -e
```
Choose: 1 for nano  
Adding this line into crontab  
```@reboot bash /var/www/html/smartagri/AutoRun.sh  ```
