# Prevent password requirement for sudo chown
This only for chown:
```
chmod u+s /bin/chown
sudo -i
visudo
pi ALL=NOPASSWD:/bin/chown
```
For all apps:
pi ALL=NOPASSWD: ALL
