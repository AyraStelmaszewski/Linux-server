# Linux-server
Simulation of a linux server for a company with services as DNS, DHCP, HTTP+ and mariaDB.

## Project context
The local library in your little town has no funding for Windows licenses so the director is considering Linux. Some users are sceptical and ask for a demo. The local IT company where you work is taking up the project and you are in charge of setting up a server and a workstation. To demonstrate this setup, you will use virtual machines and an internal virtual network (your DHCP must not interfere with the LAN).

You may propose any additional functionality you consider interesting.

##  Must Have
Set up the following Linux infrastructure:

## One server (no GUI) running the following services:

DHCP (one scope serving the local internal network) isc-dhcp-server
DNS (resolve internal resources, a redirector is used for external resources) bind
HTTP+ mariadb (internal website running GLPI)
Required
Weekly backup the configuration files for each service into one single compressed archive
The server is remotely manageable (SSH)
Optional
Backups are placed on a partition located on separate disk, this partition must be mounted for the backup, then unmounted <br>
## One workstation running a desktop environment and the following apps:

LibreOffice
Gimp
Mullvad browser
Required
This workstation uses automatic addressing
The /home folder is located on a separate partition, same disk
Optional
Propose and implement a solution to remotely help a user
Performance criteria
The teams must give a sense of confidence to the audience when presenting their demo, the documentation must be clear, structured and must provide all elements of configuration.

## Evaluation methods
Quality of the documentation and the live demo
All required items are implemented are working
Optional items are implemented and working
The team demonstrates good organisation and planning
The team explains and justifies the various choices they made
Tests were performed to ensure the configuration is working
The team demonstrate a solid knowledge of the Linux environment when answering questions
