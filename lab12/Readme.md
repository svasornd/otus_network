### BGP. IBGP ####

Цель: Настроить iBGP в офисе Москва Настроить iBGP в сети провайдера Триада Организовать полную IP связанность всех сетей

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. iBGP в офисом Москва между маршрутизаторами R14 и R15
2. Настроите iBGP в провайдере Триада
3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
4. В офисе С.-Петербург работает протокол iBGP. (Не использовать протокол OSPF)
5. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно
6. Все сети в лабораторной работе должны иметь IP связность
7. План работы и изменения зафиксированы в документации 

![](https://github.com/svasornd/otus_network/blob/master/lab12/Lab12.png)

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

            route-map LAMAS-AS-PREPEND permit 10
             set as-path prepend 1001 1001 1001
      
Таблица BGP

             * i 20.0.0.0/20      10.10.0.15               0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             *>i 25.0.0.0/20      10.10.0.15               0    200      0 301 520 2042 i
             *                    50.50.23.1                             0 101 520 2042 i
             *>i 50.50.0.0/16     10.10.0.15               0    200      0 301 101 i
             *                    50.50.23.1               0             0 101 i
             *   60.60.0.0/16     50.50.23.1                             0 101 301 i
             *>i                  10.10.0.15               0    200      0 301 i
             *   70.70.0.0/16     50.50.23.1                             0 101 520 i
             *>i                  10.10.0.15               0    200      0 301 520 i

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

            route-map LAMAS permit 10
             set local-preference 200
        
Таблица BGP
  
             * i 20.0.0.0/20      10.10.0.14               0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             *>  25.0.0.0/20      60.60.154.1                   200      0 301 520 2042 i
             *>  50.50.0.0/16     60.60.154.1                   200      0 301 101 i
             *>  60.60.0.0/16     60.60.154.1              0    200      0 301 i
             *>  70.70.0.0/16     60.60.154.1                   200      0 301 520 i
       
Ping c R15(Москва) до  R18(Санкт-Петербург)

            R15#ping 25.0.0.18
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 25.0.0.18, timeout is 2 seconds:
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

R12

            router bgp 1001
             bgp log-neighbor-changes
             neighbor 10.10.0.14 remote-as 1001
             neighbor 10.10.0.14 update-source Loopback1
             neighbor 10.10.0.14 next-hop-self
             neighbor 10.10.0.15 remote-as 1001
             neighbor 10.10.0.15 update-source Loopback1
             neighbor 10.10.0.15 next-hop-self

R13

            router bgp 1001
             bgp log-neighbor-changes
             neighbor 10.10.0.14 remote-as 1001
             neighbor 10.10.0.14 update-source Loopback1
             neighbor 10.10.0.14 next-hop-self
             neighbor 10.10.0.15 remote-as 1001
             neighbor 10.10.0.15 update-source Loopback1
             neighbor 10.10.0.15 next-hop-self

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
             neighbor 70.70.44.1 distribute-list NO-iBGP out
             neighbor 70.70.45.1 remote-as 520
             neighbor 70.70.45.1 distribute-list NO-iBGP out
             maximum-paths 2

             ip access-list standard NO-iBGP
             deny   10.15.0.0 0.0.15.255
             permit any

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

R17

            router bgp 2042
             bgp log-neighbor-changes
             network 10.15.0.17 mask 255.255.255.255
             network 10.15.10.0 mask 255.255.255.128
             network 10.15.11.0 mask 255.255.255.0
             network 10.15.12.0 mask 255.255.255.0
             neighbor 10.15.10.125 remote-as 2042
             neighbor 10.15.15.1 remote-as 2042

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>i 10.15.0.16/32    10.15.10.125             0    100      0 i
             *>  10.15.0.17/32    0.0.0.0                  0         32768 i
             * i 10.15.0.18/32    10.15.15.5               0    100      0 i
             *>i                  10.15.15.1               0    100      0 i
             *>i 10.15.0.32/32    10.15.15.10              0    100      0 i
             * i 10.15.10.0/25    10.15.10.125             0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             * i 10.15.11.0/24    10.15.10.125             0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             * i 10.15.12.0/24    10.15.10.125             0    100      0 i
             *>                   0.0.0.0                  0         32768 i
            r i 10.15.15.0/30    10.15.15.5               0    100      0 i
             r>i                  10.15.15.1               0    100      0 i
             * i 10.15.15.4/30    10.15.15.1               0    100      0 i
             *>i                  10.15.10.125             0    100      0 i
             *>i 10.15.15.8/30    10.15.10.125             0    100      0 i
             * i 20.0.0.0/20      10.15.15.5               0    100      0 520 301 1001 i
             *>i                  10.15.15.1               0    100      0 520 301 1001 i
             * i 25.0.0.0/20      10.15.15.5               0    100      0 i
             *>i                  10.15.15.1               0    100      0 i
             * i 50.50.0.0/16     10.15.15.5               0    100      0 520 101 i
             *>i                  10.15.15.1               0    100      0 520 101 i
             * i 60.60.0.0/16     10.15.15.5               0    100      0 520 301 i
            *>i                  10.15.15.1               0    100      0 520 301 i
             * i 70.70.0.0/16     10.15.15.5               0    100      0 520 i
            *>i                  10.15.15.1               0    100      0 520 i

R16

            router bgp 2042
             bgp log-neighbor-changes
             network 10.15.0.16 mask 255.255.255.255
             network 10.15.10.0 mask 255.255.255.128
             network 10.15.11.0 mask 255.255.255.0
             network 10.15.12.0 mask 255.255.255.0
             network 10.15.15.4 mask 255.255.255.252
             network 10.15.15.8 mask 255.255.255.252
             neighbor 10.15.10.126 remote-as 2042
             neighbor 10.15.10.126 route-reflector-client
             neighbor 10.15.15.5 remote-as 2042
             neighbor 10.15.15.5 route-reflector-client
             neighbor 10.15.15.5 next-hop-self
             neighbor 10.15.15.10 remote-as 2042
             neighbor 10.15.15.10 route-reflector-client
             neighbor 10.15.15.10 next-hop-self

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>  10.15.0.16/32    0.0.0.0                  0         32768 i
             *>i 10.15.0.17/32    10.15.10.126             0    100      0 i
             *>i 10.15.0.18/32    10.15.15.5               0    100      0 i
             *>i 10.15.0.32/32    10.15.15.10              0    100      0 i
             * i 10.15.10.0/25    10.15.10.126             0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             * i 10.15.11.0/24    10.15.10.126             0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             * i 10.15.12.0/24    10.15.10.126             0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             *>i 10.15.15.0/30    10.15.15.5               0    100      0 i
             * i 10.15.15.4/30    10.15.15.5               0    100      0 i
             *>                   0.0.0.0                  0         32768 i
             *>  10.15.15.8/30    0.0.0.0                  0         32768 i
             *>i 20.0.0.0/20      10.15.15.5               0    100      0 520 301 1001 i
             *>i 25.0.0.0/20      10.15.15.5               0    100      0 i
             *>i 50.50.0.0/16     10.15.15.5               0    100      0 520 101 i
             *>i 60.60.0.0/16     10.15.15.5               0    100      0 520 301 i
             *>i 70.70.0.0/16     10.15.15.5               0    100      0 520 i

R32

            router bgp 2042
             bgp log-neighbor-changes
             network 10.15.0.32 mask 255.255.255.255
             neighbor 10.15.15.9 remote-as 2042
             neighbor 10.15.15.9 next-hop-self

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>i 10.15.0.16/32    10.15.15.9               0    100      0 i
             *>i 10.15.0.17/32    10.15.10.126             0    100      0 i
             *>i 10.15.0.18/32    10.15.15.5               0    100      0 i
             *>  10.15.0.32/32    0.0.0.0                  0         32768 i
             *>i 10.15.10.0/25    10.15.15.9               0    100      0 i
             *>i 10.15.11.0/24    10.15.15.9               0    100      0 i
             *>i 10.15.12.0/24    10.15.15.9               0    100      0 i
             *>i 10.15.15.0/30    10.15.15.5               0    100      0 i
             *>i 10.15.15.4/30    10.15.15.9               0    100      0 i
             r>i 10.15.15.8/30    10.15.15.9               0    100      0 i
             *>i 20.0.0.0/20      10.15.15.5               0    100      0 520 301 1001 i
             *>i 25.0.0.0/20      10.15.15.5               0    100      0 i
             *>i 50.50.0.0/16     10.15.15.5               0    100      0 520 101 i
             *>i 60.60.0.0/16     10.15.15.5               0    100      0 520 301 i
             *>i 70.70.0.0/16     10.15.15.5               0    100      0 520 i

Ламас

R21

        router bgp 301
             bgp router-id 60.60.0.21
             network 60.60.0.0 mask 255.255.0.0
             neighbor 50.50.10.2 remote-as 101
             neighbor 60.60.154.75 remote-as 1001
             neighbor 70.70.10.9 remote-as 520

        ip route 60.60.0.0 255.255.0.0 Null0

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>  20.0.0.0/20      60.60.154.75             0             0 1001 i
             *>  25.0.0.0/20      70.70.10.9                             0 520 2042 i
             *                    50.50.10.2                             0 101 520 2042 i
             *   50.50.0.0/16     70.70.10.9                             0 520 101 i
             *>                   50.50.10.2               0             0 101 i
             *>  60.60.0.0/16     0.0.0.0                  0         32768 i
             *   70.70.0.0/16     50.50.10.2                             0 101 520 i
             *>                   70.70.10.9               0             0 520 i

Киторн

R22

        router bgp 101
             bgp router-id 50.50.0.22
             network 50.50.0.0 mask 255.255.0.0
             neighbor 50.50.10.1 remote-as 301
             neighbor 50.50.23.145 remote-as 1001
             neighbor 70.70.10.6 remote-as 520

        ip route 50.50.0.0 255.255.0.0 Null0

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *   20.0.0.0/20      70.70.10.6                             0 520 301 1001 i
             *>                   50.50.10.1                             0 301 1001 i
             *                    50.50.23.145             0             0 1001 1001 1001 1001 i
             *   25.0.0.0/20      50.50.23.145                           0 1001 1001 1001 1001 301 520 2042 i
             *                    50.50.10.1                             0 301 520 2042 i
             *>                   70.70.10.6                             0 520 2042 i
             *>  50.50.0.0/16     0.0.0.0                  0         32768 i
             *   60.60.0.0/16     70.70.10.6                             0 520 301 i
             *                    50.50.23.145                           0 1001 1001 1001 1001 301 i
             *>                   50.50.10.1               0             0 301 i
             *   70.70.0.0/16     50.50.23.145                           0 1001 1001 1001 1001 301 520 i
             *                    50.50.10.1                             0 301 520 i
             *>                   70.70.10.6               0             0 520 i

Триада

R23

            router bgp 520
             bgp router-id 70.70.0.23
             bgp log-neighbor-changes
             network 70.70.0.0 mask 255.255.0.0
             neighbor 70.70.0.24 remote-as 520
             neighbor 70.70.0.24 update-source Loopback1
             neighbor 70.70.0.24 next-hop-self
             neighbor 70.70.0.25 remote-as 520
             neighbor 70.70.0.25 update-source Loopback1
             neighbor 70.70.0.25 route-reflector-client
             neighbor 70.70.0.25 next-hop-self
             neighbor 70.70.10.5 remote-as 101

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>i 20.0.0.0/20      70.70.0.24               0    100      0 301 1001 i
             *                    70.70.10.5                             0 101 301 1001 i
             *>i 25.0.0.0/20      70.70.0.24               0    100      0 2042 i
             *>  50.50.0.0/16     70.70.10.5               0             0 101 i
             *>i 60.60.0.0/16     70.70.0.24               0    100      0 301 i
             *                    70.70.10.5                             0 101 301 i
             * i 70.70.0.0/16     70.70.0.24               0    100      0 i
             *>                   0.0.0.0                  0         32768 i

R24

            router bgp 520
             bgp router-id 70.70.0.24
             bgp log-neighbor-changes
             network 70.70.0.0 mask 255.255.0.0
             neighbor 70.70.0.23 remote-as 520
             neighbor 70.70.0.23 update-source Loopback1
             neighbor 70.70.0.23 next-hop-self
             neighbor 70.70.0.26 remote-as 520
             neighbor 70.70.0.26 update-source Loopback1
             neighbor 70.70.0.26 route-reflector-client
             neighbor 70.70.0.26 next-hop-self
             neighbor 70.70.10.10 remote-as 301
             neighbor 70.70.45.2 remote-as 2042

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>  20.0.0.0/20      70.70.10.10                            0 301 1001 i
             * i 25.0.0.0/20      70.70.0.26               0    100      0 2042 i
             *>                   70.70.45.2               0             0 2042 i
             *   50.50.0.0/16     70.70.10.10                            0 301 101 i
             *>i                  70.70.0.23               0    100      0 101 i
             *>  60.60.0.0/16     70.70.10.10              0             0 301 i
             * i 70.70.0.0/16     70.70.0.26               0    100      0 i
             * i                  70.70.0.23               0    100      0 i
            *>                   0.0.0.0                  0         32768 i

R25

            router bgp 520
             bgp router-id 70.70.0.25
             bgp log-neighbor-changes
             neighbor 70.70.0.23 remote-as 520
             neighbor 70.70.0.23 update-source Loopback1
             neighbor 70.70.0.23 next-hop-self
             neighbor 70.70.0.26 remote-as 520
             neighbor 70.70.0.26 update-source Loopback1
             neighbor 70.70.0.26 next-hop-self

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>i 20.0.0.0/20      70.70.0.24               0    100      0 301 1001 i
             * i 25.0.0.0/20      70.70.0.24               0    100      0 2042 i
             *>i                  70.70.0.26               0    100      0 2042 i
             *>i 50.50.0.0/16     70.70.0.23               0    100      0 101 i
             *>i 60.60.0.0/16     70.70.0.24               0    100      0 301 i
             *>i 70.70.0.0/16     70.70.0.23               0    100      0 i
             * i                  70.70.0.26               0    100      0 i

R26

            router bgp 520
             bgp router-id 70.70.0.26
             bgp log-neighbor-changes
             network 70.70.0.0 mask 255.255.0.0
             neighbor 70.70.0.24 remote-as 520
             neighbor 70.70.0.24 update-source Loopback1
             neighbor 70.70.0.24 next-hop-self
             neighbor 70.70.0.25 remote-as 520
             neighbor 70.70.0.25 next-hop-self
             neighbor 70.70.44.2 remote-as 2042

Таблица BGP

                 Network          Next Hop            Metric LocPrf Weight Path
             *>i 20.0.0.0/20      70.70.0.24               0    100      0 301 1001 i
             * i 25.0.0.0/20      70.70.0.24               0    100      0 2042 i
             *>                   70.70.44.2               0             0 2042 i
             *>i 50.50.0.0/16     70.70.0.23               0    100      0 101 i
             *>i 60.60.0.0/16     70.70.0.24               0    100      0 301 i
             * i 70.70.0.0/16     70.70.0.24               0    100      0 i
             *>                   0.0.0.0                  0         32768 i


[Конфигурация R12](https://github.com/svasornd/otus_network/blob/master/lab12/config/R12.md)

[Конфигурация R13](https://github.com/svasornd/otus_network/blob/master/lab12/config/R13.md)

[Конфигурация R14](https://github.com/svasornd/otus_network/blob/master/lab12/config/R14.md)

[Конфигурация R15](https://github.com/svasornd/otus_network/blob/master/lab12/config/R15.md)

[Конфигурация R22](https://github.com/svasornd/otus_network/blob/master/lab12/config/R22.md)

[Конфигурация R21](https://github.com/svasornd/otus_network/blob/master/lab12/config/R21.md)

[Конфигурация R23](https://github.com/svasornd/otus_network/blob/master/lab12/config/R23.md)

[Конфигурация R24](https://github.com/svasornd/otus_network/blob/master/lab12/config/R24.md)

[Конфигурация R25](https://github.com/svasornd/otus_network/blob/master/lab12/config/R25.md)

[Конфигурация R26](https://github.com/svasornd/otus_network/blob/master/lab12/config/R26.md)

[Конфигурация R16](https://github.com/svasornd/otus_network/blob/master/lab12/config/R16.md)

[Конфигурация R17](https://github.com/svasornd/otus_network/blob/master/lab12/config/R17.md)

[Конфигурация R18](https://github.com/svasornd/otus_network/blob/master/lab12/config/R18.md)

[Конфигурация R32](https://github.com/svasornd/otus_network/blob/master/lab12/config/R32.md)
