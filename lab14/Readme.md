### Основные протоколы сети интернет ####

Цель: Настроить DHCP в офисе Москва Настроить синхронизацию времени в офисе Москва Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001
2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042
3. Настроите статический NAT для R20
4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления
5. Настроите статический NAT(PAT) для офиса Чокурдах
6. Настроите DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP
7. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13
8. Все офисы в лабораторной работе должны иметь IP связность
9. План работы и изменения зафиксированы в документации 

![](https://github.com/svasornd/otus_network/blob/master/lab14/Lab14.png)


#### Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001 ####

Москва

R15

          ip nat inside source list 2 interface Loopback2 overload
          access-list 2 permit 10.10.0.0 0.0.255.255

          interface Loopback1
           ip nat inside
          interface Loopback2
           ip nat inside
          interface Ethernet0/0
           ip nat inside
          interface Ethernet0/1
           ip nat inside
          interface Ethernet0/2
           ip nat outside
          interface Ethernet0/3
           ip nat inside



#### Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042 ####

Санкт-Петербург

R18

          ip nat pool NAT-POOL-R18 25.0.0.1 25.0.0.5 prefix-length 24
          ip nat inside source list 2 pool NAT-POOL-R18
          access-list 2 permit 10.15.0.0 0.0.255.255
          interface Loopback1
           ip nat inside
          interface Loopback2
           ip nat inside
          interface Ethernet0/0
           ip nat inside
          interface Ethernet0/1
           ip nat inside
          interface Ethernet0/2
           ip nat outside
          interface Ethernet0/3
           ip nat outside


#### Настроите статический NAT для R20 ####

#### Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления ####


Москва 

R15

          ip nat inside source static tcp 10.10.0.19 23 20.0.0.19 23 extendable
          ip nat inside source static 10.10.0.20 20.0.0.20
      
Таблица NAT

          R15#sh ip nat translations
          Pro Inside global      Inside local       Outside local      Outside global
          tcp 20.0.0.19:23       10.10.0.19:23      ---                ---
          --- 20.0.0.20          10.10.0.20         ---                ---


#### Настроите статический NAT(PAT) для офиса Чокурдах ####

Чокурдах

R28

          ip nat inside source static tcp 10.20.3.2 2004 70.70.57.5 2004 extendable
          ip nat inside source static tcp 10.20.2.2 2004 70.70.58.76 2004 extendable

Таблица BGP

          r28#show ip nat translations
          Pro Inside global      Inside local       Outside local      Outside global
          tcp 70.70.58.76:2004   10.20.2.2:2004     ---                ---
          tcp 70.70.57.5:2004    10.20.3.2:2004     ---                ---

#### Настроите DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP ####

Москва 

R12

          ip dhcp excluded-address 10.10.11.253
          ip dhcp excluded-address 10.10.11.254
          ip dhcp excluded-address 10.10.11.1
          ip dhcp pool POOL-VLAN111
           network 10.10.11.0 255.255.255.0
           default-router 10.10.11.1
          ipv6 dhcp pool IPV6-VLAN111
           address prefix 2001:1010:0:1000::/64 lifetime infinite infinite
          interface Ethernet0/0.111
           ipv6 dhcp server IPV6-VLAN111

R13

          ip dhcp excluded-address 10.10.12.253
          ip dhcp excluded-address 10.10.12.254
          ip dhcp excluded-address 10.10.12.1
          ip dhcp pool POOL-VLAN12
           network 10.10.12.0 255.255.255.0
           default-router 10.10.12.1
          ipv6 dhcp pool IPV6-VLAN121
           address prefix 2001:1010:0:2000::/64 lifetime infinite infinite
          interface Ethernet0/0.121
           ipv6 dhcp server IPV6-VLAN121

VPC1

          VPCS> show ip
          NAME        : VPCS[1]
          IP/MASK     : 10.10.11.3/24
          GATEWAY     : 10.10.11.1
          DNS         :
          DHCP SERVER : 10.10.11.254
          DHCP LEASE  : 72909, 86400/43200/75600
          MAC         : 00:50:79:66:68:01
          LPORT       : 20000
          RHOST:PORT  : 127.0.0.1:30000
          MTU         : 1500

VPC7

          VPCS> show ip
          NAME        : VPCS[1]
          IP/MASK     : 10.10.12.3/24
          GATEWAY     : 10.10.12.1
          DNS         :
          DHCP SERVER : 10.10.12.253
          DHCP LEASE  : 86397, 86400/43200/75600
          MAC         : 00:50:79:66:68:07
          LPORT       : 20000
          RHOST:PORT  : 127.0.0.1:30000
          MTU         : 1500


#### Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13 ####

Москва 

R12

          ntp master 1
          ntp update-calendar
          interface Ethernet0/0.30
           ntp broadcast

SW3

          interface Vlan30
           ntp broadcast client

          SW3#show clock detail
          20:44:46.426 EET Thu Jul 2 2020
          Time source is NTP

R20

          ntp server 10.10.0.12
          ntp server 10.10.0.13

          R20#show clock detail
          20:46:16.232 EET Thu Jul 2 2020
          Time source is NTP

