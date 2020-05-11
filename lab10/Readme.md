## Работа протокола OSPF для IPv6 ##

Домашнее задание

OSPF для IPv6

Цель: Настроить OSPF для IPv6, сохранив ту же логику работы, что у OSPF для IPv4

1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone
2. Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по-умолчанию
3. [Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию]
4. [Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101]
5. План работы и изменения зафиксированы в документации 

Схема AREA OSPF филиала Москва

![](https://github.com/svasornd/otus_network/blob/master/lab10/lab_10_area_ospf.png)

| OSPF | Area | Type Area | Router ID  |
|------|------|-----------|------------|
| R12  | 10   | Standard  | 10.10.0.12 |
| R13  | 10   | Standard  | 10.10.0.13 |
| R14  | 0    | Backbone  | 10.10.0.14 |
| R14  | 10   | Standard  | 10.10.0.14 |
| R14  | 101  | TotalStub | 10.10.0.14 |
| R15  | 0    | Backbone  | 10.10.0.15 |
| R15  | 10   | Standard  | 10.10.0.15 |
| R15  | 101  | TotalStub | 10.10.0.15 |
| R19  | 101  | Stub      | 10.10.0.19 |
| R20  | 102  | Standard  | 10.10.0.19 |

Схама и адресация филиала Москва IPv6

| Устройство | Интерфейс | Адрес IPv6             | Префикс IPv6 | Шлюз                | link local    |
|------------|-----------|------------------------|--------------|---------------------|---------------|
| R14        | Lo1       | 2001:1010:1000::14     | /128         |                     | FE80::1400/10 |
| R14        | e0/2      | 2001:5050:0:5::2       | /64          |                     | FE80::1400/10 |
| R14        | e0/0      | 2001:1010:0:02::1      | /64          |                     | FE80::1400/10 |
| R14        | e0/1      | 2001:1010:0:3::1       | /64          |                     | FE80::1400/10 |
| R14        | e0/3      | 2001:1010:0:1::1       | /64          |                     | FE80::1400/10 |
| R15        | Lo1       | 2001:1010:1000::15     | /128         |                     | FE80::1500/10 |
| R15        | e0/2      | 2001:6060:0:3::2       | /64          |                     | FE80::1500/10 |
| R15        | e0/0      | 2001:1010:0:5::1       | /64          |                     | FE80::1500/10 |
| R15        | e0/1      | 2001:1010:0:4::1       | /64          |                     | FE80::1500/10 |
| R15        | e0/3      | 2001:1010:0:6::1       | /64          |                     | FE80::1500/10 |
| R12        | Lo1       | 2001:1010:1000::12     | /128         |                     | FE80::1200/10 |
| R12        | e0/2      | 2001:1010:0:2::2       | /64          |                     | FE80::1200/10 |
| R12        | e0/3      | 2001:1010:0:4::2       | /64          |                     | FE80::1200/10 |
| R12        | VLAN111   | 2001:1010:0:1000::1000 | /64          |                     | FE80::1200/10 |
| R12        | VLAN121   | 2001:1010:0:2000::1000 | /64          |                     | FE80::1200/10 |
| R12        | VLAN30    | 2001:1010::1000        | /64          |                     | FE80::1200/10 |
| R13        | Lo1       | 2001:1010:1000::13     | /128         |                     | FE80::1300/10 |
| R13        | e0/2      | 2001:1010:0:5::2       | /64          |                     | FE80::1300/10 |
| R13        | e0/3      | 2001:1010:0:3::2       | /64          |                     | FE80::1300/10 |
| R13        | VLAN111   | 2001:1010:0:1000::1001 | /64          |                     | FE80::1300/10 |
| R13        | VLAN121   | 2001:1010:0:2000::1001 | /64          |                     | FE80::1300/10 |
| R13        | VLAN30    | 2001:1010::1001        | /64          |                     | FE80::1300/10 |
| R19        | Lo1       | 2001:1010:1000::19     | /128         |                     | FE80::1900/10 |
| R19        | e0/0      | 2001:1010:0:1::2       | /64          |                     | FE80::1900/10 |
| R20        | Lo1       | 2001:1010:1000::20     | /128         |                     | FE80::2000/10 |
| R20        | e0/0      | 2001:1010:0:6::2       | /64          |                     | FE80::2000/10 |
| SW3        | vlan30    | 2001:1010::3           | /64          | 2001:1010::1        | FE80::1403/10 |
| SW2        | vlan30    | 2001:1010::2           | /64          | 2001:1010::1        | FE80::1403/10 |
| SW4        | vlan30    | 2001:1010::4           | /64          | 2001:1010::1        | FE80::1403/10 |
| SW5        | vlan30    | 2001:1010::5           | /64          | 2001:1010::1        | FE80::1403/10 |
| VPC1       | eth0      | 2001:1010:0:1000::2    | /64          | 2001:1010:0:1000::1 | FE80::1403/10 |
| VPC7       | eth0      | 2001:1010:0:2000::2    | /64          | 2001:1010:0:2000::1 | FE80::1403/10 |

Схема HSRP

| HSRP   |        |         |                 |
|--------|--------|---------|-----------------|
| Филиал | Москва |         |                 |
| Vlan   | Active | Standby | IPv6 link local |
| 30     | R12    | R13     | FE80::1230/10   |
| 111    | R12    | R13     | FE80::1211/10   |
| 121    | R13    | R12     | FE80::1321/10   |

Схема STP

![](https://github.com/svasornd/otus_network/blob/master/lab10/lab_10_Moscow_STP.png)

| STP | Priority  |
|-----|-----------|
| SW2 | 32769     |
| SW3 | 32769     |
| SW4 | 24577     |
| SW5 | 28673     |

[Конфигурация R12](https://github.com/svasornd/otus_network/blob/master/lab10/config/R12.md)

[Конфигурация R13](https://github.com/svasornd/otus_network/blob/master/lab10/config/R13.md)

[Конфигурация R14](https://github.com/svasornd/otus_network/blob/master/lab10/config/R14.md)

[Конфигурация R15](https://github.com/svasornd/otus_network/blob/master/lab10/config/R15.md)

[Конфигурация R19](https://github.com/svasornd/otus_network/blob/master/lab10/config/R19.md)

[Конфигурация R20](https://github.com/svasornd/otus_network/blob/master/lab10/config/R20.md)

[Конфигурация SW2](https://github.com/svasornd/otus_network/blob/master/lab10/config/SW2.md)

[Конфигурация SW3](https://github.com/svasornd/otus_network/blob/master/lab10/config/SW3.md)

[Конфигурация SW4](https://github.com/svasornd/otus_network/blob/master/lab10/config/SW4.md)

[Конфигурация SW5](https://github.com/svasornd/otus_network/blob/master/lab10/config/SW5.md)

R14

SW3

    interface Vlan30
      ipv6 address 2001:1010::3/64
    ipv6 route ::/0 2001:1010::1 name V30HSRP

SW2

    interface Vlan30
      ipv6 address 2001:1010::2/64
    ipv6 route ::/0 2001:1010::1 name V30HSRP
 
SW4
 
    interface Vlan30
     ipv6 address 2001:1010::4/64
    ipv6 route ::/0 2001:1010::1 name V30HSRP

SW5

    interface Vlan30
     ipv6 address 2001:1010::5/64
    ipv6 route ::/0 2001:1010::1 name V30HSRP
    
R12

    ipv6 unicast-routing

    interface Loopback1
      ip address 10.10.0.12 255.255.255.255
      ipv6 address FE80::1200 link-local
      ipv6 address 2001:1010:1000::12/128
      ipv6 ospf 10 area 10

    interface Ethernet0/0.30
      standby 61 ipv6 FE80::1230
      standby 61 priority 150
      standby 61 preempt
      ipv6 address FE80::1200 link-local
      ipv6 address 2001:1010::1000/64
      ipv6 enable
      ipv6 ospf 10 area 10

    interface Ethernet0/0.111
      standby 62 ipv6 FE80::1211
      standby 62 priority 150
      standby 62 preempt
      ipv6 address FE80::1200 link-local
      ipv6 address 2001:1010:0:1000::1000/64
      ipv6 enable
      ipv6 ospf 10 area 10

    interface Ethernet0/0.121
      standby 63 ipv6 FE80::1321
      ipv6 address FE80::1200 link-local
      ipv6 address 2001:1010:0:2000::1000/64
      ipv6 enable
      ipv6 ospf 10 area 10

    interface Ethernet0/2
      ipv6 address FE80::1200 link-local
      ipv6 address 2001:1010:0:2::2/64
      ipv6 ospf 10 area 10

    interface Ethernet0/3
      ipv6 address FE80::1200 link-local
      ipv6 address 2001:1010:0:4::2/64
      ipv6 ospf 10 area 10

    ipv6 router ospf 10
      router-id 10.10.0.12
      
R13

    ipv6 unicast-routing

    interface Loopback1
      ipv6 address FE80::1300 link-local
      ipv6 address 2001:1010:1000::13/128
      ipv6 ospf 10 area 10

    interface Ethernet0/0.30
      standby 61 ipv6 FE80::1230
      ipv6 address FE80::1300 link-local
      ipv6 address 2001:1010::1001/64
      ipv6 enable
      ipv6 ospf 10 area 10

    interface Ethernet0/0.111
      standby 62 ipv6 FE80::1211
      ipv6 address FE80::1300 link-local
      ipv6 address 2001:1010:0:1000::1001/64
      ipv6 enable
      ipv6 ospf 10 area 10

    interface Ethernet0/0.121
      standby 63 ipv6 FE80::1321
      standby 63 priority 150
      standby 63 preempt
      ipv6 address FE80::1300 link-local
      ipv6 address 2001:1010:0:2000::1001/64
      ipv6 enable
      ipv6 ospf 10 area 10

    interface Ethernet0/2
      ip address 10.10.15.18 255.255.255.252
      ipv6 address FE80::1300 link-local
      ipv6 address 2001:1010:0:5::2/64
      ipv6 ospf 10 area 10

    interface Ethernet0/3
      ip address 10.10.15.10 255.255.255.252
      ipv6 address FE80::1300 link-local
      ipv6 address 2001:1010:0:3::2/64
      ipv6 ospf 10 area 10

    ipv6 router ospf 10
      router-id 10.10.0.13

R14

    ipv6 unicast-routing

    interface Loopback1
      ipv6 address FE80::1400 link-local
      ipv6 address 2001:1010:1000::14/128
      ipv6 ospf 10 area 0

    interface Ethernet0/0
      ipv6 address FE80::1400 link-local
      ipv6 address 2001:1010:0:2::1/64
      ipv6 ospf 10 area 10

    interface Ethernet0/1
      ipv6 address FE80::1400 link-local
      ipv6 address 2001:1010:0:3::1/64
      ipv6 ospf 10 area 10

    interface Ethernet0/3
      ipv6 address FE80::1400 link-local
      ipv6 address 2001:1010:0:1::1/64
      ipv6 ospf 10 area 101

    ipv6 router ospf 10
      router-id 10.10.0.14
      area 10 virtual-link 10.10.0.15
      area 101 stub no-summary
      default-information originate
      
R15

    ipv6 unicast-routing
    
    interface Loopback1
      ipv6 address FE80::1500 link-local
      ipv6 address 2001:1010:1000::15/128
      ipv6 ospf 10 area 0

    interface Ethernet0/0
      ipv6 address FE80::1500 link-local
      ipv6 address 2001:1010:0:5::1/64
      ipv6 ospf 10 area 10

    interface Ethernet0/1
      ipv6 address FE80::1500 link-local
      ipv6 address 2001:1010:0:4::1/64
      ipv6 ospf 10 area 10

    interface Ethernet0/3
      ipv6 address FE80::1500 link-local
      ipv6 address 2001:1010:0:6::1/64
      ipv6 ospf 10 area 102

    ipv6 router ospf 10
      router-id 10.10.0.15
      area 10 virtual-link 10.10.0.14
      default-information originate

 R19

    ipv6 unicast-routing  
    
    interface Loopback1
      ipv6 address FE80::1900 link-local
      ipv6 address 2001:1010:1000::19/128
      ipv6 ospf 10 area 101

    interface Ethernet0/0
      ipv6 address FE80::1900 link-local
      ipv6 address 2001:1010:0:1::2/64
      ipv6 ospf 10 area 101

    ipv6 router ospf 10
      router-id 10.10.0.19
      area 101 stub
      
 R20

    ipv6 unicast-routing  

    interface Loopback1
      ipv6 address FE80::2000 link-local
      ipv6 address 2001:1010:1000::20/128
      ipv6 ospf 10 area 102

    interface Ethernet0/0
      ipv6 address FE80::2000 link-local
      ipv6 address 2001:1010:0:6::2/64
      ipv6 ospf 10 area 102

    ipv6 router ospf 10
      router-id 10.10.0.19
      distribute-list prefix-list AREA101V6 in

    ipv6 prefix-list AREA101V6 seq 5 deny 2001:1010:1000::19/128
    ipv6 prefix-list AREA101V6 seq 10 deny 2001:1010:0:1::/64
    ipv6 prefix-list AREA101V6 seq 15 permit ::/0 le 128





          
          


