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

1) In our case we'll run that server on a Ubuntu Server vm hosted with UTM. 
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

# Set up DHCP in linux server
1) Install dhcp on the server isc-dhcp-server
```bash
sudo apt-get install isc-dhcp-server
```
2) Configure the .conf (/etc/dhcp/dhcpd.conf) file with your network, in our case we'll use this :
```bash
subnet 192.168.64.0 netmask 255.255.255.248 {
  range 192.168.64.4 192.168.64.6;
  option domain-name-servers 1.1.1.1, 8.8.8.8;
  option subnet-mask 255.255.255.248;
  option routers 192.168.64.1;
  default-lease-time 3600;
  max-lease-time 7200;
}
```
3) It will be the offical dhcp server for our local network so allow authoritative;
```bash
authoritative;
```
4) To check the status of our server we can use : 
```bash
systemctl status isc-dhcp-server
```
5) We'll active a firewall ufw and allow port 67 for our dhcp server :
```bash
sudo ufw start
```
```bash
sudo ufw allow 67
```
```bash
sudo ufw restart
```
```bash
sudo ufw status
```
6) We're using ssh on default port so let's allow ssh to ufw : 
```bash
sudo ufw allow ssh
```
7) You have to allow rights for dhcp on /var/lib/dhcp/dhcpd.leases file.
8) You can start dhcp server listener on port 67 with your specific interface, in our case enp0s1
```bash
sudo tcpdump -vv -n -i enp0s1 port 67
```
9) Lunch another vm on your network and it's ip will be assigned with our dhcp configuration.
10) In the cli where we lunched the server we can see a connexion have been made.
```bash
12:31:31.064050 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto UDP (17), length 322)
    0.0.0.0.68 > 255.255.255.255.67: [udp sum ok] BOOTP/DHCP, Request from fa:1e:74:d8:e0:bb, length 294, xid 0x25bdfeb6, secs 1, Flags [none] (0x0000)
	  Client-Ethernet-Address fa:1e:74:d8:e0:bb
	  Vendor-rfc1048 Extensions
	    Magic Cookie 0x63825363
	    DHCP-Message (53), length 1: Request
	    Client-ID (61), length 7: ether fa:1e:74:d8:e0:bb
	    Parameter-Request (55), length 17: 
	      Subnet-Mask (1), Time-Zone (2), Domain-Name-Server (6), Hostname (12)
	      Domain-Name (15), MTU (26), BR (28), Classless-Static-Route (121)
	      Default-Gateway (3), Static-Route (33), YD (40), YS (41)
	      NTP (42), Unknown (119), Classless-Static-Route-Microsoft (249), Unknown (252)
	      RP (17)
	    MSZ (57), length 2: 576
	    Requested-IP (50), length 4: 192.168.64.4
	    Server-ID (54), length 4: 192.168.64.3
	    Hostname (12), length 4: "kali"
12:31:31.068523 IP (tos 0x10, ttl 128, id 0, offset 0, flags [none], proto UDP (17), length 328)
    192.168.64.3.67 > 192.168.64.4.68: [udp sum ok] BOOTP/DHCP, Reply, length 300, xid 0x25bdfeb6, secs 1, Flags [none] (0x0000)
	  Your-IP 192.168.64.4
	  Server-IP 192.168.64.3
	  Client-Ethernet-Address fa:1e:74:d8:e0:bb
	  Vendor-rfc1048 Extensions
	    Magic Cookie 0x63825363
	    DHCP-Message (53), length 1: ACK
	    Server-ID (54), length 4: 192.168.64.3
	    Lease-Time (51), length 4: 3600
	    Subnet-Mask (1), length 4: 255.255.255.248
	    Domain-Name-Server (6), length 8: 1.1.1.1,8.8.8.8
	    Default-Gateway (3), length 4: 192.168.64.1

```
11) To verify the connexion you can also use this cmd to list all ip deserved by your dhcp server : 
```bash
dhcp-lease-list
```
**Debug**
- Monitor errors in real time 
```bash
sudo tail -f /var/log/syslog /var/log/messages
```
# Set up DNS in linux server
1) We'll use bind9 to build our dns so install it
```bash
sudo apt install bind9
```
3) Stop and disable systemd, default dns services
```bash
systemctl disable systemd-resolved.service 
```
```bash
systemctl stop systemd-resolved.service
```
4) We can verify it's stopped with dig cmd
```bash
dig google.com
```
5) We'll configure our resolv.conf file in /etc/resolv.conf with this string
```bash
namerserver 127.0.0.1
```
6) We'll configure our /etc/bind/named.conf.options files
```bash
         forwarders {
                192.168.64.3;
         };
```
7) Restart the server
```bash
systemctl restart bind9
```
8) We'll create a zones folder in /etc/bind/ to manage our zone. Take original synthax from db.local in /etc/bind/ to make our configuration.
```bash
ayra@libraryserver:/etc/bind$ sudo cp db.local zones/db.librarykali
```
```bash
;
; BIND data file for local loopback interface
;
$ORIGIN librarykali.
$TTL    604800
@       IN      SOA     librarykali. root.librarykali. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      nsl.librarykali.
ubuntu.librarykali.     IN      A       192.168.64.3
nsl.librarykali.        IN      A       192.168.64.3
librarykali.            IN      A       192.168.64.3
```
9) Allow udp on the ufw firewall
```bash
sudo ufw allow 53/udp
```
10) We should have a correct answer with ip related with dig now.
```bash
dig ubuntu.librarykali
```
```bash
;; ANSWER SECTION:
ubuntu.librarykali.	604800	IN	A	192.168.64.3
```
11) Install apache2 for the webpage
```bash
sudo apt-get install apache2
```
12) Allow tcp to the ufw firewall
```bash
sudo ufw allow 80/tcp
```
13) On the client device, add the IP of the DNS server in the Network configuration
```bash
192.168.64.3
```
14) We are able to browse http://librarykali from our host machine within the network through dchp.


# GLPI 
**GLPI is an open source IT Asset Management, issue tracking system and service desk system. This software is written in PHP and distributed as open-source software under the GNU General Public License. GLPI is a web-based application helping companies to manage their information system.**

```bash
sudo apt-get install apache2 php mariadb-server
```
```bash
sudo apt-get install php-xml php-common php-json php-mysql php-mbstring php-curl php-gd php-intl php-zip php-bz2 php-imap php-apcu
```
```bash
sudo mariadb_secure_installation
```
```bash
sudo mariadb -u root -p
```
```bash
CREATE DATABASE db_glpi;
GRANT ALL PRIVILEGES ON db_glpi.* TO glpi_admin@localhost IDENTIFIED BY "YourPassword";
FLUSH PRIVILEGES;
EXIT
```
```bash
wget https://github.com/glpi-project/glpi/releases/download/10.0.10/glpi-10.0.10.tgz
```
```bash
sudo tar -xzvf glpi-10.0.10.tgz -C /var/www/
```
```bash
sudo chown www-data /var/www/glpi/ -R
```
```bash
sudo mkdir /etc/glpi
```
```bash
sudo chown www-data /etc/glpi/
```
```bash
sudo mv /var/www/glpi/config /etc/glpi
```
```bash
sudo mkdir /var/lib/glpi
```
```bash
sudo chown www-data /var/lib/glpi/
```
```bash
sudo mv /var/www/glpi/files /var/lib/glpi
```
```bash
sudo mkdir /var/log/glpi
```
```bash
sudo chown www-data /var/log/glpi
```
11) Install apache2 for the webpage
```bash
sudo nano /var/www/glpi/inc/downstream.php
```
```php
<?php
define('GLPI_CONFIG_DIR', '/etc/glpi/');
if (file_exists(GLPI_CONFIG_DIR . '/local_define.php')) {
    require_once GLPI_CONFIG_DIR . '/local_define.php';
}```
```bash
sudo nano /etc/glpi/local_define.php
```
```php
<?php
define('GLPI_VAR_DIR', '/var/lib/glpi/files');
define('GLPI_LOG_DIR', '/var/log/glpi');
```
```bash
sudo nano /etc/apache2/sites-available/librarykali.conf
```
```bash
sudo a2ensite support.it-connect.tech.conf
```
```bash
sudo a2enmod rewrite
```
```bash
sudo systemctl restart apache2
```
```bash
sudo apt-get install php8.1-fpm
```
```bash
sudo a2enmod proxy_fcgi setenvif
```
```bash
sudo a2enconf php8.2-fpm
```
```bash
sudo systemctl reload apache2
```
```bash
sudo nano /etc/php/8.1/fpm/php.ini
```
```bash
sudo systemctl restart php8.1-fpm.service
```
```bash
<FilesMatch \.php$>
    SetHandler "proxy:unix:/run/php/php8.2-fpm.sock|fcgi://localhost/"
</FilesMatch>
```
```bash
sudo systemctl restart apache2
```
