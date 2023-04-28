# Install Tailscale on Teltonika RUTX Routers
This outlines how to install Tailscale on Teltonika RUTX (OpenWRT) Routers.
Teltonika routers are currently running on OpenWRT 21 and therefore the Tailscale package is an old version 1.24.
This installs the latest Tailscale client on a Teltonika router and presents itself as a subnet-router.  It also ensures tailscale starts when rebooted.
This is designed so updates are undertaken manually.
I haven't found another method so hopefully this helps.  


### Login via SSH
  ssh root@ipaddress <br/>
  gui password <br/>
  
### Download the latest Taiscale client for arm devices (check the arch for your device)
  cd ..  <br/>
  cd /tmp <br/>
  curl https://pkgs.tailscale.com/stable/tailscale_1.40.0_arm.tgz --output tailscale.tgz <br/>
  update from the tailscale package source <br/>

### Extract the package
  tar x -zvC /tmp -f tailscale.tgz <br/>
 
### Delete the package now its extracted to save disk space
  rm tailscale.tgz <br/>

### Then we move the tailscale and tailscaled binaries over to /usr/bin/ where they will live
  cd tailsca~1 <br/>
  cp tailscale /usr/bin <br/>
  cp tailscaled /usr/bin <br/>
  
### Remove extracted folder to save space
  cd .. <br/>
  rm tailsca~1 -R <br/>

### Utilise the service startup script from Adyanth (thank you!) and create the script, then paste it there:
  https://github.com/adyanth/openwrt-tailscale-enabler/blob/main/etc/init.d/tailscale <br/>
  vim /etc/init.d/tailscale <br/>

### Set permissions on the files created/downloaded to run
  chmod 775 /etc/init.d/tailscale <br/>
  chmod 775 /usr/bin/tailscale <br/>
  chmod 775 /usr/bin/tailscaled <br/>

### Update OPKG and install dependency packages
  opkg update <br/>
  opkg install libustream-openssl ca-bundle kmod-tun <br/>
  ignore kernal errors <br/>

### Modify the firewall config file
  vim /etc/config/firewall <br/>
  append the following: <br/>
 
  config zone<br/>
    option device 'tailscale+'<br/>
    option name 'tailscale'<br/>
    option src 'wan'<br/>
    option input 'ACCEPT'<br/>
    option forward 'REJECT'<br/>
    option output 'REJECT'<br/>
    
### Startup tailscale and enable at boot
  /etc/init.d/tailscale start<br/>
  /etc/init.d/tailscale enable<br/>

### Run tailscale up and authenticate
  tailscale up --advertise-routes=10.0.0.0/24 --accept-dns=false<br/>
  

