## Prerequisites
> [!NOTE]
> This is a modified version of assignment 3 part one that requires 2 servers and a load balancer.  
Install `nginx` and `ufw`.

## Step 1: Creating a New System User  

Running web server (or any other process or service) as a system user instead of a regular user or root adds additional security. Systems users can be restricted to only access files and directories needed for their process, limiting the damage that can be caused by a bug or error.  

Create a new system user "webgen" with the `useradd` command:
```
sudo useradd --system -s /usr/bin/nologin -d /var/lib/webgen -M webgen
```

This command will set `/var/lib/webgen` as the home directory of the system user but won't create it. Copy the `webgen` folder from this repository into `/var/lib/` to create the home directory.  

## Step 2: Createing webgen's Home Directory
Clone the directory `webgen` into the path `/var/lib`. Change the file ownership the directory to `webgen` using `chown -R`. Lastly, use `chmod` to give the owner and group of `webgen/bin/generate_index` execute permissions.

## Step 3: Enabling `generate-index` Service and Timer Unit Files
The `generate-index` timer and service file will run the script `generate_index` in `/var/lib/webgen/bin/` everyday at 5 AM PST.  

Clone `generate-index.service` and `generate-index.timer` into `/etc/systemd/system/`. Enable and start both unit files.

You can check if the service for timer has been started and enabled using the command: `systemctl status <UNIT_NAME>`  

Once you've had the timer active, you can check if it has been triggering the service at the correct time using `journalctl -u generate-index`. Look for the time the service should be triggered (13:00:00 UTC/05:00:00 PST).  

## Step 4: Configuring and enabling nginx
Replace `nginx.conf` in `/etc/nginx/` with the copy in this repository. Then create the directoires `sites-available` and `enabled-sites` in `/etc/nginx/`. Clone `snapshot.conf` to `sites-avaialable` then open the file in a text editor. Change the IP for the directive `server_name` to the IP of the current server. Then create a symbolic link to it in `sites-enabled`. If you add a new server block or modify existing configurations, you can validate your configuration using `sudo nginx -t`.  

Once configuration is complete, enable the `nginx` service.  

## Step 5: Configring the Firewall Using ufw
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

**You after step 5, the web server will be active and you can connect to it using the droplet's IP in your browser. Repeat this with the other server and you'll be able to connect to both using the IP of the load balancer.**



