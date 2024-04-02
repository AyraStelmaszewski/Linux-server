# Linux-server
Simulation of a linux server for a company with services as DNS, DHCP, HTTP+ and mariaDB.


# Linux network services

## Project context 
The local library in your little town has no funding for Windows licenses so the director is considering Linux. Some users are sceptical and ask for a demo. The local IT company where you work is taking up the project and you are in charge of setting up a server and a workstation.
To demonstrate this setup, you will use virtual machines and an internal virtual network (your DHCP must not interfere with the LAN).

You may propose any additional functionality you consider interesting.

## Must Have

Set up the following Linux infrastructure:

1. One server (no GUI) running the following services:
    - DHCP (one scope serving the local internal network)  isc-dhcp-server
    - DNS (resolve internal resources, a redirector is used for external resources) bind
    - HTTP+ mariadb (internal website running GLPI)
    - **Required**
        1. Weekly backup the configuration files for each service into one single compressed archive
        2. The server is remotely manageable (SSH)
    - **Optional**
        1. Backups are placed on a partition located on  separate disk, this partition must be mounted for the backup, then unmounted

2. One workstation running a desktop environment and the following apps:
    - LibreOffice
    - Gimp
    - Mullvad browser
    - **Required** 
        1. This workstation uses automatic addressing
        2. The /home folder is located on a separate partition, same disk 
    - **Optional**
        1. Propose and implement a solution to remotely help a user

## Performance criteria
The teams must give a sense of confidence to the audience when presenting their demo,
the documentation must be clear, structured and must provide all elements of configuration.

## Evaluation methods
- Quality of the documentation and the live demo 
- All required items are implemented are working
- Optional items are implemented and working 
- The team demonstrates good organisation and planning
- The team explains and justifies the various choices they made
- Tests were performed to ensure the configuration is working 
- The team demonstrate a solid knowledge of the Linux environment when answering questions 

## Deliverables
a documentation in word format named LinuxBrief_firstname1_firstname2.docx,
a summary in English (at least one page)
Live demonstration in front of the group

Mac tips : <br>
| => maj+option+l <br>


# Process : 
# Set up a secured SSH without password 

1) In our case we'll run that server on a kali linux vm hosted with UTM. 
2) We want to connect our hosts to the vm so we'll verify if SSH is enable, if not, start it up.
```bash
sudo ssh service status OR (depend of Ubuntu version) sudo systemctl status ssh
```
```bash
sudo ssh service restart OR (depend of Ubuntu version) sudo systemctl restart ssh
```
3) Make the connexion between hosts, my vm IP is 192.168.64.2 and user ayra so : 
```bash
ssh ayra@192.168.64.2 
```
4) Change the host name of your server  
```bash
hostnamectl set-hostname libraryserver
```
5) We'll configure our SSH connexion with a key so password will be enable. It avoids people brute forcing our ssh password.   
```bash
ssh-keygen
```
6) We'll copy our SSH key directly to the server from our host machine :   
```bash
ssh-copy-id -i /Users/dotcom/.ssh/id_rsa.pub ayra@192.168.64.3
```
7) Now we can connect without ssh password but ssh password still available. Desactivate them with :
```bash
sudo vim /etc/ssh/sshd_config
```
```bash
find the line and change it to no => PasswordAuthentication no 
```
8) Restart ssh service to make changes working with :
```bash
sudo systemctl restart ssh
```
9) Sometimes it's better to reboot the machine to make all changes. Do it with :
```bash
sudo reboot
```
10) Now we established a secure ssh connexion without password.

# Set up DNS in linux server

1) Go to /etc/bind, it's where DNS is located. 
2) Run this command (in /etc/bind) to generate a dns configuration files with authentication etc
```bash
ddns-confgen
```
3) As we want to have unique key for each hosts, will change the key name (which is by default "ddns-key") to dhcp1.templab.lan (care, this cmd will automaticaly change the password)
```bash
ddns-confgen -k dhcp1.templab.lan
```
4) As we want to have unique key for each hosts, will change the key name (which is by default "ddns-key".
```bash
ddns-confgen
```


# Set up DHCP in linux server

1) 
