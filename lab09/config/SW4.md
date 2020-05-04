version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

hostname SW4

boot-start-marker

boot-end-marker

no aaa new-model

clock timezone EET 2 0

no ip domain-lookup

ip cef

no ipv6 cef

spanning-tree mode pvst

spanning-tree extend system-id

spanning-tree vlan 1,30,111 priority 24576

spanning-tree vlan 121 priority 20480

interface Port-channel1

 switchport trunk encapsulation dot1q

 switchport mode trunk

interface Ethernet0/0

 switchport trunk encapsulation dot1q

 switchport mode trunk
!
interface Ethernet0/1

 switchport trunk encapsulation dot1q

 switchport mode trunk

interface Ethernet0/2

 switchport trunk encapsulation dot1q

 switchport mode trunk

 channel-group 1 mode on

interface Ethernet0/3

 switchport trunk encapsulation dot1q

 switchport mode trunk

 channel-group 1 mode on

interface Ethernet1/0

 switchport trunk encapsulation dot1q

 switchport mode trunk

interface Ethernet1/1

interface Ethernet1/2

interface Ethernet1/3

interface Vlan30

 ip address 10.10.10.4 255.255.255.128

ip forward-protocol nd

ip http server

ip route 0.0.0.0 0.0.0.0 10.10.10.1 10 name VLAN30

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

control-plane

line con 0

 logging synchronous

line aux 0

line vty 0 4

 login

end
