# Secure Your PiHole: DNS Blackhole in a Cloud Service

In this course, we take a look at DNS Blackholing to prevent drive-by exploitation of computer systems and get back a bit of our bandwidth. 
Using the PiHole project and a droplet from Digital Ocean, we can quickly and effectively block up to 50% of unnecessary traffic on our networks! 

## Install WinTerminal

- Open the Microsoft Store
- Search for `Windows Terminal`
- Click `Windows Terminal`
- Click `Install`

## Generate an SSH Key

- Launch the `Windows Terminal` app
- Run the command `ssh-keygen.exe`
- Press enter to save in the `$env:userprofile/.ssh` directory
- Enter a passhrase if desired
- Validate your key creation with the command `Get-ChildItem $env:userprofile/.ssh`

## Create a DigitalOcean Account

- Navigate to DigitalOcean.com
- Click `Sign Up`
- Register with your prefered method
- Click your account Icon in the top right corner
- Click `My Account`
- Click the `Security` tab and `Add SSH Key`
- In your terminal, run the below command  
`Get-content ~/.ssh/id_rsa.pub`
- Copy the output of the above command into the `SSH key content` area.
- Give the key a unique and descriptive name.

## Create a Droplet with your SSH key

- Select `Create` > `Droplets`
- Choose `Debian` > `10 x64`
- Choose `Shared CPU` > `Basic`
- Choose `CPU Options` > `Regular Intel with SSD`
- Choose `$5/mo` option
- Choose a datacenter region, closer is better.
- In the `Authentication` field, choose `SSH Keys`
- Choose a unique and memorable `hostname`
- Click `Create Droplet`

## SSH into Your New Cloud Server

- Copy the droplet's IP address
- In the winterm, type `ssh root@YOUR.SERVER.IP`
- Accept the fingerprint of your new server

## Create a Root Password

- Run `passwd`
- Use your password solution to generate a strong and unique password

## Remove Root Password Login

- Using `nano`, modify the `PermitRootLogin` setting from `Yes` to `without-password` or `no`.
- Save the file using `Ctrl + O` and `Ctrl + X`

<mark> Note: If you choose `no`, your server will no longer be accessible via SSH, without generating new unprivileged users. In this case, all instructions would need to be completed through the DigitalOcean Console.  </mark>

## Upgrade Your Server

- Run the below commands  
`apt update && apt upgrade -y`

## Install Missing Packages

`apt install curl`

## Get your IP Address

- In the winterm, run
`Invoke-WebRequest -UseBasicParsing ifconfig.me`
- Copy the ip address returned in the `Content` field.

## Create a UFW FW rule

- In your ssh session, run `apt install ufw`
- Run the following commands paying special attention to insert the right commands.

````bash
ufw allow from YOUR.IP.ADDRESS to any port 22 proto tcp
ufw allow from YOUR.IP.ADDRESS to any port 53 proto udp
ufw allow from YOUR.IP.ADDRESS to any port 80 proto tcp
ufw enable
ufw deny from any to any
ufw status verbose numbered
````

## DigitalOcean Documentation

DigitalOcean has some of the best documentation that's ever been written. It is extremely thorough, direct, and devoid of pictures.
You should strive to make your documentation like theirs.

As an example, check out their [UFW Essentials](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

## Install PiHole

- Visit [Pi-hole.net](https://pi-hole.net/)
- Click `Install`
- Copy the `One-Step Automated Install`

````bash
curl -sSL https://install.pi-hole.net |bash
````

<mark> Note: Downloading anything from the internet and piping it to `bash` is an inherently bad idea. If you wanted to check out the script before running it, simply drop the `| bash` and redirect the result to a file to review the script before executing.
i.e., `curl -sSL https://install.pi-hole.net > script.sh` </mark>

- Answer the prompts
- Choose an Interface > `eth0`
- Select Upstream DNS Provider > `OpenDNS`
- BlockList > `Stephen's`
- Install Web Admin Interface > `On`
- Install Web Server Lighttpd > `On`
- Log Queries > `On`
- Privacy Mode > `Show Everything`
- Copy Password from PiHole Install Screen

## Install AdLists

- Navigate to Your.Server.IP/admin
- Click `Login` and enter the password from the Pihole Install Screen
- Click `Settings` and select `Teleporter`
- Uncheck all the check boxes under `Restore` except `Adlists`
- Choose the .GZ provided for this course
- Click `Restore`

## Update Gravity

- Click `Tools` > `Update Gravity`
- Do Not Navigate Away
- Once Complete, go to your `Dashboard`

## Point your Router @ your PiHole

This will be dependent on the device. However, a quick DuckDuckGo search of your router model along with terms like `custom dns server` will likely resolve this.

## Check Your Work

- Add a Domain to your Blocklist
- Attempt to visit that Domain, resolution should fail
- Remove it from your blocklist
