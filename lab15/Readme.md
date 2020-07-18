### VPN. GRE. DmVPN ####

Цель: Настроить GRE между офисами Москва и С.-Петербург Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. Настроите GRE между офисами Москва и С.-Петербург
2. Настроите DMVMN между Москва и Чокурдах, Лабытнанги
8. Все офисы в лабораторной работе должны иметь IP связность
9. План работы и изменения зафиксированы в документации 

![](https://github.com/svasornd/otus_network/blob/master/lab15/Lab15.png)


#### Настроите GRE между офисами Москва и С.-Петербург ####

Москва

R14

    		interface Tunnel1
     		 ip address 172.16.1.1 255.255.255.0
    		 tunnel source 20.0.0.14
    		 tunnel destination 25.0.0.18
    
    		router ospf 1
    		 area 25 filter-list prefix AREA-25-FILTER in
    		 area 25 filter-list prefix AREA-25-FILTER-BGP out
    		 network 172.16.1.0 0.0.0.255 area 25
    
    		ip prefix-list AREA-25-FILTER seq 10 deny 20.0.0.14/32
    		ip prefix-list AREA-25-FILTER seq 15 deny 20.0.0.15/32
    		ip prefix-list AREA-25-FILTER seq 17 deny 172.16.1.0/24
    		ip prefix-list AREA-25-FILTER seq 18 deny 172.16.2.0/24
    		ip prefix-list AREA-25-FILTER seq 20 permit 0.0.0.0/0 le 32
    
    		ip prefix-list AREA-25-FILTER-BGP seq 10 permit 10.15.0.0/20
    		ip prefix-list AREA-25-FILTER-BGP seq 20 deny 0.0.0.0/0 le 32
    
Санкт-Петербург

R18

    		interface Tunnel1
    		 ip address 172.16.1.2 255.255.255.0
    		 tunnel source 25.0.0.18
    		 tunnel destination 20.0.0.14
    
    		router ospf 1
    		 area 25 filter-list prefix AREA-25-FILTER out
    		 summary-address 10.15.0.0 255.255.240.0
    		 redistribute bgp 2042 subnets route-map DISTR-BGP-MAP
    		 network 10.15.0.0 0.0.255.255 area 25
    		 network 172.16.1.0 0.0.0.255 area 25
    		 network 172.16.2.0 0.0.0.255 area 25
    
    		router bgp 2042
    		 bgp router-id 25.0.0.18
    		 bgp log-neighbor-changes
    		 bgp bestpath as-path multipath-relax
    		 bgp redistribute-internal
    
    		ip access-list standard REDIST-BGP
    		 permit 10.15.0.0 0.0.255.255
    
    		route-map DISTR-BGP-MAP permit 10
    		 match ip address REDIST-BGP
    
    		ip prefix-list AREA-25-FILTER seq 5 permit 10.15.0.0/16
    		ip prefix-list AREA-25-FILTER seq 10 deny 0.0.0.0/0 le 32





    Связность Москва VPC1 - Санкт-Петербург VPC8
    
    		VPCS> ping 10.15.12.2
    		84 bytes from 10.15.12.2 icmp_seq=1 ttl=60 time=5.937 ms
    		84 bytes from 10.15.12.2 icmp_seq=2 ttl=60 time=3.650 ms
    		84 bytes from 10.15.12.2 icmp_seq=3 ttl=60 time=4.011 ms
    		84 bytes from 10.15.12.2 icmp_seq=4 ttl=60 time=3.990 ms
    		84 bytes from 10.15.12.2 icmp_seq=5 ttl=60 time=3.816 ms
    
    		VPCS> trace 10.15.12.2
    		trace to 10.15.12.2, 8 hops max, press Ctrl+C to stop
    		 1   10.10.11.254   0.835 ms  0.632 ms  0.640 ms
    		 2   10.10.15.5   0.888 ms  0.833 ms  1.071 ms
    		 3   172.16.1.2   2.503 ms  1.883 ms  1.905 ms
    		 4   10.15.15.6   1.950 ms  1.621 ms  1.744 ms
    		 5   *10.15.12.2   2.654 ms 
    		 
    Связность  Санкт-Петербург VPC8 -  Москва VPC1
    
    		VPCS> ping 10.10.11.3
    		84 bytes from 10.10.11.3 icmp_seq=1 ttl=60 time=4.870 ms
    		84 bytes from 10.10.11.3 icmp_seq=2 ttl=60 time=3.855 ms
    		84 bytes from 10.10.11.3 icmp_seq=3 ttl=60 time=4.535 ms
    		84 bytes from 10.10.11.3 icmp_seq=4 ttl=60 time=4.339 ms
    		84 bytes from 10.10.11.3 icmp_seq=5 ttl=60 time=3.913 ms
    
    		VPCS> trace 10.10.11.3
    		trace to 10.10.11.3, 8 hops max, press Ctrl+C to stop
    		 1   10.15.12.253   1.433 ms  0.700 ms  0.907 ms
    		 2   10.15.15.5   1.535 ms  1.258 ms  0.936 ms
    		 3   172.16.2.1   2.640 ms  1.725 ms  1.852 ms
    		 4   10.10.15.14   1.719 ms  1.853 ms  1.703 ms
    		 5   *10.10.11.3   3.041 ms 
    

#### Настроите DMVMN между Москва и Чокурдах, Лабытнанги ####

Москва

R14

    		interface Tunnel10
    		 ip address 172.16.10.1 255.255.255.0
    		 no ip redirects
    		 ip nhrp map multicast dynamic
    		 ip nhrp network-id 10
    		 ip ospf network broadcast
    		 ip ospf priority 10
    		 tunnel source Loopback2
    		 tunnel mode gre multipoint
    
    		router ospf 1
    		 area 27 filter-list prefix AREA-27-FILTER in
    		 network 172.16.10.0 0.0.0.255 area 27
    
    		ip prefix-list AREA-27-FILTER seq 10 deny 20.0.0.14/32
    		ip prefix-list AREA-27-FILTER seq 20 deny 20.0.0.15/32
    		ip prefix-list AREA-27-FILTER seq 40 permit 0.0.0.0/0 le 32
    

Лабытнанги 

R27

    		interface Tunnel10
    		 ip address 172.16.10.5 255.255.255.0
    		 ip nhrp map 172.16.10.1 20.0.0.14
    		 ip nhrp network-id 10
    		 ip nhrp nhs 172.16.10.1
    		 ip ospf network broadcast
    		 ip ospf priority 0
    		 tunnel source Ethernet0/0
    		 tunnel destination 20.0.0.14
     
    		router ospf 1
    		 network 10.21.1.129 0.0.0.0 area 27
    		 network 172.16.10.0 0.0.0.255 area 27
    
    R27#show ip route ospf
    
          10.0.0.0/8 is variably subnetted, 16 subnets, 5 masks
    O IA     10.10.0.12/32 [110/1011] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.0.13/32 [110/1011] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.0.14/32 [110/1001] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.0.19/32 [110/1011] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.10.0/25 [110/1020] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.11.0/24 [110/1020] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.12.0/24 [110/1020] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.15.0/30 [110/1010] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.15.4/30 [110/1010] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.15.8/30 [110/1010] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.15.12/30 [110/1020] via 172.16.10.1, 00:43:26, Tunnel10
    O IA     10.10.15.16/30 [110/1020] via 172.16.10.1, 00:43:26, Tunnel10
    O E2     10.15.0.0/20 [110/1] via 172.16.10.1, 00:43:26, Tunnel10
    O        10.20.0.0/25 [110/1010] via 172.16.10.4, 00:41:35, Tunnel10
    
    

Чокурдах

R28

    		interface Tunnel10
    		 ip address 172.16.10.4 255.255.255.0
    		 ip nhrp map 172.16.10.1 20.0.0.14
    		 ip nhrp network-id 10
    		 ip nhrp nhs 172.16.10.1
    		 ip ospf network broadcast
    		 ip ospf priority 0
    		 tunnel source Ethernet0/0
    		 tunnel destination 20.0.0.14
     
    		router ospf 1
    		 network 10.20.0.0 0.0.0.3 area 27
    		 network 172.16.10.0 0.0.0.255 area 27
    
    r28#show ip route ospf
    
          10.0.0.0/8 is variably subnetted, 21 subnets, 5 masks
    O IA     10.10.0.12/32 [110/1011] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.0.13/32 [110/1011] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.0.14/32 [110/1001] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.0.19/32 [110/1011] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.10.0/25 [110/1020] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.11.0/24 [110/1020] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.12.0/24 [110/1020] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.15.0/30 [110/1010] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.15.4/30 [110/1010] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.15.8/30 [110/1010] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.15.12/30 [110/1020] via 172.16.10.1, 00:40:55, Tunnel10
    O IA     10.10.15.16/30 [110/1020] via 172.16.10.1, 00:40:55, Tunnel10
    O E2     10.15.0.0/20 [110/1] via 172.16.10.1, 00:40:55, Tunnel10
    O        10.21.1.129/32 [110/1001] via 172.16.10.5, 00:40:55, Tunnel10
    
    



    Связность Москва VPC7 - Чокурдах SW29
    
    		VPCS> ping 10.20.0.29
    		84 bytes from 10.20.0.29 icmp_seq=1 ttl=252 time=3.833 ms
    		84 bytes from 10.20.0.29 icmp_seq=2 ttl=252 time=4.432 ms
    		84 bytes from 10.20.0.29 icmp_seq=3 ttl=252 time=4.110 ms
    		84 bytes from 10.20.0.29 icmp_seq=4 ttl=252 time=4.076 ms
    		84 bytes from 10.20.0.29 icmp_seq=5 ttl=252 time=3.945 ms
    
    		VPCS> trace 10.20.0.29
    		trace to 10.20.0.29, 8 hops max, press Ctrl+C to stop
    		 1   10.10.12.253   2.208 ms  0.989 ms  1.002 ms
    		 2   10.10.15.9   1.256 ms  1.118 ms  1.057 ms
    		 3   172.16.10.4   3.111 ms  2.021 ms  2.155 ms
    		 4   *10.20.0.29   3.205 ms (
    		 
    Связность  Чокурдах SW29 -  Москва VPC7 
    
    		sw29#ping 10.21.1.129
    		Type escape sequence to abort.
    		Sending 5, 100-byte ICMP Echos to 10.21.1.129, timeout is 2 seconds:
    		!!!!!
    
    		sw29#traceroute 10.10.12.3
    		Type escape sequence to abort.
    		Tracing the route to 10.10.12.3
    		VRF info: (vrf in name/id, vrf out name/id)
    		  1 10.20.0.1 32 msec 1 msec 1 msec
    		  2 172.16.10.1 2 msec 3 msec 1 msec
    		  3 10.10.15.6 2 msec 6 msec 2 msec
    		  4 10.10.12.3 6 msec 4 msec 2 msec
    		  
    Связность  Лабытнанги R27  - Чокурдах SW29 
    
    		R27#ping 10.20.0.29
    		Type escape sequence to abort.
    		Sending 5, 100-byte ICMP Echos to 10.20.0.29, timeout is 2 seconds:
    		!!!!!
    		Success rate is 100 percent (5/5), round-trip min/avg/max = 2/3/4 ms
    
    		R27#traceroute 10.20.0.29
    		Type escape sequence to abort.
    		Tracing the route to 10.20.0.29
    		VRF info: (vrf in name/id, vrf out name/id)
    		  1 172.16.10.1 2 msec 2 msec 1 msec
    		  2 172.16.10.4 3 msec 3 msec 2 msec
    		  3 10.20.0.29 3 msec *  8 msec



