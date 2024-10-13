# 1 - Cloud Setup

- Samll IOT proof-of-Concept Limitied budget
- What kind of resourse we need?

# 2 - Define 'resourse group'

1. creat resource group for Iot project
   1. Details
      1. subscription "Azure for student"
      2. Name 'resourse group'
      3. Chosen region => '(Europe) North Europe'
      4. Review + create
   2. click 'Create Button'

## 2.1 - Reserve resources for Iot project

find suitable resource. in this example, we will use virtual machines. It is flexible cloud resources for many use cases. It is not scalable alone.

1. Navigate and find 'VM' from azure
2. Create Virtual machines instance
   1. Subscription `Azure for student`
   2. Resource group: `{YOUR_RESOURCE_GROUP_NAME}`
   3. Identifiable and unique name( in internet) Virtual Machine Name:`{UNIQUE_NAME}`
   4. Region(note! limitiations): "`Europe North`"
   5. Availability options: "Availability zone"
   6. Zone options: deafult options
   7. Security type: defult
   8. image: "Ubuntu Server 24.04 LTS -x64 Gen2"
   9. Vm architecture: `x64`
   10. Run with Azure Spot discount `no selection`
   11. size:`standard_B1s`
   12. Authentication type:`SSH public key`
   13. Username: `{anything but Obvious}`
   14. SSH public key sources: `Use existing public key`
   15. SSH public key: `{YOUR_PUBLIC_SSH_KEY}`
   16. Public inbound porrts: `Allow selected ports`
   17. Select inbound ports: `HTP (80), HTTP(80),HTTPS (443)`

To create a new SSH keypair, use ssh-keygen.
Example:
`ssh-keygen -b 4096 -t rsa -C "newkey_name" -f ~/.ssh/newkey_name` in bash

`cat ~/.ssh/newkey_name.pub`

Copy the public key from the bash and paste it to the PUBLIC KEY place in VM creation.

## 2.2 Use Virtule Machine

1. Access the virtual machine remotely. Use SSH
   1. Remote login details
      1. Address (IP or FQDN)
         1. Navigate: Azure -> Resourse Group -> virtule machine -> Public IP Address
   2. Username
   3. SSH Keyfile
      1. Optionally the password for the SSH key

`ssh newkey_name@ip_addr_to_vm -i ~/.ssh/newkey_name`

# 3 - Cloud setup

1. Install web-server
   1. serving static Html
   2. Serving Rest API (reverse proxy)

# 3.1 Login to the Azure VM

- Get the `ip_addr_to_Vm`
  - my Ip_adr `20.93.68.24`
- rember username
- Rember ssh_key
  Also The password, if proctected key
- full command `ssh newkey_name @ip_addr_to_vm -i ~/.ssh/newkey_name `

# 3.2 Install Update

In Ubuntu, the update installation consist of two seperate command

- Download updates: `sudo apt update`
- install: `sudo apt upgrade -y`
  ` -y` - install without prompting

<h4>Download updates </h4>
`remote_user@vm:~$ sudo apt update
Hit:1 http://azure.archive.ubuntu.com/ubuntu noble InRelease
Get:2 http://azure.archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
Hit:3 http://azure.archive.ubuntu.com/ubuntu noble-backports InRelease
Hit:4 http://azure.archive.ubuntu.com/ubuntu noble-security InRelease
Fetched 126 kB in 1s (189 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
29 packages can be upgraded. Run 'apt list --upgradable' to see them.`

# Install web- servers

1. Find we-server
   1. install with `sudo apt install web_SERVICE_NAME`
2. configure web-server
   1.
   1. To serve stsatic Html
   1. To serve REST API (later)
3. check the firemail and routing rules
   1. WAN (internet) -> LAN (vm)
4. Test the web-server
   1. Use Browser or cURl

# Find web server

Compare popular web-servers. Consider Pros and Cons:

- Performance
- Security
- Ease of maintenance
- Cost
- Support for new features (e.g., HTTP3 QUICK)
  Nginx chosen for now
  Install with command: `sudo apt install -y nginx`

```
remote_user@vm:~$ sudo apt install nginx
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  linux-azure-cloud-tools-6.8.0-1014 linux-azure-headers-6.8.0-1014 linux-azure-tools-6.8.0-1014 linux-cloud-tools-6.8.0-1014-azure
  linux-headers-6.8.0-1014-azure linux-image-6.8.0-1014-azure linux-modules-6.8.0-1014-azure linux-tools-6.8.0-1014-azure
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  nginx-common
Suggested packages:
  fcgiwrap nginx-doc ssl-cert
The following NEW packages will be installed:
  nginx nginx-common
0 upgraded, 2 newly installed, 0 to remove and 2 not upgraded.
Need to get 552 kB of archives.
After this operation, 1596 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
...
Restarting services...

Service restarts being deferred:
 /etc/needrestart/restart.d/dbus.service
 systemctl restart networkd-dispatcher.service
 systemctl restart unattended-upgrades.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
remote_user@vm:~$
```

Check if nginx is running:
`sudo systemctl status nginx.service`

```
remote_user@vm:~$ sudo systemctl status nginx.service
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
     Active: active (running) since Tue 2024-09-17 09:49:02 UTC; 3min 34s ago
       Docs: man:nginx(8)
    Process: 78884 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 78886 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Main PID: 78888 (nginx)
      Tasks: 2 (limit: 1064)
     Memory: 1.7M (peak: 1.9M)
        CPU: 14ms
     CGroup: /system.slice/nginx.service
             ├─78888 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             └─78889 "nginx: worker process"

Sep 17 09:49:02 vm systemd[1]: Starting nginx.service - A high performance web server and a reverse proxy server...
Sep 17 09:49:02 vm systemd[1]: Started nginx.service - A high performance web server and a reverse proxy server.
remote_user@vm:~$
```

Summary - Nginx Service is running - Nginx service is enabled (starts on system boot)

Test if responding inside the Virtual machine: `curl http://localhost:80`

Response (default nginx landing page):

```
Welcome to nginx!

html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }



Welcome to nginx!
If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.

For online documentation and support please refer to
nginx.org.
Commercial support is available at
nginx.com.

Thank you for using nginx.
```

Test if the site is visible in the internet

- Use browser to go to `http://vm.public.ip.addr:80`

Should be successful in Azure environment

# Change the defult site

1. Read the webserver configuration
   1. Locate the root folder for STATIC files
2. Modify `index.html` in the webserver root folder
3. See the updated site in the browser

# 3.5.1 Nginx configurations

loction is `/etc/nginx/nginx.conf`

Use cat command to read the configuration

`cat /etc/nginx/nginx.conf`

The sites configurations are included from `/etc/nginx/sites-enabled/*`

List configurations in the sites enabled folder `ls -al /etc/nginx/sites-enabled`

```
remote_user@vm:~$ ls -al /etc/nginx/sites-enabled/
total 8
drwxr-xr-x 2 root root 4096 Sep 17 09:49 .
drwxr-xr-x 8 root root 4096 Sep 17 09:49 ..
lrwxrwxrwx 1 root root   34 Sep 17 09:49 default -> /etc/nginx/sites-available/default
```

Read the `default` site configuration:` cat /etc/nginx/sites-enabled/default`

The `default` site configuration says something like:

```
server {
        listen 80 default_server; # Listen for IPv4 at port 80
        listen [::]:80 default_server; # Listen for IPv6 at port 80
        # ...
        root /var/www/html; # The location for root files (THE STATIC FILES)
        # ...
        location / { # tells where to look for static files
            try_files $uri $uri/ =404;
        }
}
```

# 3.5.2 Modify the default index.html

Modify the`index.html` file at `/var/www/html`

Preparations:

1. Copy the default site `sudo cp /var/www/html/index.nginx-debian.html /var/www/html/index.html`
2. Check ownership `ls -al /var/www/html/`
3. For some environments, not Azure: Change ownership to `www-data` user sudo chown www-data: `/var/www/html/index.html`

<h3>Modify index.html</h3>

1. Open in nano text-editor: `sudo nano /var/www/html/index.html`
2. Modify content to your liking
3. Save the content
   1. Save -> `CTRL` + `X`
      Type `Y` to confirm name(filepath)
      Press `Enter` to save

Modified content

```
DIoTP

html { background-color: #f1f1f1; }



DIoTP - Projects
Landing page
```

# 3.6 Creating UI element (SVG)

1. Find reference image e.g. fuel-gauge at IoT Gauge
2. Go to Sketchpad
3. Take screenshot from fuel-gauge and drag it to sketchpad
   Windows: `Win` + `Shift` + `S`
   MacOS: `Cmd`+ `Shift` + `4`

Once the SVG is ready:

1. Embed it into an .html file or upload raw .svg
2. Upload the file to the server
   1. Cmd syntax `scp source_path destination_path`
   2. Usage (from local machine):` scp /path/to/your/image.svg username@azure.public.ip.addr:~/image.svg`
   3. Move the file to the webserver’s root directory
      1. Copy `cp /path/to/image.svg /var/www/html/new_filename.svg`

Check the results from your site

### Setup Azure VM for REST API

Steps:

1. Finish REST API
2. Login to the remote server
3. Install Node.JS (same version as locally (>=20 LTS))
4. Install cURL
5. Move the REST API files to VM
6. Start & Test the REST APi
7. Configure Reverse proxy to serve PAI to internet
8. Test API from the internet

#### S1 - Finish REST API

Compeleted on "Create REST API in "server.mjs" file"/

#### S2 - Login to the remote server

Go to Azure Portal: https://aka.ms/azureportal

Login to remote VM

My details:

```bash
ssh abhiproject@40.67.244.43
```

#### S3 - Install NodeJS v20

check for updates

```bash
sudo apt update
```

check available Node.js packages

```bash
sudo apt list -a node.js
```

https://github.com/nodesource/distributions
use this above link to install Node.js 20.x version

```bash
# Install curl
sudo apt install -y curl

# Download and setup
curl -fsSL https://deb.nodesource.com/setup_20.x -o nodesource_setup.sh

# Run nodesource setup scipt for Noe and setup
sudo -E bash nodesource_setup.sh

# might need installation step
sudo apt install -y node.js

# Print NodeJS version
node --version
# Should be >=20 .x


```

#### S4 - Move REST API files to VM

Move happens from local machine to the remote machine. Pay attention to the terminal (which machine are you working on...)

Prepare "~/projects" directory in virtual machine

```bash
mkdir ~/projects
mkdir ~/projects/cloud_api
# check if dir exist
ls ~/projects
ls ~/projecrs/cloud_api
```

Start from the local machine.

Send files with SCP:

```bash
scp -i ~/.ssh/abhiproject package.json abhiproject@40.67.244.43:~/projects/cloud_api
scp -i ~/.ssh/abhiproject -r src abhiproject@40.67.244.43:~/projects/cloud_api
# Should show the files in appropriate directory
```

command on remote machine

```bash
cd ~/projects/cloud_api
npm install
```

#### S5 - Start and test the Rest APi

Start cloud api in remote machine (terminal window 1)

```bash
npm run start
```

Open up new remote connection to the machine (terminal window 2) .Test the REST API using CURL.

```bash
ssh abhiproject@40.67.244.43
# once logged in on terminal window 2
# test if rest api working
curl "http://127.0.0.1:3000/data?value=1234"
# should respond with "Value recieved: 1234
```

#### S6 - Configure reverse proxy to serve API to the install

Modify the default site nginx configuration at "/etc/nginx/sites-enabled/default"

This requires sudo privileges. Test configuration and apply if syntax ok

```bash
sudo nano /etc/nginx/sites-enabled/default
# add the reverse proxy for cloud API (port 3000)
nginx -t # test nginx config
# restart nginx
sudo nginx -s reload
# another way to restart
sudo systemctl restart nginx
```

Modify nginx block for reverse prosxy

```nginx
#  server_name...
```

---

## DIOTP

- Best way to store event-based data efficiently.
- unix Timestamp and epoch conveter - https://www.epochconverter.com/

Install InfluxDB

-setup shh Turnel - to connect Azure VM

# SSh Directory `~/.ssh` configure

1. setup

```bash
nano ~/.shh/config
```

2.```bash
ssh diotp

```

in the config define SSH configuration

# Install influxdb

```

curl --silent --location -O \
https://repos.influxdata.com/influxdata-archive.key
echo "943666881a1b8d9b849b74caebf02d3465d6beb716510d86a39f6c8e8dac7515 influxdata-archive.key" \
| sha256sum --check - && cat influxdata-archive.key \
| gpg --dearmor \
| tee /etc/apt/trusted.gpg.d/influxdata-archive.gpg > /dev/null \
&& echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive.gpg] https://repos.influxdata.com/debian stable main' \
| tee /etc/apt/sources.list.d/influxdata.list

# Install influxdb

sudo apt-get update && sudo apt-get install influxdb2
sudo apt update
sudo apt install -y influxdb2
influx version
sudo systemctl enable influxdb
sudo systemctl start influxdb
sudo systemctl status influxdb
Configure InfluxDB
Edit configuration /etc/influx/influxdb.conf(may need correction).

`and Presh Q`

```

curl 'http://localhost:8086'
``

new terminal
`nano ~/.ssh/config`

# Start SSH Turnel for DashBord

- Run the Local turnnel
  `ssh diotp-vm-influx`

#
```

---

Data trainmisson
-SSH_CLINENT
-VM_public_ip_address
-Username or IDENTIYFILE

or use SSH config See the config:

```
cat ~/.ssh/config
```

to edit SSH_CONFIG , use Nano:

```bash
nano~/.ssh/config
```

Connect to the VPS:

```bash
ssh -i~/path/to/key username@ip.or.fqdn.here
```

or
ssh host

## Configure HTTPS

1. Check nginx configuration
2. `cat /etc/nginx/sites-enabled/default`
3. Check if HTTPS is allowed 2.https://certbot.eff.org/instructions?ws=nginx&os=pip
4. Update repositories
5. install packeg `sudo apt install python3 python3-venv libaugeas0`

```bash=
#shep1
cat /etc/nginx/sites-enabled/default
# step 2
#see instruction
# https://certbot.eff.org/instructions?ws=nginx&os=pip
#step 3 - fetch update
sudo apt update
# step 4 - install package
sudo apt install python3 python3-venv libaugeas0
# step 5
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip

# step 6
sudo /opt/certbot/bin/pip install certbot certbot-nginx

# Step 7
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot


#before step 8- make sure FQDN is pointed
#Step 8 - Make sure FQDN is pointed
#step 8
sudo certbot --nginx
1. Add email or run previous command
2. sudo certbot --nginx --register-unsafely-without-email
3. read ther Terms of Service `https://letsencrypt.org/documents/LE-SA-v1.4-April-3-2024.pdf`
4. Enter domain name(s) (comma seperated)


```

# Pointed PQDN
