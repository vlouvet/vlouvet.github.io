Recently, I noticed that my WiFi setup was not great - some devices were trying to do too much!

For example, the modem provided by my Internet Service Provider (ISP) was bridging, routing packets, Serving DHCP and DNS requests and propagating a WiFi signal. This is convenient for most people that want a product that "just works" out of the box - but it can overwork the hardware.


To address this issue I decided to split out the network infrastructure for redundancy and better load balancing of services. 


Some time ago, I started buying hardware in pairs. Once I decide on a hardware platform for a specific purpose - I buy two units and keep one unit in storage. I also purchase used or refurbished whenever possible to offset the price increase when buying two units.


This is a list of requirements that must be met in order for the infrastructure to be "wife approved":
    
1. Less than 1% internet downtime due to maintenance or mis-configuration.
2. Seamless roaming between access points anywhere on 1/3rd acre property.
3. Ability to disconnect clients manually, or on a schedule.
4. No subscription or cloud-based services required.
5. provide reliable 802.11AC speeds to up to 20 clients
6. Adblocking for all LAN clients
7. Network hardware devices powered by UPS to prevent dropouts when power goes out.

This is a list of features that were desired, but not required:

1. Collect, graph and analyze Lan, Wifi, and WAN traffic.
2. Fast (under 5 minutes) hardware device replacements.
3. Hardware devices follow the unix philosophy "Do one thing, do it well".
4. Stream three 4K 10mBit videos from Jellyfin concurrently.
5. Access Points are powered via PoE to reduce wiring during installs.


Here is a breakdown of the network hardware used:

1. Router/Firewall - 
    - HP ProDesk 400 G3
    - Hardware Specs: Intel i3-6100, 4GB ddr4, IBM 00E0838 Quad Port 1Gb Network Adapter Card
    - using SFF instead of MicroPC for easy installation of Network Adapter card
2. Ethernet Switching - 
    - Gigabit Switch: 16 Port Gigabit NETGEAR ProSafe GS116 v2
    - PoE switch: 5 Port PoE Gigabit NETGEAR Ethernet Plus Switch GS305EP
3. Wifi Equipment - 
    - Access Points: TL-WA1201 - two units installed in opposite ends of property
4. Uninterruptible Power Supply (UPS) - 
    - APC Battery Backup BX850M
    - Future APC models will drop support for power management tools through USB for a proprietary version, YMMV [(Link)](https://www.reddit.com/r/sysadmin/comments/14g50fe/apcschneider_electric_soon_to_charge_for_nmc3/).

Here is a breakdown of the network software used:

1. Firewall OS - OPNSense 24.x [(Link)](https://opnsense.org/)
2. Wifi AP OS - OpenWrt 24.x [(Link)](https://openwrt.org/)