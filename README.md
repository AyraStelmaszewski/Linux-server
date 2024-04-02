# Linux-server
Simulation of a linux server for a company with services as DNS, DHCP, HTTP+ and mariaDB.

## Project context
The local library in your little town has no funding for Windows licenses so the director is considering Linux. Some users are sceptical and ask for a demo.  <br> The local IT company where you work is taking up the project and you are in charge of setting up a server and a workstation.  <br> To demonstrate this setup, you will use virtual machines and an internal virtual network (your DHCP must not interfere with the LAN). <br>

You may propose any additional functionality you consider interesting. <br>

##  Must Have
Set up the following Linux infrastructure: <br>

## One server (no GUI) running the following services:

DHCP (one scope serving the local internal network) isc-dhcp-server <br>
DNS (resolve internal resources, a redirector is used for external resources) bind <br>
HTTP+ mariadb (internal website running GLPI) <br>

## Required
Weekly backup the configuration files for each service into one single compressed archive <br>
The server is remotely manageable (SSH) <br>
 ## Optional
Backups are placed on a partition located on separate disk, this partition must be mounted for the backup, then unmounted <br>
## One workstation running a desktop environment and the following apps:

LibreOffice <br>
Gimp <br>
Mullvad browser <br>
Required <br>
This workstation uses automatic addressing <br>
The /home folder is located on a separate partition, same disk <br>
# Optional <br>
Propose and implement a solution to remotely help a user <br>
Performance criteria <br>
The teams must give a sense of confidence to the audience when presenting their demo, the documentation must be clear, structured and must provide all elements of configuration.  <br>

## Evaluation methods
Quality of the documentation and the live demo <br>
All required items are implemented are working <br>
Optional items are implemented and working <br>
The team demonstrates good organisation and planning <br>
The team explains and justifies the various choices they made <br>
Tests were performed to ensure the configuration is working <br>
The team demonstrate a solid knowledge of the Linux environment when answering questions <br>
