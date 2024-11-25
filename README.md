# Droplet IP
```
146.190.119.238
```

## Prerequisites
Install `nginx` and `ufw`.

## Step 1: Creating a New System User  

Running web server (or any other process or service) as a system user instead of a regular user or root adds additional security. Systems users can be restricted to only access files and directories needed for their process, limiting the damage that can be caused by a bug or error.  

Create a new system user "webgen" with the `useradd` command:
```
sudo useradd --system -s /usr/bin/nologin -d /var/lib/webgen -M webgen
```

This command will set `/var/lib/webgen` as the home directory of the system user but won't create it. Copy the `webgen` folder from this repository into `/var/lib/` to create the home directory.  

## Step 2: Enabling `generate-index` Service and Timer Unit Files
The `generate-index` timer and service file will run the script `generate_index` in `/var/lib/webgen/bin/` everyday at 5 AM PST.  

Clone `generate-index.service` and `generate-index.timer` into `/etc/systemd/system/`. Enable and start both unit files.

You can check if the service for timer has been started and enabled using the command:
```
systemctl status <UNIT_NAME>
```  
put a screen shot of output here -s   

Once you've had the timer active, you can check if it has been triggering the service at the correct time using `journalctl -u generate-index`. Look for the time the service should be triggered (13:00:00 UTC/05:00:00 PST).  

## Step 3: Configuring and enabling nginx
Replace `nginx.conf` in `/etc/nginx/` with the copy in this repository. Then create the directoires `sites-available` and `sites-enabled` in `/etc/nginx/`. Clone `snapshot.conf` to `sites-avaialable` and create a symbolic link to it in `sites-enabled`. Using separate files for the server blocks instead of writing it in `nginx.conf` makes it easier maintain and manage each site. If you add a new server block or modify existing configurations, you can validate your configuration using `sudo nginx -t`.  

Once configuration is complete, enable the `nginx` service.  

## Step 4: Configring the Firewall Using ufw
Enable SSH and HTTP connections from any IP using these commands:
```
sudo ufw allow ssh
sudo ufw allow http
```
Then limit SSH connections using:
```
sudo ufw limit ssh
```

Before enabling the firewall, check the rules you've added using `sudo ufw show added`.   
Enable the firewall with `sudo ufw enable`. After turning it on, you can check what its status with `sudo ufw status verbose`.  

**You after step 4, the web server will be active and you can connect to it using the droplet's IP in your browser.**



