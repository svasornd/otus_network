### BGP. Основы ####

Цель: Настроить BGP между автономными системами Организовать доступность между офисами Москва и С.-Петербург

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас
2. Настроите eBGP между провайдерами Киторн и Ламас
3. Настроите eBGP между Ламас и Триада
4. eBGP между офисом С.-Петербург и провайдером Триада
5. Организуете IP доступность между офисами Москва и С.-Петербург
6. План работы и изменения зафиксированы в документации 

![](https://github.com/svasornd/otus_network/blob/master/lab11/Lab11.png)

Москва

R14

        router bgp 1001
            network 10.10.0.0 mask 255.255.240.0
            neighbor 50.50.23.1 remote-as 101
        ip route 10.10.0.0 255.255.240.0 Null0

R15

        router bgp 1001
            network 10.10.0.0 mask 255.255.240.0
            neighbor 60.60.154.1 remote-as 301
        ip route 10.10.0.0 255.255.240.0 Null0

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
            network 70.70.0.0 mask 255.255.0.0
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

Санкт-Петербург

R18

        router bgp 2042
            network 10.15.0.0 mask 255.255.240.0
            neighbor 70.70.44.1 remote-as 520
            neighbor 70.70.45.1 remote-as 520
        ip route 10.15.0.0 255.255.240.0 Null0


[Конфигурация R14](https://github.com/svasornd/otus_network/blob/master/lab11/config/R14.md)

[Конфигурация R15](https://github.com/svasornd/otus_network/blob/master/lab11/config/R15.md)

[Конфигурация R22](https://github.com/svasornd/otus_network/blob/master/lab11/config/R22.md)

[Конфигурация R21](https://github.com/svasornd/otus_network/blob/master/lab11/config/R21.md)

[Конфигурация R23](https://github.com/svasornd/otus_network/blob/master/lab11/config/R23.md)

[Конфигурация R24](https://github.com/svasornd/otus_network/blob/master/lab11/config/R24.md)

[Конфигурация R26](https://github.com/svasornd/otus_network/blob/master/lab11/config/R26.md)

[Конфигурация R18](https://github.com/svasornd/otus_network/blob/master/lab11/config/R18.md)
