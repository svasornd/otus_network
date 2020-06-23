### BGP. Фильтрация ####

Цель: Настроить iBGP в офисе Москва Настроить iBGP в сети провайдера Триада Организовать полную IP связанность всех сетей

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path)
2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list)
3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию
4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург
5. Все сети в лабораторной работе должны иметь IP связность
6. План работы и изменения зафиксированы в документации 

![](https://github.com/svasornd/otus_network/blob/master/lab13/Lab13.png)


#### Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию ####

Киторн

R22

        router bgp 101
             bgp router-id 50.50.0.22
             network 50.50.0.0 mask 255.255.0.0
             neighbor 50.50.10.1 remote-as 301
             neighbor 50.50.23.145 remote-as 1001
             neighbor 50.50.23.145 default-originate
             neighbor 50.50.23.145 filter-list 1 out
             neighbor 70.70.10.6 remote-as 520
             

        ip route 50.50.0.0 255.255.0.0 Null0



#### Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург ####
 

Ламас

R21

        router bgp 301
             bgp router-id 60.60.0.21
             network 60.60.0.0 mask 255.255.0.0
             neighbor 50.50.10.2 remote-as 101
             neighbor 60.60.154.75 remote-as 1001
             neighbor 60.60.154.75 default-originate
             neighbor 60.60.154.75 distribute-list DEF_ROUT_SPB out
             neighbor 70.70.10.9 remote-as 520

        ip access-list standard DEF_ROUT_SPB
             permit 25.0.0.0 0.0.240.255
             deny   any
       
        ip route 60.60.0.0 255.255.0.0 Null0

#### Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path) ####

Москва 

R14

            router bgp 1001
             bgp router-id 20.0.0.14
             bgp log-neighbor-changes
             network 20.0.0.0 mask 255.255.240.0
             neighbor 10.10.0.12 remote-as 1001
             neighbor 10.10.0.12 update-source Loopback1
             neighbor 10.10.0.12 next-hop-self
             neighbor 10.10.0.13 remote-as 1001
             neighbor 10.10.0.13 update-source Loopback1
             neighbor 10.10.0.13 next-hop-self
             neighbor 10.10.0.15 remote-as 1001
             neighbor 10.10.0.15 update-source Loopback1
             neighbor 10.10.0.15 next-hop-self
             neighbor 50.50.23.1 remote-as 101
             neighbor 50.50.23.1 route-map LAMAS-AS-PREPEND out
             neighbor 50.50.23.1 filter-list 1 out

            route-map LAMAS-AS-PREPEND permit 10
             set as-path prepend 1001 1001 1001

            ip as-path access-list 1 permit ^$
      
Таблица BGP

             r>i 0.0.0.0          10.10.0.15               0    200      0 301 i
             r                    50.50.23.1                             0 101 i
             * i 20.0.0.0/20      10.10.0.15               0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             *>i 25.0.0.0/20      10.10.0.15               0    200      0 301 520 2042 i

Ping c R15(Москва) до  R18(Санкт-Петербург)

            R14#ping 25.0.0.18 source loopback 2
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 25.0.0.18, timeout is 2 seconds:
            Packet sent with a source address of 20.0.0.14
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

R15

            router bgp 1001
             bgp router-id 20.0.0.15
             bgp log-neighbor-changes
             network 20.0.0.0 mask 255.255.240.0
             neighbor 10.10.0.12 remote-as 1001
             neighbor 10.10.0.12 update-source Loopback1
             neighbor 10.10.0.12 next-hop-self
             neighbor 10.10.0.14 remote-as 1001
             neighbor 10.10.0.14 update-source Loopback1
             neighbor 10.10.0.14 next-hop-self
             neighbor 20.0.0.13 remote-as 1001
             neighbor 20.0.0.13 update-source Loopback2
             neighbor 20.0.0.13 next-hop-self
             neighbor 60.60.154.1 remote-as 301
             neighbor 60.60.154.1 route-map LAMAS in
             neighbor 60.60.154.1 filter-list 1 out


            route-map LAMAS permit 10
             set local-preference 200

            ip as-path access-list 1 permit ^$
        
Таблица BGP
  
             r>  0.0.0.0          60.60.154.1                   200      0 301 i
             * i 20.0.0.0/20      10.10.0.14               0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             *>  25.0.0.0/20      60.60.154.1                   200      0 301 520 2042 i

       
Ping c R15(Москва) до  R18(Санкт-Петербург)

            R15#ping 25.0.0.18
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 25.0.0.18, timeout is 2 seconds:
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

#### Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list) ####

Санкт-Петербург 

R18

            router bgp 2042
             bgp router-id 25.0.0.18
             bgp log-neighbor-changes
             bgp bestpath as-path multipath-relax
             network 10.15.0.18 mask 255.255.255.255
             network 10.15.15.0 mask 255.255.255.252
             network 10.15.15.4 mask 255.255.255.252
             network 25.0.0.0 mask 255.255.240.0
             neighbor 10.15.15.2 remote-as 2042
             neighbor 10.15.15.6 remote-as 2042
             neighbor 10.15.15.6 next-hop-self
             neighbor 70.70.44.1 remote-as 520
             neighbor 70.70.44.1 prefix-list PREF-OUT-1 out
             neighbor 70.70.45.1 remote-as 520
             neighbor 70.70.45.1 prefix-list PREF-OUT-1 out

             maximum-paths 2

            ip prefix-list PREF-OUT-1 seq 10 permit 25.0.0.0/20
            ip prefix-list PREF-OUT-1 seq 20 deny 10.15.0.0/20



Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>i 10.15.0.16/32    10.15.15.6               0    100      0 i
             * i 10.15.0.17/32    10.15.10.126             0    100      0 i
             *>i                  10.15.15.2               0    100      0 i
             *>  10.15.0.18/32    0.0.0.0                  0         32768 i
             *>i 10.15.0.32/32    10.15.15.10              0    100      0 i
             *>i 10.15.10.0/25    10.15.15.6               0    100      0 i
             * i                  10.15.15.2               0    100      0 i
             *>i 10.15.11.0/24    10.15.15.6               0    100      0 i
             * i                  10.15.15.2               0    100      0 i
             *>i 10.15.12.0/24    10.15.15.6               0    100      0 i
             * i                  10.15.15.2               0    100      0 i
             *>  10.15.15.0/30    0.0.0.0                  0         32768 i
             *>  10.15.15.4/30    0.0.0.0                  0         32768 i
             * i                  10.15.15.6               0    100      0 i
             *>i 10.15.15.8/30    10.15.15.6               0    100      0 i
             *m  20.0.0.0/20      70.70.44.1                             0 520 301 1001 i
             *>                   70.70.45.1                             0 520 301 1001 i
            *>  25.0.0.0/20      0.0.0.0                  0         32768 i
             *m  50.50.0.0/16     70.70.44.1                             0 520 101 i
             *>                   70.70.45.1                             0 520 101 i
            *m  60.60.0.0/16     70.70.44.1                             0 520 301 i
             *>                   70.70.45.1                             0 520 301 i
             *>  70.70.0.0/16     70.70.45.1               0             0 520 i
             *m                   70.70.44.1               0             0 520 i

Ping c R18(Санкт-Петербург) до  R14 R15 (Москва)

            R18#ping 20.0.0.14
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 20.0.0.14, timeout is 2 seconds:
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

            R18#ping 20.0.0.15
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 20.0.0.15, timeout is 2 seconds:
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms



[Конфигурация R14](https://github.com/svasornd/otus_network/blob/master/lab13/config/R14.md)

[Конфигурация R15](https://github.com/svasornd/otus_network/blob/master/lab13/config/R15.md)

[Конфигурация R22](https://github.com/svasornd/otus_network/blob/master/lab13/config/R22.md)

[Конфигурация R21](https://github.com/svasornd/otus_network/blob/master/lab13/config/R21.md)

[Конфигурация R18](https://github.com/svasornd/otus_network/blob/master/lab13/config/R18.md)


