# dc1-leaf2a

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
- [MLAG](#mlag)
  - [MLAG Summary](#mlag-summary)
  - [MLAG Device Configuration](#mlag-device-configuration)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
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

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.16.1.103/24 | 172.16.1.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.16.1.103/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 192.168.1.1 | MGMT | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf MGMT 192.168.1.1
```

### NTP

#### NTP Summary

##### NTP Local Interface

| Interface | VRF |
| --------- | --- |
| Management1 | MGMT |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 0.pool.ntp.org | MGMT | True | - | - | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface vrf MGMT Management1
ntp server vrf MGMT 0.pool.ntp.org prefer
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

#### Management API HTTP Device Configuration

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

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | 192.168.1.12:9910 | MGMT | token,/tmp/token | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | True |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=192.168.1.12:9910 -cvauth=token,/tmp/token -cvvrf=MGMT -disableaaa -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
   no shutdown
```

## MLAG

### MLAG Summary

| Domain-id | Local-interface | Peer-address | Peer-link |
| --------- | --------------- | ------------ | --------- |
| DC1_L3_BORDER_LEAF2 | Vlan4094 | 10.255.1.69 | Port-Channel3 |

Dual primary detection is disabled.

### MLAG Device Configuration

```eos
!
mlag configuration
   domain-id DC1_L3_BORDER_LEAF2
   local-interface Vlan4094
   peer-address 10.255.1.69
   peer-link Port-Channel3
   reload-delay mlag 300
   reload-delay non-mlag 330
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

### Internal VLAN Allocation Policy Device Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 11 | PROD_VLAN11 | - |
| 12 | PROD_VLAN12 | - |
| 13 | QA_VLAN11 | - |
| 14 | QA_VLAN12 | - |
| 15 | SHAREDSVCS_VLAN11 | - |
| 16 | SHAREDSVCS_VLAN12 | - |
| 21 | PROD_VLAN21 | - |
| 22 | PROD_VLAN22 | - |
| 23 | QA_VLAN21 | - |
| 24 | QA_VLAN22 | - |
| 25 | SHAREDSVCS_VLAN21 | - |
| 26 | SHAREDSVCS_VLAN22 | - |
| 1000 | PROD_FW_TRANSIT | - |
| 1001 | QA_FW_TRANSIT | - |
| 1002 | SHAREDSVCS_FW_TRANSIT | - |
| 3009 | MLAG_iBGP_PROD | LEAF_PEER_L3 |
| 3011 | MLAG_iBGP_QA | LEAF_PEER_L3 |
| 3013 | MLAG_iBGP_SHAREDSVCS | LEAF_PEER_L3 |
| 3401 | L2_VLAN3401 | - |
| 3402 | L2_VLAN3402 | - |
| 3403 | L2_VLAN3403 | - |
| 3404 | L2_VLAN3404 | - |
| 3405 | L2_VLAN3405 | - |
| 3406 | L2_VLAN3406 | - |
| 4093 | LEAF_PEER_L3 | LEAF_PEER_L3 |
| 4094 | MLAG_PEER | MLAG |

### VLANs Device Configuration

```eos
!
vlan 11
   name PROD_VLAN11
!
vlan 12
   name PROD_VLAN12
!
vlan 13
   name QA_VLAN11
!
vlan 14
   name QA_VLAN12
!
vlan 15
   name SHAREDSVCS_VLAN11
!
vlan 16
   name SHAREDSVCS_VLAN12
!
vlan 21
   name PROD_VLAN21
!
vlan 22
   name PROD_VLAN22
!
vlan 23
   name QA_VLAN21
!
vlan 24
   name QA_VLAN22
!
vlan 25
   name SHAREDSVCS_VLAN21
!
vlan 26
   name SHAREDSVCS_VLAN22
!
vlan 1000
   name PROD_FW_TRANSIT
!
vlan 1001
   name QA_FW_TRANSIT
!
vlan 1002
   name SHAREDSVCS_FW_TRANSIT
!
vlan 3009
   name MLAG_iBGP_PROD
   trunk group LEAF_PEER_L3
!
vlan 3011
   name MLAG_iBGP_QA
   trunk group LEAF_PEER_L3
!
vlan 3013
   name MLAG_iBGP_SHAREDSVCS
   trunk group LEAF_PEER_L3
!
vlan 3401
   name L2_VLAN3401
!
vlan 3402
   name L2_VLAN3402
!
vlan 3403
   name L2_VLAN3403
!
vlan 3404
   name L2_VLAN3404
!
vlan 3405
   name L2_VLAN3405
!
vlan 3406
   name L2_VLAN3406
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
| Ethernet3 | MLAG_PEER_dc1-leaf2b_Ethernet3 | *trunk | *- | *- | *['LEAF_PEER_L3', 'MLAG'] | 3 |
| Ethernet4 | MLAG_PEER_dc1-leaf2b_Ethernet4 | *trunk | *- | *- | *['LEAF_PEER_L3', 'MLAG'] | 3 |
| Ethernet5 |  dc1-leaf2-fw1_Eth5 | trunk | - | 4092 | - | - |
| Ethernet8 | DC1-LEAF2C_Ethernet1 | *trunk | *11-16,21-26,1000-1002,3401-3406 | *- | *- | 8 |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_LINK_TO_DC1-SPINE1_Ethernet3 | routed | - | 10.255.255.9/31 | default | 1500 | False | - | - |
| Ethernet2 | P2P_LINK_TO_DC1-SPINE2_Ethernet3 | routed | - | 10.255.255.11/31 | default | 1500 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_LINK_TO_DC1-SPINE1_Ethernet3
   no shutdown
   mtu 1500
   no switchport
   ip address 10.255.255.9/31
!
interface Ethernet2
   description P2P_LINK_TO_DC1-SPINE2_Ethernet3
   no shutdown
   mtu 1500
   no switchport
   ip address 10.255.255.11/31
!
interface Ethernet3
   description MLAG_PEER_dc1-leaf2b_Ethernet3
   no shutdown
   channel-group 3 mode active
!
interface Ethernet4
   description MLAG_PEER_dc1-leaf2b_Ethernet4
   no shutdown
   channel-group 3 mode active
!
interface Ethernet5
   description dc1-leaf2-fw1_Eth5
   no shutdown
   switchport trunk native vlan 4092
   switchport mode trunk
   switchport
   spanning-tree portfast
!
interface Ethernet8
   description DC1-LEAF2C_Ethernet1
   no shutdown
   channel-group 8 mode active
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Type | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel3 | MLAG_PEER_dc1-leaf2b_Po3 | switched | trunk | - | - | ['LEAF_PEER_L3', 'MLAG'] | - | - | - | - |
| Port-Channel8 | DC1-LEAF2C_Po1 | switched | trunk | 11-16,21-26,1000-1002,3401-3406 | - | - | - | - | 8 | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel3
   description MLAG_PEER_dc1-leaf2b_Po3
   no shutdown
   switchport
   switchport mode trunk
   switchport trunk group LEAF_PEER_L3
   switchport trunk group MLAG
!
interface Port-Channel8
   description DC1-LEAF2C_Po1
   no shutdown
   switchport
   switchport trunk allowed vlan 11-16,21-26,1000-1002,3401-3406
   switchport mode trunk
   mlag 8
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | EVPN_Overlay_Peering | default | 10.255.0.5/32 |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | 10.255.1.5/32 |
| Loopback10 | PROD_VTEP_DIAGNOSTICS | PROD | 10.255.10.5/32 |
| Loopback12 | QA_VTEP_DIAGNOSTICS | QA | 10.255.12.5/32 |
| Loopback14 | SHAREDSVCS_VTEP_DIAGNOSTICS | SHAREDSVCS | 10.255.14.5/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | EVPN_Overlay_Peering | default | - |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | - |
| Loopback10 | PROD_VTEP_DIAGNOSTICS | PROD | - |
| Loopback12 | QA_VTEP_DIAGNOSTICS | QA | - |
| Loopback14 | SHAREDSVCS_VTEP_DIAGNOSTICS | SHAREDSVCS | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description EVPN_Overlay_Peering
   no shutdown
   ip address 10.255.0.5/32
!
interface Loopback1
   description VTEP_VXLAN_Tunnel_Source
   no shutdown
   ip address 10.255.1.5/32
!
interface Loopback10
   description PROD_VTEP_DIAGNOSTICS
   no shutdown
   vrf PROD
   ip address 10.255.10.5/32
!
interface Loopback12
   description QA_VTEP_DIAGNOSTICS
   no shutdown
   vrf QA
   ip address 10.255.12.5/32
!
interface Loopback14
   description SHAREDSVCS_VTEP_DIAGNOSTICS
   no shutdown
   vrf SHAREDSVCS
   ip address 10.255.14.5/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan11 | PROD_VLAN11 | PROD | - | False |
| Vlan12 | PROD_VLAN12 | PROD | - | False |
| Vlan13 | QA_VLAN11 | QA | - | False |
| Vlan14 | QA_VLAN12 | QA | - | False |
| Vlan15 | SHAREDSVCS_VLAN11 | SHAREDSVCS | - | False |
| Vlan16 | SHAREDSVCS_VLAN12 | SHAREDSVCS | - | False |
| Vlan21 | PROD_VLAN21 | PROD | - | False |
| Vlan22 | PROD_VLAN22 | PROD | - | False |
| Vlan23 | QA_VLAN21 | QA | - | False |
| Vlan24 | QA_VLAN22 | QA | - | False |
| Vlan25 | SHAREDSVCS_VLAN21 | SHAREDSVCS | - | False |
| Vlan26 | SHAREDSVCS_VLAN22 | SHAREDSVCS | - | False |
| Vlan1000 | PROD_FW_TRANSIT | PROD | - | False |
| Vlan1001 | QA_FW_TRANSIT | QA | - | False |
| Vlan1002 | SHAREDSVCS_FW_TRANSIT | SHAREDSVCS | - | False |
| Vlan3009 | MLAG_PEER_L3_iBGP: vrf PROD | PROD | 1500 | False |
| Vlan3011 | MLAG_PEER_L3_iBGP: vrf QA | QA | 1500 | False |
| Vlan3013 | MLAG_PEER_L3_iBGP: vrf SHAREDSVCS | SHAREDSVCS | 1500 | False |
| Vlan4093 | MLAG_PEER_L3_PEERING | default | 1500 | False |
| Vlan4094 | MLAG_PEER | default | 1500 | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan11 |  PROD  |  -  |  10.10.11.1/24  |  -  |  -  |  -  |  -  |
| Vlan12 |  PROD  |  -  |  10.10.12.1/24  |  -  |  -  |  -  |  -  |
| Vlan13 |  QA  |  -  |  10.11.11.1/24  |  -  |  -  |  -  |  -  |
| Vlan14 |  QA  |  -  |  10.11.12.1/24  |  -  |  -  |  -  |  -  |
| Vlan15 |  SHAREDSVCS  |  -  |  10.12.11.1/24  |  -  |  -  |  -  |  -  |
| Vlan16 |  SHAREDSVCS  |  -  |  10.12.12.1/24  |  -  |  -  |  -  |  -  |
| Vlan21 |  PROD  |  -  |  10.10.21.1/24  |  -  |  -  |  -  |  -  |
| Vlan22 |  PROD  |  -  |  10.10.22.1/24  |  -  |  -  |  -  |  -  |
| Vlan23 |  QA  |  -  |  10.11.21.1/24  |  -  |  -  |  -  |  -  |
| Vlan24 |  QA  |  -  |  10.11.22.1/24  |  -  |  -  |  -  |  -  |
| Vlan25 |  SHAREDSVCS  |  -  |  10.12.21.1/24  |  -  |  -  |  -  |  -  |
| Vlan26 |  SHAREDSVCS  |  -  |  10.12.22.1/24  |  -  |  -  |  -  |  -  |
| Vlan1000 |  PROD  |  -  |  10.254.254.1/24  |  -  |  -  |  -  |  -  |
| Vlan1001 |  QA  |  -  |  10.254.253.1/24  |  -  |  -  |  -  |  -  |
| Vlan1002 |  SHAREDSVCS  |  -  |  10.254.252.1/24  |  -  |  -  |  -  |  -  |
| Vlan3009 |  PROD  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan3011 |  QA  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan3013 |  SHAREDSVCS  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan4093 |  default  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan4094 |  default  |  10.255.1.68/31  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan11
   description PROD_VLAN11
   no shutdown
   vrf PROD
   ip address virtual 10.10.11.1/24
!
interface Vlan12
   description PROD_VLAN12
   no shutdown
   vrf PROD
   ip address virtual 10.10.12.1/24
!
interface Vlan13
   description QA_VLAN11
   no shutdown
   vrf QA
   ip address virtual 10.11.11.1/24
!
interface Vlan14
   description QA_VLAN12
   no shutdown
   vrf QA
   ip address virtual 10.11.12.1/24
!
interface Vlan15
   description SHAREDSVCS_VLAN11
   no shutdown
   vrf SHAREDSVCS
   ip address virtual 10.12.11.1/24
!
interface Vlan16
   description SHAREDSVCS_VLAN12
   no shutdown
   vrf SHAREDSVCS
   ip address virtual 10.12.12.1/24
!
interface Vlan21
   description PROD_VLAN21
   no shutdown
   vrf PROD
   ip address virtual 10.10.21.1/24
!
interface Vlan22
   description PROD_VLAN22
   no shutdown
   vrf PROD
   ip address virtual 10.10.22.1/24
!
interface Vlan23
   description QA_VLAN21
   no shutdown
   vrf QA
   ip address virtual 10.11.21.1/24
!
interface Vlan24
   description QA_VLAN22
   no shutdown
   vrf QA
   ip address virtual 10.11.22.1/24
!
interface Vlan25
   description SHAREDSVCS_VLAN21
   no shutdown
   vrf SHAREDSVCS
   ip address virtual 10.12.21.1/24
!
interface Vlan26
   description SHAREDSVCS_VLAN22
   no shutdown
   vrf SHAREDSVCS
   ip address virtual 10.12.22.1/24
!
interface Vlan1000
   description PROD_FW_TRANSIT
   no shutdown
   vrf PROD
   ip address virtual 10.254.254.1/24
!
interface Vlan1001
   description QA_FW_TRANSIT
   no shutdown
   vrf QA
   ip address virtual 10.254.253.1/24
!
interface Vlan1002
   description SHAREDSVCS_FW_TRANSIT
   no shutdown
   vrf SHAREDSVCS
   ip address virtual 10.254.252.1/24
!
interface Vlan3009
   description MLAG_PEER_L3_iBGP: vrf PROD
   no shutdown
   mtu 1500
   vrf PROD
   ip address 10.255.1.100/31
!
interface Vlan3011
   description MLAG_PEER_L3_iBGP: vrf QA
   no shutdown
   mtu 1500
   vrf QA
   ip address 10.255.1.100/31
!
interface Vlan3013
   description MLAG_PEER_L3_iBGP: vrf SHAREDSVCS
   no shutdown
   mtu 1500
   vrf SHAREDSVCS
   ip address 10.255.1.100/31
!
interface Vlan4093
   description MLAG_PEER_L3_PEERING
   no shutdown
   mtu 1500
   ip address 10.255.1.100/31
!
interface Vlan4094
   description MLAG_PEER
   no shutdown
   mtu 1500
   no autostate
   ip address 10.255.1.68/31
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
| 11 | 10011 | - | - |
| 12 | 10012 | - | - |
| 13 | 11013 | - | - |
| 14 | 11014 | - | - |
| 15 | 12015 | - | - |
| 16 | 12016 | - | - |
| 21 | 10021 | - | - |
| 22 | 10022 | - | - |
| 23 | 11023 | - | - |
| 24 | 11024 | - | - |
| 25 | 12025 | - | - |
| 26 | 12026 | - | - |
| 1000 | 11000 | - | - |
| 1001 | 12001 | - | - |
| 1002 | 13002 | - | - |
| 3401 | 13401 | - | - |
| 3402 | 13402 | - | - |
| 3403 | 14403 | - | - |
| 3404 | 14404 | - | - |
| 3405 | 15405 | - | - |
| 3406 | 15406 | - | - |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| PROD | 10 | - |
| QA | 12 | - |
| SHAREDSVCS | 14 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description dc1-leaf2a_VTEP
   vxlan source-interface Loopback1
   vxlan virtual-router encapsulation mac-address mlag-system-id
   vxlan udp-port 4789
   vxlan vlan 11 vni 10011
   vxlan vlan 12 vni 10012
   vxlan vlan 13 vni 11013
   vxlan vlan 14 vni 11014
   vxlan vlan 15 vni 12015
   vxlan vlan 16 vni 12016
   vxlan vlan 21 vni 10021
   vxlan vlan 22 vni 10022
   vxlan vlan 23 vni 11023
   vxlan vlan 24 vni 11024
   vxlan vlan 25 vni 12025
   vxlan vlan 26 vni 12026
   vxlan vlan 1000 vni 11000
   vxlan vlan 1001 vni 12001
   vxlan vlan 1002 vni 13002
   vxlan vlan 3401 vni 13401
   vxlan vlan 3402 vni 13402
   vxlan vlan 3403 vni 14403
   vxlan vlan 3404 vni 14404
   vxlan vlan 3405 vni 15405
   vxlan vlan 3406 vni 15406
   vxlan vrf PROD vni 10
   vxlan vrf QA vni 12
   vxlan vrf SHAREDSVCS vni 14
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

Virtual Router MAC Address: 00:1c:73:00:00:99

#### Virtual Router MAC Address Device Configuration

```eos
!
ip virtual-router mac-address 00:1c:73:00:00:99
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| MGMT | False |
| PROD | True |
| QA | True |
| SHAREDSVCS | True |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
ip routing vrf PROD
ip routing vrf QA
ip routing vrf SHAREDSVCS
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |
| PROD | false |
| QA | false |
| SHAREDSVCS | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.16.1.1 | - | 1 | - | - | - |
| PROD | 10.254.255.1/32 | 10.254.254.2 | - | 1 | - | - | - |
| QA | 10.254.255.1/32 | 10.254.253.2 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.16.1.1
ip route vrf PROD 10.254.255.1/32 10.254.254.2
ip route vrf QA 10.254.255.1/32 10.254.253.2
```

### Router BGP

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65102 | 10.255.0.5 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 4 ecmp 4 |

#### Router BGP Peer Groups

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
| Remote AS | 65102 |
| Next-hop self | True |
| Send community | all |
| Maximum routes | 12000 |

##### PROD-PALO

| Settings | Value |
| -------- | ----- |
| Address Family | Inter-VRF Inspection |
| Remote AS | 64512 |
| Local AS | 65000 |
| Ebgp multihop | 3 |
| Send community | all |
| Maximum routes | 12000 |

##### QA-PALO

| Settings | Value |
| -------- | ----- |
| Address Family | Inter-VRF Inspection |
| Remote AS | 64512 |
| Local AS | 65001 |
| Ebgp multihop | 3 |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.0.1 | 65100 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - | - |
| 10.255.0.2 | 65100 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | default | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.255.255.8 | 65100 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.255.10 | 65100 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | PROD | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.254.255.1 | 64512 | PROD | - | Inherited from peer group PROD-PALO | Inherited from peer group PROD-PALO | - | - | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | QA | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.254.255.1 | 64512 | QA | - | Inherited from peer group QA-PALO | Inherited from peer group QA-PALO | - | - | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | SHAREDSVCS | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| EVPN-OVERLAY-PEERS | True | default |

#### Router BGP VLANs

| VLAN | Route-Distinguisher | Both Route-Target | Import Route Target | Export Route-Target | Redistribute |
| ---- | ------------------- | ----------------- | ------------------- | ------------------- | ------------ |
| 11 | 10.255.0.5:10011 | 10011:10011 | - | - | learned |
| 12 | 10.255.0.5:10012 | 10012:10012 | - | - | learned |
| 13 | 10.255.0.5:11013 | 11013:11013 | - | - | learned |
| 14 | 10.255.0.5:11014 | 11014:11014 | - | - | learned |
| 15 | 10.255.0.5:12015 | 12015:12015 | - | - | learned |
| 16 | 10.255.0.5:12016 | 12016:12016 | - | - | learned |
| 21 | 10.255.0.5:10021 | 10021:10021 | - | - | learned |
| 22 | 10.255.0.5:10022 | 10022:10022 | - | - | learned |
| 23 | 10.255.0.5:11023 | 11023:11023 | - | - | learned |
| 24 | 10.255.0.5:11024 | 11024:11024 | - | - | learned |
| 25 | 10.255.0.5:12025 | 12025:12025 | - | - | learned |
| 26 | 10.255.0.5:12026 | 12026:12026 | - | - | learned |
| 1000 | 10.255.0.5:11000 | 11000:11000 | - | - | learned |
| 1001 | 10.255.0.5:12001 | 12001:12001 | - | - | learned |
| 1002 | 10.255.0.5:13002 | 13002:13002 | - | - | learned |
| 3401 | 10.255.0.5:13401 | 13401:13401 | - | - | learned |
| 3402 | 10.255.0.5:13402 | 13402:13402 | - | - | learned |
| 3403 | 10.255.0.5:14403 | 14403:14403 | - | - | learned |
| 3404 | 10.255.0.5:14404 | 14404:14404 | - | - | learned |
| 3405 | 10.255.0.5:15405 | 15405:15405 | - | - | learned |
| 3406 | 10.255.0.5:15406 | 15406:15406 | - | - | learned |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| PROD | 10.255.0.5:10 | connected |
| QA | 10.255.0.5:12 | connected |
| SHAREDSVCS | 10.255.0.5:14 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65102
   router-id 10.255.0.5
   maximum-paths 4 ecmp 4
   no bgp default ipv4-unicast
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
   neighbor MLAG-IPv4-UNDERLAY-PEER remote-as 65102
   neighbor MLAG-IPv4-UNDERLAY-PEER next-hop-self
   neighbor MLAG-IPv4-UNDERLAY-PEER description dc1-leaf2b
   neighbor MLAG-IPv4-UNDERLAY-PEER password 7 <removed>
   neighbor MLAG-IPv4-UNDERLAY-PEER send-community
   neighbor MLAG-IPv4-UNDERLAY-PEER maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER route-map RM-MLAG-PEER-IN in
   neighbor PROD-PALO peer group
   neighbor PROD-PALO remote-as 64512
   neighbor PROD-PALO local-as 65000 no-prepend replace-as
   neighbor PROD-PALO description VRF-PROD ASN
   neighbor PROD-PALO ebgp-multihop 3
   neighbor PROD-PALO send-community
   neighbor PROD-PALO maximum-routes 12000
   neighbor QA-PALO peer group
   neighbor QA-PALO remote-as 64512
   neighbor QA-PALO local-as 65001 no-prepend replace-as
   neighbor QA-PALO description VRF-QA ASN
   neighbor QA-PALO ebgp-multihop 3
   neighbor QA-PALO send-community
   neighbor QA-PALO maximum-routes 12000
   neighbor 10.255.0.1 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.0.1 remote-as 65100
   neighbor 10.255.0.1 description dc1-spine1
   neighbor 10.255.0.2 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.0.2 remote-as 65100
   neighbor 10.255.0.2 description dc1-spine2
   neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
   neighbor 10.255.1.101 description dc1-leaf2b
   neighbor 10.255.255.8 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.8 remote-as 65100
   neighbor 10.255.255.8 description dc1-spine1_Ethernet3
   neighbor 10.255.255.10 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.10 remote-as 65100
   neighbor 10.255.255.10 description dc1-spine2_Ethernet3
   redistribute connected route-map RM-CONN-2-BGP
   !
   vlan 1000
      rd 10.255.0.5:11000
      route-target both 11000:11000
      redistribute learned
   !
   vlan 1001
      rd 10.255.0.5:12001
      route-target both 12001:12001
      redistribute learned
   !
   vlan 1002
      rd 10.255.0.5:13002
      route-target both 13002:13002
      redistribute learned
   !
   vlan 11
      rd 10.255.0.5:10011
      route-target both 10011:10011
      redistribute learned
   !
   vlan 12
      rd 10.255.0.5:10012
      route-target both 10012:10012
      redistribute learned
   !
   vlan 13
      rd 10.255.0.5:11013
      route-target both 11013:11013
      redistribute learned
   !
   vlan 14
      rd 10.255.0.5:11014
      route-target both 11014:11014
      redistribute learned
   !
   vlan 15
      rd 10.255.0.5:12015
      route-target both 12015:12015
      redistribute learned
   !
   vlan 16
      rd 10.255.0.5:12016
      route-target both 12016:12016
      redistribute learned
   !
   vlan 21
      rd 10.255.0.5:10021
      route-target both 10021:10021
      redistribute learned
   !
   vlan 22
      rd 10.255.0.5:10022
      route-target both 10022:10022
      redistribute learned
   !
   vlan 23
      rd 10.255.0.5:11023
      route-target both 11023:11023
      redistribute learned
   !
   vlan 24
      rd 10.255.0.5:11024
      route-target both 11024:11024
      redistribute learned
   !
   vlan 25
      rd 10.255.0.5:12025
      route-target both 12025:12025
      redistribute learned
   !
   vlan 26
      rd 10.255.0.5:12026
      route-target both 12026:12026
      redistribute learned
   !
   vlan 3401
      rd 10.255.0.5:13401
      route-target both 13401:13401
      redistribute learned
   !
   vlan 3402
      rd 10.255.0.5:13402
      route-target both 13402:13402
      redistribute learned
   !
   vlan 3403
      rd 10.255.0.5:14403
      route-target both 14403:14403
      redistribute learned
   !
   vlan 3404
      rd 10.255.0.5:14404
      route-target both 14404:14404
      redistribute learned
   !
   vlan 3405
      rd 10.255.0.5:15405
      route-target both 15405:15405
      redistribute learned
   !
   vlan 3406
      rd 10.255.0.5:15406
      route-target both 15406:15406
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY-PEERS activate
   !
   address-family ipv4
      no neighbor EVPN-OVERLAY-PEERS activate
      neighbor IPv4-UNDERLAY-PEERS activate
      neighbor MLAG-IPv4-UNDERLAY-PEER activate
      neighbor PROD-PALO activate
      neighbor QA-PALO activate
   !
   vrf PROD
      rd 10.255.0.5:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.255.0.5
      neighbor 10.254.255.1 remote-as 64512
      neighbor 10.254.255.1 peer group PROD-PALO
      neighbor 10.254.255.1 description Palo Alto Firewall
      neighbor 10.254.255.1 update-source loopback10
      neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
      redistribute connected
      !
      address-family ipv4
         neighbor 10.254.255.1 activate
   !
   vrf QA
      rd 10.255.0.5:12
      route-target import evpn 12:12
      route-target export evpn 12:12
      router-id 10.255.0.5
      neighbor 10.254.255.1 remote-as 64512
      neighbor 10.254.255.1 peer group QA-PALO
      neighbor 10.254.255.1 description Palo Alto Firewall
      neighbor 10.254.255.1 update-source loopback12
      neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
      redistribute connected
      !
      address-family ipv4
         neighbor 10.254.255.1 activate
   !
   vrf SHAREDSVCS
      rd 10.255.0.5:14
      route-target import evpn 14:14
      route-target export evpn 14:14
      router-id 10.255.0.5
      neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
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
| 10 | permit 10.255.0.0/27 eq 32 |
| 20 | permit 10.255.1.0/27 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.255.0.0/27 eq 32
   seq 20 permit 10.255.1.0/27 eq 32
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
| MGMT | disabled |
| PROD | enabled |
| QA | enabled |
| SHAREDSVCS | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
!
vrf instance PROD
!
vrf instance QA
!
vrf instance SHAREDSVCS
```

## Virtual Source NAT

### Virtual Source NAT Summary

| Source NAT VRF | Source NAT IP Address |
| -------------- | --------------------- |
| PROD | 10.255.10.5 |
| QA | 10.255.12.5 |
| SHAREDSVCS | 10.255.14.5 |

### Virtual Source NAT Configuration

```eos
!
ip address virtual source-nat vrf PROD address 10.255.10.5
ip address virtual source-nat vrf QA address 10.255.12.5
ip address virtual source-nat vrf SHAREDSVCS address 10.255.14.5
```
