version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

hostname SW2

boot-start-marker

boot-end-marker

no aaa new-model

clock timezone EET 2 0

no ip domain-lookup

ip cef

no ipv6 cef

spanning-tree mode pvst

spanning-tree extend system-id

interface Ethernet0/0

 switchport trunk encapsulation dot1q

 switchport mode trunk

interface Ethernet0/1
 
 switchport trunk encapsulation dot1q
 
 switchport mode trunk

interface Ethernet0/2
 
 switchport access vlan 121
 
 switchport mode access

interface Ethernet0/3

interface Ethernet1/0

interface Ethernet1/1

interface Ethernet1/2

interface Ethernet1/3

interface Vlan30
 
 ip address 10.10.10.2 255.255.255.128
 
 ipv6 address 2001:1010::2/64

ip forward-protocol nd

ip http server

ip route 0.0.0.0 0.0.0.0 10.10.10.1 10 name VLAN30

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ipv6 route ::/0 2001:1010::1 name V30HSRP

control-plane

line con 0
 
 logging synchronous

line aux 0

line vty 0 4
 
 login

end
