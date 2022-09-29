# Ansible role: wifi_ap

[![MIT licensed][mit-badge]][mit-link]
[![Galaxy Role][role-badge]][galaxy-link]

Ansible role for setting up RaspberryPi as wireless Access Point.

Role accomplishes the following:

 - installs and configures `isc-dchp-server`
 - installs `iptables-persistent` and configures `iptables` to use NAT
 - installs and configures `hostapd`

It is assumed that the raspberry pi 3(with built in broadcom wifi adapter) is used with additional Ralink usb wifi dongle.

The one I use it with is Ralink USB WiFi RT5370:

```
 - 802.11 b/g/n 150Mbps;
 - 2.400-2.487 GHz channels 1-14;
 - Power: Voltage: 5V+5%, Amperage: 70mA Average)
```

NOTE: The role uses ansible `replace` module wich is currently broken:
the module's `before/after` properties work other way around.
The [issue][ansible-replace-issue-link] is still open, but may change in the future.

Requirements
----

**NOTE:** Role requires Fact Gathering by ansible!

One of the following distros (or derivatives) required:
 - Debian | Raspberry Pi OS | Raspbian | [Minibian][minibian-link]
    - jessie
    - stretch
    - bullseye

Role Variables
----

| Variable | Description | Default |
|----------|-------------|---------|
| `wifi_ap_WAN` | WAN (external) interface  | `eth0` |
| `wifi_ap_WLAN` | WLAN (internal) wireless interface  | `wlan0` |
| `wifi_ap_WLAN_subnet` | Network IP address for WLAN interface  | `192.168.42.0` |
| `wifi_ap_WLAN_netmask` | Subnet mask for WLAN interface | `255.255.255.0` |
| `wifi_ap_WLAN_broadcast` | Broadcast IP address for WLAN interface | `192.168.42.255` |
| `wifi_ap_WLAN_ip` | IP address of WLAN interface  | `192.168.42.1` |
| `wifi_ap_dhcp_range` | DHCP range of IP addresses | `[192.168.42.100, 192.168.42.199]` |
| `wifi_ap_domain` | Search domain to be adertised to all clients by DHCP server | `domain.lan` |
| `wifi_ap_domain` | List of DNS resolvers to be adertised to all clients by DHCP server | `[9.9.9.9, 9.9.9.10, 149.112.122.122]` |
| `wifi_ap_essid` | ESSID (name) of the wifi network | `MyAP` |
| `wifi_ap_passphrase` | WPA Passphrase for wifi network | `P@$$w0rd` |
| `wifi_ap_flush_iptables` | no - for fresh install; yes - removes idempotency (always yellow) | `no` |
| `wifi_ap__rpi_network_wifi_APs` | this overrides the rpi_network_wifi_APs var of rpi_network dependency role | see [`defaults/main.yml`](defaults/main.yml#L27) |

**ATTENTION!**

make sure you override the **wifi_ap__rpi_network_wifi_APs** var as it contains a sensitive data for your wireless networks,
such as WPA passphrase and network ESSID...

It is highly recommended to encrypt with [ansible-vault][ansible-vault-link].

Before running any playbook which uses this role, decrypt the file *vars/main.yml* with:

```
ansible-vault decrypt vars/main.yml --vault-password-file=.vault.key
```

OR set environment variable:

```
export ANSIBLE_VAULT_PASSWORD_FILE=.vault.key
```

OR (PREFERRED):
add the following to **ansible.cfg**:

```
[defaults]
vault_password_file = .vault.key
```

Dependencies
----

 - [drew1kun.rpi_network][rpi_network-galaxy-link]

Install it with galaxy:

```
ansible-galaxy install drew1kun.rpi_network
```

Example Playbook
----

As the role works with sensitive info like essid and wpa passphrase, the use of [ansible-vault][ansible-vault-link] is recommended for use in
playbook:

```yaml
- hosts: raspberrypi
  gather_facts: yes

  vars_files:
  - vars/vault.yml

  roles:
  - role: drew1kun.wifi_ap
    wifi_ap_essid: "{{ vault_wifi_ap_essid }}"
    wifi_ap_passphrase: "{{ vault_wifi_ap_passphrase }}"
    wifi_ap_WAN: wlan0
    wifi_ap_WLAN: wlan1

    wif_ap__rpi_network_wifi_APs:
    - id_str: home
      hidden: no
      essid: "{{ vault_wifi_ap__rpi_network_wifi_APs[0].essid }}"
      passphrase: "{{ vault_wifi_ap__rpi_network_wifi_APs[0].passphrase }}"
      priority: 10

```
*vars/vault.yml*:

```yaml
vault_wifi_ap__rpi_network_wifi_APs:
# only sensitive stuff goes here:
- essid: YourSensitiveESSID
  passphrase: YourSecureWPA_Passphrase
```

License
----

[MIT][mit-link]

Author Information
----

Andrew Shagayev | [e-mail](mailto:drewshg@gmail.com)

[role-badge]: https://img.shields.io/badge/role-drew1kun.wifi__ap-green.svg
[galaxy-link]: https://galaxy.ansible.com/drew1kun/wifi_ap/
[mit-badge]: https://img.shields.io/badge/license-MIT-blue.svg
[mit-link]: https://raw.githubusercontent.com/drew1kun/ansible-wifi_ap/master/LICENSE
[minibian-link]: https://minibianpi.wordpress.com/
[ansible-vault-link]: https://docs.ansible.com/ansible/latest/user_guide/vault.html
[rpi_network-galaxy-link]: https://galaxy.ansible.com/drew1kun/rpi_network/
[ansible-replace-issue-link]: https://github.com/ansible/ansible/issues/31354
