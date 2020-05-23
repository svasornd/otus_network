### BGP. Основы ####

Цель: Настроить BGP между автономными системами Организовать доступность между офисами Москва и С.-Петербург

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас
2. Настроите eBGP между провайдерами Киторн и Ламас
3. Настроите eBGP между Ламас и Триада
4. eBGP между офисом С.-Петербург и провайдером Триада
5. Организуете IP доступность между офисами Москва и С.-Петербург
6. План работы и изменения зафиксированы в документации 

![](https://github.com/svasornd/otus_network/blob/master/lab11/Lab11_01.png)

#### Версия 1 ####

Москва 

R14

        router bgp 1001
            network 10.10.0.0 mask 255.255.240.0
            neighbor 50.50.23.1 remote-as 101
        ip route 10.10.0.0 255.255.240.0 Null0

Таблица маршрутов BGP

                      10.0.0.0/8 is variably subnetted, 20 subnets, 5 masks
                B        10.15.0.0/20 [20/0] via 50.50.23.1, 00:24:20
                      50.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
                B        50.50.0.0/16 [20/0] via 50.50.23.1, 00:24:50
                      60.0.0.0/16 is subnetted, 1 subnets
                B        60.60.0.0 [20/0] via 50.50.23.1, 00:24:50
                      70.0.0.0/16 is subnetted, 1 subnets
                B        70.70.0.0 [20/0] via 50.50.23.1, 00:24:50



R15

        router bgp 1001
            network 10.10.0.0 mask 255.255.240.0
            neighbor 60.60.154.1 remote-as 301
        ip route 10.10.0.0 255.255.240.0 Null0

Таблица маршрутов BGP

                      10.0.0.0/8 is variably subnetted, 20 subnets, 5 masks
                B        10.15.0.0/20 [20/0] via 60.60.154.1, 00:23:28
                      50.0.0.0/16 is subnetted, 1 subnets
                B        50.50.0.0 [20/0] via 60.60.154.1, 00:23:28
                      60.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
                B        60.60.0.0/16 [20/0] via 60.60.154.1, 00:23:58
                      70.0.0.0/16 is subnetted, 1 subnets
                B        70.70.0.0 [20/0] via 60.60.154.1, 00:23:28

Ping c VPS1 до R18 - Санкт - Петербург

                VPCS> ping 10.15.0.18

                84 bytes from 10.15.0.18 icmp_seq=1 ttl=251 time=1.917 ms
                84 bytes from 10.15.0.18 icmp_seq=2 ttl=251 time=2.078 ms
                84 bytes from 10.15.0.18 icmp_seq=3 ttl=251 time=1.901 ms

Санкт-Петербург 

R18

        router bgp 2042
            network 10.15.0.0 mask 255.255.240.0
            neighbor 70.70.44.1 remote-as 520
            neighbor 70.70.45.1 remote-as 520
        ip route 10.15.0.0 255.255.240.0 Null0

Таблица маршрутов BGP

                      10.0.0.0/8 is variably subnetted, 13 subnets, 5 masks
                B        10.10.0.0/20 [20/0] via 70.70.45.1, 00:14:39
                      50.0.0.0/16 is subnetted, 1 subnets
                B        50.50.0.0 [20/0] via 70.70.45.1, 00:14:39
                      60.0.0.0/16 is subnetted, 1 subnets
                B        60.60.0.0 [20/0] via 70.70.45.1, 00:15:10
                      70.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
                B        70.70.0.0/16 [20/0] via 70.70.45.1, 00:15:10

Ping c VPS8 до R14 R15 -Москва

                VPCS> ping 10.10.0.14

                84 bytes from 10.10.0.14 icmp_seq=1 ttl=254 time=1.744 ms
                84 bytes from 10.10.0.14 icmp_seq=2 ttl=254 time=1.627 ms
                84 bytes from 10.10.0.14 icmp_seq=3 ttl=254 time=1.939 ms

                VPCS> ping 10.10.0.15

                84 bytes from 10.10.0.15 icmp_seq=1 ttl=254 time=1.713 ms
                84 bytes from 10.10.0.15 icmp_seq=2 ttl=254 time=1.267 ms
                84 bytes from 10.10.0.15 icmp_seq=3 ttl=254 time=1.331 ms

#### Версия2 #####

Москва 

R14

        router bgp 1001
            network 10.10.0.14 mask 255.255.255.255
            neighbor 50.50.23.1 remote-as 101
       
Таблица маршрутов BGP

                      10.0.0.0/8 is variably subnetted, 20 subnets, 5 masks
                B        10.15.0.18/32 [20/0] via 50.50.23.1, 00:27:43
                      50.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
                B        50.50.0.0/16 [20/0] via 50.50.23.1, 01:16:29
                      60.0.0.0/16 is subnetted, 1 subnets
                B        60.60.0.0 [20/0] via 50.50.23.1, 01:16:29
                      70.0.0.0/16 is subnetted, 1 subnets
                B        70.70.0.0 [20/0] via 50.50.23.1, 00:03:49

Ping c R14(Москва) до  R18(Санкт-Петербург)

                R14#ping 10.15.0.18
                Type escape sequence to abort.
                Sending 5, 100-byte ICMP Echos to 10.15.0.18, timeout is 2 seconds:
                !!!!!
                Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

R15

        router bgp 1001
            network 10.10.0.15 mask 255.255.255.255
            neighbor 60.60.154.1 remote-as 301
        
Таблица маршрутов BGP
  
                      10.0.0.0/8 is variably subnetted, 20 subnets, 5 masks
                B        10.15.0.18/32 [20/0] via 60.60.154.1, 00:32:38
                      50.0.0.0/16 is subnetted, 1 subnets
                B        50.50.0.0 [20/0] via 60.60.154.1, 01:20:54
                      60.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
                B        60.60.0.0/16 [20/0] via 60.60.154.1, 01:21:24
                      70.0.0.0/16 is subnetted, 1 subnets
                B        70.70.0.0 [20/0] via 60.60.154.1, 01:20:54

        
Ping c R15(Москва) до  R18(Санкт-Петербург)

                R15#ping 10.15.0.18
                Type escape sequence to abort.
                Sending 5, 100-byte ICMP Echos to 10.15.0.18, timeout is 2 seconds:
                !!!!!
                Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms


Таблица маршрутов BGP

                      10.0.0.0/8 is variably subnetted, 20 subnets, 5 masks
                B        10.15.0.0/20 [20/0] via 60.60.154.1, 00:23:28
                      50.0.0.0/16 is subnetted, 1 subnets
                B        50.50.0.0 [20/0] via 60.60.154.1, 00:23:28
                      60.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
                B        60.60.0.0/16 [20/0] via 60.60.154.1, 00:23:58
                      70.0.0.0/16 is subnetted, 1 subnets
                B        70.70.0.0 [20/0] via 60.60.154.1, 00:23:28

Ping c VPS1 до R18 - Санкт - Петербург

                VPCS> ping 10.15.0.18

                84 bytes from 10.15.0.18 icmp_seq=1 ttl=251 time=1.917 ms
                84 bytes from 10.15.0.18 icmp_seq=2 ttl=251 time=2.078 ms
                84 bytes from 10.15.0.18 icmp_seq=3 ttl=251 time=1.901 ms

Санкт-Петербург 

R18

        router bgp 2042
            network 10.15.0.18 mask 255.255.255.255
            neighbor 70.70.44.1 remote-as 520
            neighbor 70.70.45.1 remote-as 520

Таблица маршрутов BGP

                      10.0.0.0/8 is variably subnetted, 14 subnets, 5 masks
                B        10.10.0.14/32 [20/0] via 70.70.45.1, 00:37:28
                B        10.10.0.15/32 [20/0] via 70.70.45.1, 00:38:31
                      50.0.0.0/16 is subnetted, 1 subnets
                B        50.50.0.0 [20/0] via 70.70.45.1, 01:24:39
                      60.0.0.0/16 is subnetted, 1 subnets
                B        60.60.0.0 [20/0] via 70.70.45.1, 01:25:10
                      70.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
                B        70.70.0.0/16 [20/0] via 70.70.45.1, 01:25:10




Ping c R18(Санкт-Петербург) до  R14 R15 (Москва)

                R18#ping 10.10.0.14
                Type escape sequence to abort.
                Sending 5, 100-byte ICMP Echos to 10.10.0.14, timeout is 2 seconds:
                !!!!!
                Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
                R18#ping 10.10.0.15
                Type escape sequence to abort.
                Sending 5, 100-byte ICMP Echos to 10.10.0.15, timeout is 2 seconds:
                !!!!!
                Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

Ламас

R21

        router bgp 101
            network 60.60.0.0 mask 255.255.0.0
            neighbor 50.50.23.145 remote-as 1001
            neighbor 100.10.10.1 remote-as 301
            neighbor 100.10.10.6 remote-as 520
        ip route 60.60.0.0 255.255.0.0 Null0

Киторн

R22

        router bgp 101
            bgp log-neighbor-changes
            network 50.50.0.0 mask 255.255.0.0
            neighbor 50.50.23.145 remote-as 1001
            neighbor 100.10.10.1 remote-as 301
            neighbor 100.10.10.6 remote-as 520
        ip route 50.50.0.0 255.255.0.0 Null0

Триада

R23

        router bgp 520
            neighbor 100.10.10.5 remote-as 101
        ip route 70.70.0.0 255.255.0.0 Null0

R24

        router bgp 520
            network 70.70.0.0 mask 255.255.0.0
            neighbor 70.70.45.2 remote-as 2042
            neighbor 100.10.10.10 remote-as 301
        ip route 70.70.0.0 255.255.0.0 Null0

R26

        router bgp 520
            network 70.70.0.0 mask 255.255.0.0
            neighbor 70.70.44.2 remote-as 2042
        ip route 70.70.0.0 255.255.0.0 Null0




[Конфигурация R14](https://github.com/svasornd/otus_network/blob/master/lab11/config/R14.md)

[Конфигурация R15](https://github.com/svasornd/otus_network/blob/master/lab11/config/R15.md)

[Конфигурация R22](https://github.com/svasornd/otus_network/blob/master/lab11/config/R22.md)

[Конфигурация R21](https://github.com/svasornd/otus_network/blob/master/lab11/config/R21.md)

[Конфигурация R23](https://github.com/svasornd/otus_network/blob/master/lab11/config/R23.md)

[Конфигурация R24](https://github.com/svasornd/otus_network/blob/master/lab11/config/R24.md)

[Конфигурация R26](https://github.com/svasornd/otus_network/blob/master/lab11/config/R26.md)

[Конфигурация R18](https://github.com/svasornd/otus_network/blob/master/lab11/config/R18.md)
