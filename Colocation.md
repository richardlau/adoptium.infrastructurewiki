## Zurich, Switzerland
Provided by [Nine](https://www.nine.ch), hosted at [Colozüri](https://www.colozueri.ch/).

Overview:
* 20u usable (9u free)
* One 1 Gbps link with HA (automatic failover if port goes down)
* Redundant power bars
* 1 kW power budget

### Support, Remote Hands

People that are elegible to open support requests and request remote hands:

* [Andreas Ahlenstorf](https://github.com/aahlenst)
* [George Adams](https://github.com/gdams)
* [Martijn Verburg](https://github.com/karianna)
* [Shelley Lambert](https://github.com/smlambert)
* [Stewart Addison](https://github.com/sxa)
* [Will Parker](https://github.com/willsparker)

### Installed Equipment

Network, management:

* [Deciso DEC4630](https://www.deciso.com/product-catalog/DEC4630/) with OPNSense (purchased from Deciso 2020-11-06)
* [Deciso DEC4630](https://www.deciso.com/product-catalog/DEC4630/) with OPNSense (purchased from Deciso 2020-11-06)
* [Netgear M4300-48X](https://www.netgear.com/business/products/switches/managed/M4300-48X.aspx) (purchased from Brack on 2020-10-13)
* [Raritan Dominion KX III-108](https://www.raritan.com/products/kvm-serial/kvm-over-ip-switches/enterprise-ip-kvm-switch) (purchased from Brack on 2020-10-16)

Servers:
* Twin Mac Minis:
  * Mac Mini (2020) with 3.2 GHz 6 Core Intel i7, 64 GB RAM, 2 TB SSD, 10 GBase-T Ethernet with Apple Care+ (purchased from Apple on 2020-10-16)
  * mounted in [Sonnet RackMac mini 2018](https://www.sonnettech.com/product/rackmacmini.html) (purchased from Brack on 2020-10-16)
  * connected to KVM switch via HDMI, dual USB
* ER12A BitScope Edge Rack incl. 12 Raspberry Pis 4B, 8GB, 120 GB SSD (purchased from BitScope on 2020-10-16)
* ER12A BitScope Edge Rack incl. 12 Raspberry Pis 4B, 8GB, 120 GB SSD (purchased from BitScope on 2020-10-16)

### Remote Access

The firewalls are accessible via our bastion host ([use it as a SOCKS proxy](https://ma.ttias.be/socks-proxy-linux-ssh-bypass-content-filters/)) or internally via VPN. 

### Network Setup

The uplink is configured with active/passive high-availability over two perimeter networks (1 and 2, see below). 1 is the active, 2 the passive link. If one link goes down, traffic flows over the other link.

#### Perimeter Network 1

5.148.175.184/30  
Uplink IP: 5.148.175.185  
Our (gateway/firewall) IP: 5.148.175.186

2a02:418:39aa:2::/64  
Uplink IP: 2a02:418:39aa:2::1  
Our (gateway/firewall) IP: 2a02:418:39aa:2::2

#### Perimeter Network 2

5.148.175.188/30  
Uplink IP: 5.148.175.189  
Our (gateway/firewall) IP: 5.148.175.190

2a02:418:39aa:7::1/64  
Uplink IP: 2a02:418:39aa:2::1  
Our (gateway/firewall) IP: 2a02:418:39aa:7::2

#### Public IP Ranges

5.148.170.144/28  
2a02:418:3001::/48

#### Management Network

10.0.10.0/24 (VLAN 10)

```
x.1 CARP
x.2 Firewall 01
x.3 Firewall 02
x.4 Switch
x.5 KVM switch 01
```

### VPN

VPN is required to manage or access servers and other equipments. We use OpenVPN (SSL/TLS + User Auth with TOTP enabled) and have 3 different networks.

#### vpnstaff (port 1194)

Provides full access to all equipment except TCK infrastructure. For TCK infrastructure, only access to lights-out management is provided. Limited to AdoptOpenJDK infrastructure team.

#### vpnguest (port 1195)

Provides access to build and test networks (for debugging builds, tests, ...).

#### vpntck (port 1196)

Provides access to TCK infrastructure. **Limited to EMO staff and people that have signed a three-way OCTLA with Eclipse and Oracle**.

### Notes

VPN:

* OpenVPN was chosen because it's the most flexible and recommended option for OPNSense (Jan 2021).
* We have one OpenVPN instance per user group because (again) it's the most flexible option we found during the setup (Jan 2021). The approach outlined in the [official How To Guide](https://openvpn.net/community-resources/how-to/#configuring-client-specific-rules-and-access-policies) works with client-specific rules. Those rules are specific for a single client, require a separate network per user, and therefore do not scale with a larger number of users.