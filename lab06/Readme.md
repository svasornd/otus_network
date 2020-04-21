## Лабораторная работа. Базовая настройка протокола EIGRP для IPv4 ##

![](https://github.com/svasornd/otus_network/blob/master/lab06/Lab06.png)

| Устройство | Интерфейс | IP адрес | Маска подсети | Шлюз по умолчанию |
| ---------- | ------------| ----------| ------------ | ------------ | 
|  R1 | e0/0 | 192.168.1.1 | 255.255.255.0 | - |
|  R1 | s1/0 (DCE) | 10.1.1.1 | 255.255.255.252 | - |
|  R1 | s1/2 | 10.3.3.1 | 255.255.255.252 | - |
|  R2 | e0/0 | 192.168.2.1 | 255.255.255.0 | - |
|  R2 | s1/0 | 10.1.1.2 | 255.255.255.252 | - |
|  R2 | s1/1 (DCE) | 10.2.2.2| 255.255.255.252 | - |
|  R3 | e0/0  | 192.168.3.1 | 255.255.255.0 | - |
|  R3 | s1/2  | 10.3.3.2| 255.255.255.252 | - |
|  R3 | s1/1  | 10.2.2.1| 255.255.255.252 | - |
|  PC-A | NIC  | 192.168.1.3 | 255.255.255.0 | 192.168.1.1 |
|  PC-A | NIC  | 192.168.2.3 | 255.255.255.0 | 192.168.2.1 |
|  PC-A | NIC  | 192.168.3.3 | 255.255.255.0 | 192.168.3.1 |

[Конфигурация R1](https://github.com/svasornd/otus_network/blob/master/lab06/config/R1.md)

[Конфигурация R2](https://github.com/svasornd/otus_network/blob/master/lab06/config/R2.md) 

[Конфигурация R3](https://github.com/svasornd/otus_network/blob/master/lab06/config/R3.md)


#####  Почему рекомендуется использовать шаблонные маски при объявлении сетей? Можно ли исключить маску в какой-нибудь из вышеприведённых инструкций network? 
Если да, то в какой (в каких)? #####  

Чтобы использовать только адреса с интерфесов попадающие в это диапазон и не анонсировать классовые сети. Можно исключить 

*network 192.168.1.0 0.0.0.255*

При команде 

*network 192.168.1.0*

будет использованна сеть класса C  с маской /24, т.е. полностью совпадающая с шаблонной маской.

#####  Почему у маршрутизатора R1 два пути к сети 10.2.2.0/30? #####  

Ему приходят 2 маршрута к сети 10.2.2.0/30.  Один через интерфейс Serial1/2, второй через Serial1/0 с равной суммарной метрикой.

        D        10.2.2.0/30 [90/2681856] via 10.3.3.2, 00:03:06, Serial1/2
                             [90/2681856] via 10.1.1.2, 00:03:06, Serial1/0

#####  Почему в таблице топологии маршрутизатора R1 отсутствуют возможные преемники? #####  

Каждый маршрут до пути назначения имеет одинаковую суммарную метрику и поэтому оба маршрутизатора R2 и R3 являются заместителями. 

#####  Какой номер автономной системы используется?  #####  

        EIGRP-IPv4 Protocol for AS(10)

#####  Какие сети объявляются? #####  

        Routing for Networks:
            10.1.1.0/30
            10.3.3.0/30
            192.168.1.0

#####  Каково значение административной дистанции для маршрутов EIGRP?  #####  

Для внутренних 90 - внешних 170

          Distance: internal 90 external 170


#####  Сколько маршрутов с равной стоимостью по умолчанию использует EIGRP? #####  


         Maximum path: 4

#####  Какова пропускная способность по умолчанию для этого последовательного интерфейса?  #####  

BW 1544 Kbit/sec

        Serial1/0 is up, line protocol is up
          Hardware is M4T
          Internet address is 10.1.1.1/30
          MTU 1500 bytes, BW 1544 Kbit/sec, DLY 20000 usec,
             reliability 255/255, txload 1/255, rxload 1/255
          Encapsulation HDLC, crc 16, loopback not set
          Keepalive set (10 sec)
          Restart-Delay is 0 secs
          Last input 00:00:01, output 00:00:02, output hang never
          Last clearing of "show interface" counters 06:10:36
          Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
          Queueing strategy: fifo
          Output queue: 0/40 (size/max)
          5 minute input rate 0 bits/sec, 0 packets/sec
          5 minute output rate 0 bits/sec, 0 packets/sec
             2915 packets input, 212759 bytes, 0 no buffer
             Received 2573 broadcasts (0 IP multicasts)
             0 runts, 0 giants, 0 throttles
             0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
             3029 packets output, 219850 bytes, 0 underruns
             0 output errors, 0 collisions, 3 interface resets
             0 unknown protocol drops
             0 output buffer failures, 0 output buffers swapped out
             3 carrier transitions     DCD=up  DSR=up  DTR=up  RTS=up  CTS=up


#####   Сколько маршрутов к сети 10.2.2.0/30 содержит таблица маршрутизации? #####  

Два маршрута

        D        10.2.2.0/30 [90/2681856] via 10.3.3.2, 00:25:45, Serial1/2
                             [90/2681856] via 10.1.1.2, 00:25:45, Serial1/0

#####  Выполните на маршрутизаторе R1 команду show ip route. Появились ли изменения в таблице маршрутизации? Если да, в чем они заключаются? #####  

До сети 10.2.2.0/30 остался толкьо 1 действующий маршрут

        10.2.2.0/30 [90/2681856] via 10.1.1.2, 00:00:14, Serial1/0

#####  Исходя из заданной пропускной способности, попробуйте определить, как будут выглядеть таблицы маршрутизации маршрутизаторов R2 и R3 до выполнения команды show ip route. 
Останутся ли их таблицы маршрутизации прежними или изменятся? #####  

Для R2 таблица маршрутизации не измениится.

Для R3 все марштуруты будут через R2 , так их стоимость будт наименьшей. 

#####  При выполнении лабораторной работы можно было ограничиться только статической маршрутизацией. 
Каковы преимущества использования EIGRP? #####  

Легко задать маршрут прохождения трафика, при использовании статической маршрутизации на каждом роутере надо было прописать 6 маршрутов на каждом роутере. И в ручную указать метрику для выбора необходимого маршрута. 
Так же более просто в добавлении еше одной сети. Если надо её добавить, достаточно добавить только на одном маршрутизаторе и он сообщит остальным.



