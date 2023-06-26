# Network Write-Up
 
GlobeX requires a network architecture which allows for the integration of existing physical networks(on-premises) with the GlobeX cloud base network. The integrated networks will need to be able to access the Corporate Windows server which is both the DNS server and domain controller. 

The Network architecture will be split into main parts 

## 1. AWS Resources (GlobeX HQ)

The table below list the network components which need to be up and running at the cloud provider in our case Amazon Web Services (AWS). 


| Network Components          | IP Adress      |
| --------------------------- | -------------- |
| Virtual Private Cloud (VPC) |                |
| Windows Server              | 54.183.193.15  |
| Internet Gateway            |                |
| Transit Gateway             |                |
| Site to Site VPN Connection |                |
| Customer Gateway            | 75.239.226.120 |

  
## Virtual Private Cloud (VPC)

Using a VPC to house the Windows server  allows the company to provision a logically isolated section of the cloud where you can launch resources such as virtual machines, databases, and other services in a network that the company needs. The VPC will also perform the following built-in functions for the GlobeX corprate network:

* DHCP Server
> Static IPv4 Lease 10.1.10.196 (Windows Server)
> Avalbale lease Pool 10.1.10.50 - 10.1.10.150

* DNS Server

  

The VPC is configured to have 2 subnets:
* Public Subnet  10.1.20.0/24
* Private Subnet 10.1.10.0/24

The two-subnet setup will allow for a higher level of network security. The private subnet will not have access to the internet which makes it harder for company information to leave the network. It is important to note subnets cannot be reused by any attached networks.

## Windows Server

Windows is the OS of choice for the main network server. This system will perform the following:
* Domain Controller
* Active Directory Host

## Internet Gateway

The internet gateway is attached to the VPC giving the completed network access to the border Internet. Forcing all network traffic to pass thruogh this gateway will mean network admins have control over the outside access network users have.

## Transit Gateway

A Transit Gateway is a network transit hub that you can use to interconnect your Virtual Private Clouds (VPCs) and on-premises networks. This one transit gateway will allow GlobeX to add as many additional networks as needed.

## Site to Site VPN Connection
  
This type of connection will allow for a on-prem network to be connected to the Transit Gateway via a single customer gateway and a router, a pfSense in out case).

## Customer Gateway

When you set up a Site-to-Site VPN connection between your on-premises network and AWS VPC, AWS needs to know how to communicate with your on-premises network. The Customer Gateway contains the configuration information needed for this communication, such as the public IP address of your VPN device (pfSense in our case). The customer gateway will use the same IP address as the pfSense because it is acting as a gate or a pass-through. 

2. On-Premises (CleanPower)

| Network Components          | IP Adress       |
| --------------------------- | --------------- |
| psSense(router)             | LAN 192.168.1.1 |
|                             | WAN 10.0.2.10   |
| End-user System             | Dynamic         |
  
*** pfSense

pfSense supports various VPN technologies, flexible routing, traffic shaping, and monitoring capabilities make it highly versatile for network management and security. In our setup the pfSense will be acting as the connection to AWS via the customer gateway and VPN as well as performing the following:

* DHCP - For the On-perm network
* DNS - For the On-perm network
* Captive Portal 

The network interfaces on the pfSense will be configured as follows:

* WAN  IP Address 10.0.2.10
> Subnet 10.0.2.0/24

* LAN IP Address 192.168.1.1
> Subnet 192.168.1.0/24

## End-user System 

End-users are able to use the OS which fits their jobs duties; the network config can support access from a number of different operating systems. Most users are expected to use Windows 10. IP addresses for these systems will be dynamically assigned from the available pool of 192.168.1.50 - 192.168.1.150
