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