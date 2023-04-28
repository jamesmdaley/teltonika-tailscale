# Install Tailscale on Teltonika RUTX Routers
This outlines how to install Tailscale on Teltonika RUTX (OpenWRT) Routers.
Teltonika routers are currently running on OpenWRT 21 and therefore the Tailscale package is an old version 1.24.
This installs the latest Tailscale client on a Teltonika router and presents itself as a subnet-router.  It also ensures tailscale starts when rebooted.
This is designed so updates are undertaken manually.
I haven't found another method so hopefully this helps.  


### Login via SSH
```
 ssh root@ipaddress
 gui password
 ```
  
### Download the latest Tailscale client 

  First check the arch for your device here: </br>
  https://www.tme.com/html/EN/teltonika-modules-routers-and-gateways/ramka_26942_EN_pelny.html </br>
  
  Then update the line below with the suitable version from here: https://pkgs.tailscale.com/stable/#static </br>
  
  Example below is for ARM devices which is suitable for the RUTX11 </br>
  
  ```
  cd .. 
  cd /tmp
  curl https://pkgs.tailscale.com/stable/tailscale_1.40.0_arm.tgz --output tailscale.tgz
  ```

### Extract the package
  ```
  tar x -zvC /tmp -f tailscale.tgz
  ```
 
### Delete the package now its extracted to save disk space
  ```
  rm tailscale.tgz
  ```

### Then we move the tailscale and tailscaled binaries over to /usr/bin/ where they will live
  ```
  cd tailsca~1
  cp tailscale /usr/bin
  cp tailscaled /usr/bin
  ```
  
### Remove extracted folder to save space
  ```
  cd ..
  rm tailsca~1 -R
  ```

### Utilise the service startup script from Adyanth (thank you!) and create the script, then paste it there:
  https://github.com/adyanth/openwrt-tailscale-enabler/blob/main/etc/init.d/tailscale
  ```
  vim /etc/init.d/tailscale
  ```

### Set permissions on the files created/downloaded to execute
  ```
  chmod 775 /etc/init.d/tailscale
  chmod 775 /usr/bin/tailscale
  chmod 775 /usr/bin/tailscaled
  ```

### Update OPKG and install dependency packages
  ```
  opkg update
  opkg install libustream-openssl ca-bundle kmod-tun
  ```
  Ignore kernal errors if shown.
  

### Modify the firewall config file
  ```
  vim /etc/config/firewall
  ```
  Then append the following at the bottom:
  
 ```
  config zone
    option device 'tailscale+'
    option name 'tailscale'
    option src 'wan'
    option input 'ACCEPT'
    option forward 'REJECT'
    option output 'REJECT'
 ```
    
### Startup tailscale and enable at boot
  ```
  /etc/init.d/tailscale start
  /etc/init.d/tailscale enable
  ```

### Run tailscale up and authenticate
  ```
  tailscale up --advertise-routes=10.0.0.0/24 --accept-dns=false  
  ```
### Reboot (Important)
  ```
  reboot
  ```
  
## Post-reboot
Tailscale will start and advertise routes once rebooted. </br>
If you do a firmware update, it will remove the tailscale config so be careful!
