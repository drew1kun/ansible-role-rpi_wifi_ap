Ansible role: minibian_core
=========

[![MIT licensed][mit-badge]][mit-link]
[![Galaxy Role][role-badge]][galaxy-link]

Ansible role for setting up RaspberryPi as wireless Access Point.

Role accomplishes the following:

 - installs and configures `isc-dchp-server`
 - installs `iptables-persistent` and configures `iptables` to use NAT
 - installs and configures `hostapd`

Requirements
------------

NOTE: Role requires Fact Gathering by ansible!

One of the following distros (or derivatives) required:
 - [Minibian][minibian-link] | Raspbian | Debian

Role Variables
--------------

| Variable | Description | Default |
|----------|-------------|---------|
| **minibian_ap_WAN** | WAN (external) interface  | eth0 |
| **minibian_ap_WLAN** | WLAN (internal) wireless interface  | wlan0 |
| **minibian_ap_WLAN_subnet** | Network IP address for WLAN interface  | 192.168.42.0 |
| **minibian_ap_WLAN_netmask** | Subnet mask for WLAN interface | 255.255.255.0 |
| **minibian_ap_WLAN_broadcast** | Broadcast IP address for WLAN interface | 192.168.42.255 |
| **minibian_ap_WLAN_ip** | IP address of WLAN interface  | 192.168.42.1 |
| **minibian_ap_dhcp_range** | DHCP range of IP addresses | [192.168.42.100, 192.168.42.199] |
| **minibian_ap_essid** | ESSID (name) of the wifi network | AnonAP |
| **minibian_ap_wpa_psk** | WPA Passphrase for wifi network | P@ssw0rd |
| **minibian_ap_flush_iptables** | no - for fresh install; yes - removes idempotency (always yellow) | no |

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: raspberrypi
      gather_facts: yes
      roles: drew-kun.minibian_ap

License
-------

[MIT][mit-link]

Author Information
------------------

Andrew Shagayev | [e-mail](mailto:drewshg@gmail.com)

[role-badge]: https://img.shields.io/badge/role-drew--kun.minibian__ap-green.svg
[galaxy-link]: https://galaxy.ansible.com/drew-kun/minibian_ap/
[mit-badge]: https://img.shields.io/badge/license-MIT-blue.svg
[mit-link]: https://raw.githubusercontent.com/drew-kun/ansible-minibian_ap/master/LICENSE
[minibian-link]: https://minibianpi.wordpress.com/
