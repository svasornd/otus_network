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

Москва 

R14

        router bgp 1001
            bgp router-id 20.0.0.14
            network 20.0.0.14 mask 255.255.255.255
            neighbor 50.50.23.1 remote-as 101
       
Таблица маршрутов BGP

                  25.0.0.0/20 is subnetted, 1 subnets
            B        25.0.0.0 [20/0] via 50.50.23.1, 00:02:01
                  50.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
            B        50.50.0.0/16 [20/0] via 50.50.23.1, 00:02:31
                  60.0.0.0/16 is subnetted, 1 subnets
            B        60.60.0.0 [20/0] via 50.50.23.1, 00:02:31
                  70.0.0.0/16 is subnetted, 1 subnets
            B        70.70.0.0 [20/0] via 50.50.23.1, 00:02:31


Ping c R14(Москва) до  R18(Санкт-Петербург)

            R14#ping 25.0.0.18
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 25.0.0.18, timeout is 2 seconds:
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

R15

        router bgp 1001
            bgp router-id 20.0.0.15
            network 20.0.0.0 mask 255.255.240.0
            neighbor 60.60.154.1 remote-as 301
        
Таблица маршрутов BGP
  
                  25.0.0.0/20 is subnetted, 1 subnets
            B        25.0.0.0 [20/0] via 60.60.154.1, 00:04:51
                  50.0.0.0/16 is subnetted, 1 subnets
            B        50.50.0.0 [20/0] via 60.60.154.1, 00:04:51
                  60.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
            B        60.60.0.0/16 [20/0] via 60.60.154.1, 00:05:22
                  70.0.0.0/16 is subnetted, 1 subnets
            B        70.70.0.0 [20/0] via 60.60.154.1, 00:05:22
       
Ping c R15(Москва) до  R18(Санкт-Петербург)

            R15#ping 25.0.0.18
            Type escape sequence to abort.
            Sending 5, 100-byte ICMP Echos to 25.0.0.18, timeout is 2 seconds:
            !!!!!
            Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

Санкт-Петербург 

R18

        router bgp 2042
            bgp router-id 25.0.0.18
            network 25.0.0.0 mask 255.255.240.0
            neighbor 70.70.44.1 remote-as 520
            neighbor 70.70.45.1 remote-as 520

Таблица маршрутов BGP

                  20.0.0.0/20 is subnetted, 1 subnets
            B        20.0.0.0 [20/0] via 70.70.45.1, 00:07:57
            B        20.0.0.14/32 [20/0] via 70.70.45.1, 00:08:03
                  50.0.0.0/16 is subnetted, 1 subnets
            B        50.50.0.0 [20/0] via 70.70.45.1, 00:07:27
                  60.0.0.0/16 is subnetted, 1 subnets
            B        60.60.0.0 [20/0] via 70.70.45.1, 00:07:57
                  70.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
            B        70.70.0.0/16 [20/0] via 70.70.45.1, 00:08:09

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

Ламас

R21

        router bgp 301
             bgp router-id 60.60.0.21
             network 60.60.0.0 mask 255.255.0.0
             neighbor 50.50.10.2 remote-as 101
             neighbor 60.60.154.75 remote-as 1001
             neighbor 70.70.10.9 remote-as 520

        ip route 60.60.0.0 255.255.0.0 Null0

Киторн

R22

        router bgp 101
             bgp router-id 50.50.0.22
             network 50.50.0.0 mask 255.255.0.0
             neighbor 50.50.10.1 remote-as 301
             neighbor 50.50.23.145 remote-as 1001
             neighbor 70.70.10.6 remote-as 520

        ip route 50.50.0.0 255.255.0.0 Null0

Триада

R24

        router bgp 520
            bgp router-id 70.70.0.24
            network 70.70.0.0 mask 255.255.0.0
            neighbor 70.70.45.2 remote-as 2042
            neighbor 70.70.10.10 remote-as 301
        ip route 70.70.0.0 255.255.0.0 Null0

[Конфигурация R14](https://github.com/svasornd/otus_network/blob/master/lab11/config/R14.md)

[Конфигурация R15](https://github.com/svasornd/otus_network/blob/master/lab11/config/R15.md)

[Конфигурация R22](https://github.com/svasornd/otus_network/blob/master/lab11/config/R22.md)

[Конфигурация R21](https://github.com/svasornd/otus_network/blob/master/lab11/config/R21.md)

[Конфигурация R24](https://github.com/svasornd/otus_network/blob/master/lab11/config/R24.md)

[Конфигурация R18](https://github.com/svasornd/otus_network/blob/master/lab11/config/R18.md)
