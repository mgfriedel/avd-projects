# dc2-leaf2a

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
- [MLAG](#mlag)
  - [MLAG Summary](#mlag-summary)
  - [MLAG Device Configuration](#mlag-device-configuration)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Configuration](#internal-vlan-allocation-policy-configuration)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Port-Channel Interfaces](#port-channel-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [Virtual Router MAC Address](#virtual-router-mac-address)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [Virtual Source NAT](#virtual-source-nat)
  - [Virtual Source NAT Summary](#virtual-source-nat-summary)
  - [Virtual Source NAT Configuration](#virtual-source-nat-configuration)
- [EOS CLI](#eos-cli)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.16.1.113/24 | 172.16.1.1 |

##### IPv6

| Management Interface | description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.16.1.113/24
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| MGMT | - | - |

#### Management API HTTP Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf MGMT
      no shutdown
```

## Authentication

### Local Users

#### Local Users Summary

| User | Privilege | Role | Disabled | Shell |
| ---- | --------- | ---- | -------- | ----- |
| admin | 15 | network-admin | False | - |
| ansible | 15 | network-admin | False | - |

#### Local Users Device Configuration

```eos
!
username admin privilege 15 role network-admin nopassword
username ansible privilege 15 role network-admin secret sha512 <removed>
```

## MLAG

### MLAG Summary

| Domain-id | Local-interface | Peer-address | Peer-link |
| --------- | --------------- | ------------ | --------- |
| DC2_L3_LEAF2 | Vlan4094 | 10.255.129.89 | Port-Channel3 |

Dual primary detection is disabled.

### MLAG Device Configuration

```eos
!
mlag configuration
   domain-id DC2_L3_LEAF2
   local-interface Vlan4094
   peer-address 10.255.129.89
   peer-link Port-Channel3
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **mstp**

#### MSTP Instance and Priority

| Instance(s) | Priority |
| -------- | -------- |
| 0 | 4096 |

#### Global Spanning-Tree Settings

- Spanning Tree disabled for VLANs: **4093-4094**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
no spanning-tree vlan-id 4093-4094
spanning-tree mst 0 priority 4096
```

## Internal VLAN Allocation Policy

### Internal VLAN Allocation Policy Summary

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 1199 |

### Internal VLAN Allocation Policy Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 101 | Prod1-101 | - |
| 102 | Prod1-102 | - |
| 111 | Prod2-111 | - |
| 112 | Prod2-112 | - |
| 201 | HA1-201 | - |
| 202 | HA1-202 | - |
| 211 | HA2-211 | - |
| 212 | HA2-212 | - |
| 3009 | MLAG_iBGP_Prod1 | LEAF_PEER_L3 |
| 3010 | MLAG_iBGP_Prod2 | LEAF_PEER_L3 |
| 3019 | MLAG_iBGP_HA1 | LEAF_PEER_L3 |
| 3020 | MLAG_iBGP_HA2 | LEAF_PEER_L3 |
| 3401 | L2_VLAN3401 | - |
| 3402 | L2_VLAN3402 | - |
| 4093 | LEAF_PEER_L3 | LEAF_PEER_L3 |
| 4094 | MLAG_PEER | MLAG |

### VLANs Device Configuration

```eos
!
vlan 101
   name Prod1-101
!
vlan 102
   name Prod1-102
!
vlan 111
   name Prod2-111
!
vlan 112
   name Prod2-112
!
vlan 201
   name HA1-201
!
vlan 202
   name HA1-202
!
vlan 211
   name HA2-211
!
vlan 212
   name HA2-212
!
vlan 3009
   name MLAG_iBGP_Prod1
   trunk group LEAF_PEER_L3
!
vlan 3010
   name MLAG_iBGP_Prod2
   trunk group LEAF_PEER_L3
!
vlan 3019
   name MLAG_iBGP_HA1
   trunk group LEAF_PEER_L3
!
vlan 3020
   name MLAG_iBGP_HA2
   trunk group LEAF_PEER_L3
!
vlan 3401
   name L2_VLAN3401
!
vlan 3402
   name L2_VLAN3402
!
vlan 4093
   name LEAF_PEER_L3
   trunk group LEAF_PEER_L3
!
vlan 4094
   name MLAG_PEER
   trunk group MLAG
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet3 | MLAG_PEER_dc2-leaf2b_Ethernet3 | *trunk | *- | *- | *['LEAF_PEER_L3', 'MLAG'] | 3 |
| Ethernet4 | MLAG_PEER_dc2-leaf2b_Ethernet4 | *trunk | *- | *- | *['LEAF_PEER_L3', 'MLAG'] | 3 |
| Ethernet5 | dc2-leaf2-server1_PCI1 | *trunk | *11-12,21-22 | *4092 | *- | 5 |
| Ethernet8 | DC2-LEAF2-L2SW_Ethernet1 | *trunk | *101-102,111-112,201-202,211-212,3401-3402 | *- | *- | 8 |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_LINK_TO_DC2-SPINE1_Ethernet3 | routed | - | 10.255.255.113/31 | default | 1500 | False | - | - |
| Ethernet2 | P2P_LINK_TO_DC2-SPINE2_Ethernet3 | routed | - | 10.255.255.115/31 | default | 1500 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_LINK_TO_DC2-SPINE1_Ethernet3
   no shutdown
   mtu 1500
   no switchport
   ip address 10.255.255.113/31
!
interface Ethernet2
   description P2P_LINK_TO_DC2-SPINE2_Ethernet3
   no shutdown
   mtu 1500
   no switchport
   ip address 10.255.255.115/31
!
interface Ethernet3
   description MLAG_PEER_dc2-leaf2b_Ethernet3
   no shutdown
   channel-group 3 mode active
!
interface Ethernet4
   description MLAG_PEER_dc2-leaf2b_Ethernet4
   no shutdown
   channel-group 3 mode active
!
interface Ethernet5
   description dc2-leaf2-server1_PCI1
   no shutdown
   channel-group 5 mode active
!
interface Ethernet8
   description DC2-LEAF2-L2SW_Ethernet1
   no shutdown
   channel-group 8 mode active
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Type | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel3 | MLAG_PEER_dc2-leaf2b_Po3 | switched | trunk | - | - | ['LEAF_PEER_L3', 'MLAG'] | - | - | - | - |
| Port-Channel5 | dc2-leaf2-server1_PortChannel dc2-leaf2-server1 | switched | trunk | 11-12,21-22 | 4092 | - | - | - | 5 | - |
| Port-Channel8 | DC2-LEAF2-L2SW_Po1 | switched | trunk | 101-102,111-112,201-202,211-212,3401-3402 | - | - | - | - | 8 | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel3
   description MLAG_PEER_dc2-leaf2b_Po3
   no shutdown
   switchport
   switchport mode trunk
   switchport trunk group LEAF_PEER_L3
   switchport trunk group MLAG
!
interface Port-Channel5
   description dc2-leaf2-server1_PortChannel dc2-leaf2-server1
   no shutdown
   switchport
   switchport trunk allowed vlan 11-12,21-22
   switchport trunk native vlan 4092
   switchport mode trunk
   mlag 5
   spanning-tree portfast
!
interface Port-Channel8
   description DC2-LEAF2-L2SW_Po1
   no shutdown
   switchport
   switchport trunk allowed vlan 101-102,111-112,201-202,211-212,3401-3402
   switchport mode trunk
   mlag 8
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | EVPN_Overlay_Peering | default | 10.255.128.15/32 |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | 10.255.129.15/32 |
| Loopback10 | Prod1_VTEP_DIAGNOSTICS | Prod1 | 10.255.12.15/32 |
| Loopback11 | Prod2_VTEP_DIAGNOSTICS | Prod2 | 10.255.13.15/32 |
| Loopback20 | HA1_VTEP_DIAGNOSTICS | HA1 | 10.255.14.15/32 |
| Loopback21 | HA2_VTEP_DIAGNOSTICS | HA2 | 10.255.15.15/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | EVPN_Overlay_Peering | default | - |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | - |
| Loopback10 | Prod1_VTEP_DIAGNOSTICS | Prod1 | - |
| Loopback11 | Prod2_VTEP_DIAGNOSTICS | Prod2 | - |
| Loopback20 | HA1_VTEP_DIAGNOSTICS | HA1 | - |
| Loopback21 | HA2_VTEP_DIAGNOSTICS | HA2 | - |


#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description EVPN_Overlay_Peering
   no shutdown
   ip address 10.255.128.15/32
!
interface Loopback1
   description VTEP_VXLAN_Tunnel_Source
   no shutdown
   ip address 10.255.129.15/32
!
interface Loopback10
   description Prod1_VTEP_DIAGNOSTICS
   no shutdown
   vrf Prod1
   ip address 10.255.12.15/32
!
interface Loopback11
   description Prod2_VTEP_DIAGNOSTICS
   no shutdown
   vrf Prod2
   ip address 10.255.13.15/32
!
interface Loopback20
   description HA1_VTEP_DIAGNOSTICS
   no shutdown
   vrf HA1
   ip address 10.255.14.15/32
!
interface Loopback21
   description HA2_VTEP_DIAGNOSTICS
   no shutdown
   vrf HA2
   ip address 10.255.15.15/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan101 | Prod1-101 | Prod1 | - | False |
| Vlan102 | Prod1-102 | Prod1 | - | False |
| Vlan111 | Prod2-111 | Prod2 | - | False |
| Vlan112 | Prod2-112 | Prod2 | - | False |
| Vlan201 | HA1-201 | HA1 | - | False |
| Vlan202 | HA1-202 | HA1 | - | False |
| Vlan211 | HA2-211 | HA2 | - | False |
| Vlan212 | HA2-212 | HA2 | - | False |
| Vlan3009 | MLAG_PEER_L3_iBGP: vrf Prod1 | Prod1 | 1500 | False |
| Vlan3010 | MLAG_PEER_L3_iBGP: vrf Prod2 | Prod2 | 1500 | False |
| Vlan3019 | MLAG_PEER_L3_iBGP: vrf HA1 | HA1 | 1500 | False |
| Vlan3020 | MLAG_PEER_L3_iBGP: vrf HA2 | HA2 | 1500 | False |
| Vlan4093 | MLAG_PEER_L3_PEERING | default | 1500 | False |
| Vlan4094 | MLAG_PEER | default | 1500 | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan101 |  Prod1  |  -  |  10.0.101.1/24  |  -  |  -  |  -  |  -  |
| Vlan102 |  Prod1  |  -  |  10.0.102.1/24  |  -  |  -  |  -  |  -  |
| Vlan111 |  Prod2  |  -  |  10.0.111.1/24  |  -  |  -  |  -  |  -  |
| Vlan112 |  Prod2  |  -  |  10.0.112.1/24  |  -  |  -  |  -  |  -  |
| Vlan201 |  HA1  |  -  |  10.0.101.1/24  |  -  |  -  |  -  |  -  |
| Vlan202 |  HA1  |  -  |  10.0.102.1/24  |  -  |  -  |  -  |  -  |
| Vlan211 |  HA2  |  -  |  10.0.111.1/24  |  -  |  -  |  -  |  -  |
| Vlan212 |  HA2  |  -  |  10.0.112.1/24  |  -  |  -  |  -  |  -  |
| Vlan3009 |  Prod1  |  10.255.129.120/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan3010 |  Prod2  |  10.255.129.120/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan3019 |  HA1  |  10.255.129.120/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan3020 |  HA2  |  10.255.129.120/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan4093 |  default  |  10.255.129.120/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan4094 |  default  |  10.255.129.88/31  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan101
   description Prod1-101
   no shutdown
   vrf Prod1
   ip address virtual 10.0.101.1/24
!
interface Vlan102
   description Prod1-102
   no shutdown
   vrf Prod1
   ip address virtual 10.0.102.1/24
!
interface Vlan111
   description Prod2-111
   no shutdown
   vrf Prod2
   ip address virtual 10.0.111.1/24
!
interface Vlan112
   description Prod2-112
   no shutdown
   vrf Prod2
   ip address virtual 10.0.112.1/24
!
interface Vlan201
   description HA1-201
   no shutdown
   vrf HA1
   ip address virtual 10.0.101.1/24
!
interface Vlan202
   description HA1-202
   no shutdown
   vrf HA1
   ip address virtual 10.0.102.1/24
!
interface Vlan211
   description HA2-211
   no shutdown
   vrf HA2
   ip address virtual 10.0.111.1/24
!
interface Vlan212
   description HA2-212
   no shutdown
   vrf HA2
   ip address virtual 10.0.112.1/24
!
interface Vlan3009
   description MLAG_PEER_L3_iBGP: vrf Prod1
   no shutdown
   mtu 1500
   vrf Prod1
   ip address 10.255.129.120/31
!
interface Vlan3010
   description MLAG_PEER_L3_iBGP: vrf Prod2
   no shutdown
   mtu 1500
   vrf Prod2
   ip address 10.255.129.120/31
!
interface Vlan3019
   description MLAG_PEER_L3_iBGP: vrf HA1
   no shutdown
   mtu 1500
   vrf HA1
   ip address 10.255.129.120/31
!
interface Vlan3020
   description MLAG_PEER_L3_iBGP: vrf HA2
   no shutdown
   mtu 1500
   vrf HA2
   ip address 10.255.129.120/31
!
interface Vlan4093
   description MLAG_PEER_L3_PEERING
   no shutdown
   mtu 1500
   ip address 10.255.129.120/31
!
interface Vlan4094
   description MLAG_PEER
   no shutdown
   mtu 1500
   no autostate
   ip address 10.255.129.88/31
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Loopback1 |
| UDP port | 4789 |
| EVPN MLAG Shared Router MAC | mlag-system-id |

##### VLAN to VNI, Flood List and Multicast Group Mappings

| VLAN | VNI | Flood List | Multicast Group |
| ---- | --- | ---------- | --------------- |
| 101 | 10101 | - | - |
| 102 | 10102 | - | - |
| 111 | 10111 | - | - |
| 112 | 10112 | - | - |
| 201 | 20201 | - | - |
| 202 | 20202 | - | - |
| 211 | 20211 | - | - |
| 212 | 20212 | - | - |
| 3401 | 23401 | - | - |
| 3402 | 23402 | - | - |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| HA1 | 20 | - |
| HA2 | 21 | - |
| Prod1 | 10 | - |
| Prod2 | 11 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description dc2-leaf2a_VTEP
   vxlan source-interface Loopback1
   vxlan virtual-router encapsulation mac-address mlag-system-id
   vxlan udp-port 4789
   vxlan vlan 101 vni 10101
   vxlan vlan 102 vni 10102
   vxlan vlan 111 vni 10111
   vxlan vlan 112 vni 10112
   vxlan vlan 201 vni 20201
   vxlan vlan 202 vni 20202
   vxlan vlan 211 vni 20211
   vxlan vlan 212 vni 20212
   vxlan vlan 3401 vni 23401
   vxlan vlan 3402 vni 23402
   vxlan vrf HA1 vni 20
   vxlan vrf HA2 vni 21
   vxlan vrf Prod1 vni 10
   vxlan vrf Prod2 vni 11
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### Virtual Router MAC Address

#### Virtual Router MAC Address Summary

##### Virtual Router MAC Address: 00:1c:73:00:00:99

#### Virtual Router MAC Address Configuration

```eos
!
ip virtual-router mac-address 00:1c:73:00:00:99
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| HA1 | True |
| HA2 | True |
| MGMT | False |
| Prod1 | True |
| Prod2 | True |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf HA1
ip routing vrf HA2
no ip routing vrf MGMT
ip routing vrf Prod1
ip routing vrf Prod2
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| HA1 | false |
| HA2 | false |
| MGMT | false |
| Prod1 | false |
| Prod2 | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP             | Exit interface      | Administrative Distance       | Tag               | Route Name                    | Metric         |
| --- | ------------------ | ----------------------- | ------------------- | ----------------------------- | ----------------- | ----------------------------- | -------------- |
| MGMT | 0.0.0.0/0 | 172.16.1.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.16.1.1
```

### Router BGP

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65202|  10.255.128.15 |

| BGP Tuning |
| ---------- |
| update wait-install |
| no bgp default ipv4-unicast |
| maximum-paths 4 ecmp 4 |

#### Router BGP Peer Groups

##### EVPN-OVERLAY-CORE

| Settings | Value |
| -------- | ----- |
| Address Family | evpn |
| Source | Loopback0 |
| BFD | True |
| Ebgp multihop | 15 |
| Send community | all |
| Maximum routes | 0 (no limit) |

##### EVPN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | evpn |
| Source | Loopback0 |
| BFD | True |
| Ebgp multihop | 3 |
| Send community | all |
| Maximum routes | 0 (no limit) |

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Send community | all |
| Maximum routes | 12000 |

##### MLAG-IPv4-UNDERLAY-PEER

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Remote AS | 65202 |
| Next-hop self | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- |
| 10.255.0.5 | 65102 | default | - | Inherited from peer group EVPN-OVERLAY-CORE | Inherited from peer group EVPN-OVERLAY-CORE | - | Inherited from peer group EVPN-OVERLAY-CORE | - | - | - |
| 10.255.128.11 | 65200 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - |
| 10.255.128.12 | 65200 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - |
| 10.255.129.121 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | default | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - |
| 10.255.255.112 | 65200 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - |
| 10.255.255.114 | 65200 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - |
| 10.255.129.121 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | HA1 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - |
| 10.255.129.121 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | HA2 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - |
| 10.255.129.121 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Prod1 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - |
| 10.255.129.121 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Prod2 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| EVPN-OVERLAY-CORE | True | default |
| EVPN-OVERLAY-PEERS | True | default |

##### EVPN DCI Gateway Summary

| Settings | Value |
| -------- | ----- |
| Remote Domain Peer Groups | EVPN-OVERLAY-CORE |
| L3 Gateway Configured | True |
| L3 Gateway Inter-domain | True |

#### Router BGP VLANs

| VLAN | Route-Distinguisher | Both Route-Target | Import Route Target | Export Route-Target | Redistribute |
| ---- | ------------------- | ----------------- | ------------------- | ------------------- | ------------ |
| 101 | 10.255.128.15:10101 | 10101:10101<br>remote 10101:10101 | - | - | learned |
| 102 | 10.255.128.15:10102 | 10102:10102<br>remote 10102:10102 | - | - | learned |
| 111 | 10.255.128.15:10111 | 10111:10111<br>remote 10111:10111 | - | - | learned |
| 112 | 10.255.128.15:10112 | 10112:10112<br>remote 10112:10112 | - | - | learned |
| 201 | 10.255.128.15:20201 | 20201:20201<br>remote 20201:20201 | - | - | learned |
| 202 | 10.255.128.15:20202 | 20202:20202<br>remote 20202:20202 | - | - | learned |
| 211 | 10.255.128.15:20211 | 20211:20211<br>remote 20211:20211 | - | - | learned |
| 212 | 10.255.128.15:20212 | 20212:20212<br>remote 20212:20212 | - | - | learned |
| 3401 | 10.255.128.15:23401 | 23401:23401<br>remote 23401:23401 | - | - | learned |
| 3402 | 10.255.128.15:23402 | 23402:23402<br>remote 23402:23402 | - | - | learned |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| HA1 | 10.255.128.15:20 | connected |
| HA2 | 10.255.128.15:21 | connected |
| Prod1 | 10.255.128.15:10 | connected |
| Prod2 | 10.255.128.15:11 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65202
   router-id 10.255.128.15
   maximum-paths 4 ecmp 4
   update wait-install
   no bgp default ipv4-unicast
   neighbor EVPN-OVERLAY-CORE peer group
   neighbor EVPN-OVERLAY-CORE update-source Loopback0
   neighbor EVPN-OVERLAY-CORE bfd
   neighbor EVPN-OVERLAY-CORE ebgp-multihop 15
   neighbor EVPN-OVERLAY-CORE send-community
   neighbor EVPN-OVERLAY-CORE maximum-routes 0
   neighbor EVPN-OVERLAY-PEERS peer group
   neighbor EVPN-OVERLAY-PEERS update-source Loopback0
   neighbor EVPN-OVERLAY-PEERS bfd
   neighbor EVPN-OVERLAY-PEERS ebgp-multihop 3
   neighbor EVPN-OVERLAY-PEERS password 7 <removed>
   neighbor EVPN-OVERLAY-PEERS send-community
   neighbor EVPN-OVERLAY-PEERS maximum-routes 0
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS password 7 <removed>
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER peer group
   neighbor MLAG-IPv4-UNDERLAY-PEER remote-as 65202
   neighbor MLAG-IPv4-UNDERLAY-PEER next-hop-self
   neighbor MLAG-IPv4-UNDERLAY-PEER description dc2-leaf2b
   neighbor MLAG-IPv4-UNDERLAY-PEER password 7 <removed>
   neighbor MLAG-IPv4-UNDERLAY-PEER send-community
   neighbor MLAG-IPv4-UNDERLAY-PEER maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER route-map RM-MLAG-PEER-IN in
   neighbor 10.255.0.5 peer group EVPN-OVERLAY-CORE
   neighbor 10.255.0.5 remote-as 65102
   neighbor 10.255.0.5 description dc1-leaf2a
   neighbor 10.255.128.11 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.128.11 remote-as 65200
   neighbor 10.255.128.11 description dc2-spine1
   neighbor 10.255.128.12 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.128.12 remote-as 65200
   neighbor 10.255.128.12 description dc2-spine2
   neighbor 10.255.129.121 peer group MLAG-IPv4-UNDERLAY-PEER
   neighbor 10.255.129.121 description dc2-leaf2b
   neighbor 10.255.255.112 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.112 remote-as 65200
   neighbor 10.255.255.112 description dc2-spine1_Ethernet3
   neighbor 10.255.255.114 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.114 remote-as 65200
   neighbor 10.255.255.114 description dc2-spine2_Ethernet3
   redistribute connected route-map RM-CONN-2-BGP
   !
   vlan 101
      rd 10.255.128.15:10101
      rd evpn domain remote 10.255.128.15:10101
      route-target both 10101:10101
      route-target import export evpn domain remote 10101:10101
      redistribute learned
   !
   vlan 102
      rd 10.255.128.15:10102
      rd evpn domain remote 10.255.128.15:10102
      route-target both 10102:10102
      route-target import export evpn domain remote 10102:10102
      redistribute learned
   !
   vlan 111
      rd 10.255.128.15:10111
      rd evpn domain remote 10.255.128.15:10111
      route-target both 10111:10111
      route-target import export evpn domain remote 10111:10111
      redistribute learned
   !
   vlan 112
      rd 10.255.128.15:10112
      rd evpn domain remote 10.255.128.15:10112
      route-target both 10112:10112
      route-target import export evpn domain remote 10112:10112
      redistribute learned
   !
   vlan 201
      rd 10.255.128.15:20201
      rd evpn domain remote 10.255.128.15:20201
      route-target both 20201:20201
      route-target import export evpn domain remote 20201:20201
      redistribute learned
   !
   vlan 202
      rd 10.255.128.15:20202
      rd evpn domain remote 10.255.128.15:20202
      route-target both 20202:20202
      route-target import export evpn domain remote 20202:20202
      redistribute learned
   !
   vlan 211
      rd 10.255.128.15:20211
      rd evpn domain remote 10.255.128.15:20211
      route-target both 20211:20211
      route-target import export evpn domain remote 20211:20211
      redistribute learned
   !
   vlan 212
      rd 10.255.128.15:20212
      rd evpn domain remote 10.255.128.15:20212
      route-target both 20212:20212
      route-target import export evpn domain remote 20212:20212
      redistribute learned
   !
   vlan 3401
      rd 10.255.128.15:23401
      rd evpn domain remote 10.255.128.15:23401
      route-target both 23401:23401
      route-target import export evpn domain remote 23401:23401
      redistribute learned
   !
   vlan 3402
      rd 10.255.128.15:23402
      rd evpn domain remote 10.255.128.15:23402
      route-target both 23402:23402
      route-target import export evpn domain remote 23402:23402
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY-CORE activate
      neighbor EVPN-OVERLAY-CORE domain remote
      neighbor EVPN-OVERLAY-PEERS activate
      neighbor default next-hop-self received-evpn-routes route-type ip-prefix inter-domain
   !
   address-family ipv4
      no neighbor EVPN-OVERLAY-CORE activate
      no neighbor EVPN-OVERLAY-PEERS activate
      neighbor IPv4-UNDERLAY-PEERS activate
      neighbor MLAG-IPv4-UNDERLAY-PEER activate
   !
   vrf HA1
      rd 10.255.128.15:20
      route-target import evpn 20:20
      route-target export evpn 20:20
      router-id 10.255.128.15
      update wait-install
      neighbor 10.255.129.121 peer group MLAG-IPv4-UNDERLAY-PEER
      redistribute connected
   !
   vrf HA2
      rd 10.255.128.15:21
      route-target import evpn 21:21
      route-target export evpn 21:21
      router-id 10.255.128.15
      update wait-install
      neighbor 10.255.129.121 peer group MLAG-IPv4-UNDERLAY-PEER
      redistribute connected
   !
   vrf Prod1
      rd 10.255.128.15:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.255.128.15
      update wait-install
      neighbor 10.255.129.121 peer group MLAG-IPv4-UNDERLAY-PEER
      redistribute connected
   !
   vrf Prod2
      rd 10.255.128.15:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      router-id 10.255.128.15
      update wait-install
      neighbor 10.255.129.121 peer group MLAG-IPv4-UNDERLAY-PEER
      redistribute connected
```

## BFD

### Router BFD

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   multihop interval 300 min-rx 300 multiplier 3
```

## Multicast

### IP IGMP Snooping

#### IP IGMP Snooping Summary

| IGMP Snooping | Fast Leave | Interface Restart Query | Proxy | Restart Query Interval | Robustness Variable |
| ------------- | ---------- | ----------------------- | ----- | ---------------------- | ------------------- |
| Enabled | - | - | - | - | - |

#### IP IGMP Snooping Device Configuration

```eos
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.128.0/27 eq 32 |
| 20 | permit 10.255.129.0/27 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.255.128.0/27 eq 32
   seq 20 permit 10.255.129.0/27 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |

##### RM-MLAG-PEER-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | - | origin incomplete | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
!
route-map RM-MLAG-PEER-IN permit 10
   description Make routes learned over MLAG Peer-link less preferred on spines to ensure optimal routing
   set origin incomplete
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| HA1 | enabled |
| HA2 | enabled |
| MGMT | disabled |
| Prod1 | enabled |
| Prod2 | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance HA1
!
vrf instance HA2
!
vrf instance MGMT
!
vrf instance Prod1
!
vrf instance Prod2
```

## Virtual Source NAT

### Virtual Source NAT Summary

| Source NAT VRF | Source NAT IP Address |
| -------------- | --------------------- |
| HA1 | 10.255.14.15 |
| HA2 | 10.255.15.15 |
| Prod1 | 10.255.12.15 |
| Prod2 | 10.255.13.15 |

### Virtual Source NAT Configuration

```eos
!
ip address virtual source-nat vrf HA1 address 10.255.14.15
ip address virtual source-nat vrf HA2 address 10.255.15.15
ip address virtual source-nat vrf Prod1 address 10.255.12.15
ip address virtual source-nat vrf Prod2 address 10.255.13.15
```

## EOS CLI

```eos
!
interface Management1
   no lldp receive
   no lldp transmit

```
