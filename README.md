Example of Redistributed "static" route, advertised by a "non"-ABR router

Notes;
redistribution running on R3, R4, R5

[R3 - 192.168.1.252]
R3 is located in a Cisco NSSA area, area 1.
 Issue demo'ed by this project 
  -- adding additional areas to R3 causes a P-Bit issue for prefixes advertised
     via static redistribution into ospf.
     e.g. R3 Advertises Mgmnt address for R7, which is  purposely not configured for routing;
          ip route 192.168.1.245 255.255.255.255 nexthop 172.16.20.2


[R4 - 192.168.1.253]
  R4 Advertises Loopback addresses into BGP toward Mgr POD
   network 192.168.1.240 mask 255.255.255.255
   network 192.168.1.245 mask 255.255.255.255
   network 192.168.1.250 mask 255.255.255.255
   network 192.168.1.251 mask 255.255.255.255
   network 192.168.1.252 mask 255.255.255.255
   network 192.168.1.253 mask 255.255.255.255

!!For Mgr Prefixes only 
!! BGP bridges the GAP between ospf instances running in the Mgr POD
   and ospf running in the Network POD.  Mgr Prefixes transit R5<>R4  ;
   1. OSPF_MGR_POD-to-BGP
   2. BGP to OSPF_NET_POD

R4 redist's BGP prefixes (used by Netmgr) into OSPF   [Redist Outbound to Network POD]
ref prefix-list Management
 ip prefix-list Management seq 5  permit  10.200.10.0/24     !Ubuntu server
 ip prefix-list Management seq 10 permit 192.168.100.1/32    !S1 Loopback0


[R5 - 192.168.1.254]
R5 redist's OSPF prefixes (from NetMgr) into BGP [ Redist Inbound from Mgr POD]
ref prefix-list Management
 ip prefix-list Management seq 5  permit  10.200.10.0/24     !Ubuntu server
 ip prefix-list Management seq 10 permit 192.168.100.1/32    !S1 Loopback0

There is no exhange of routes between R5 & S1 except for local interfaces. 
S1 has static routes configured for reachablility to managed ip addresses
in the network POD, with a next hop on R5.