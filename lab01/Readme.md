### Лабораторная работа 01. Настройка расширенных сетей VLAN, VTP и DTP ###


![](https://github.com/svasornd/otus_network/blob/master/lab01/Lab01.png)



| Заголовок  | Интерфейс   | IP адрес  | Маска подсети  |
| ------------ | ------------ | ------------ | ------------ |
|  S1 | VLAN 99  | 192.168.99.1 | 255.255.255.0 |
|  S2 | VLAN 99  | 192.168.99.2 | 255.255.255.0  |
|  S3 | VLAN 99  | 192.168.99.3 | 255.255.255.0  |
|  PC-A | NIC | 192.168.10.1 | 255.255.255.0  |
|  PC-B | NIC | 192.168.20.1 | 255.255.255.0  |
|  PC-C | NIC | 192.168.10.2 | 255.255.255.0  |

#### Часть 1. Настройка VTP ####

Настройка коммутатора S1 - сервером VTP

*S2(config)#vtp domain CCNA*

*S2(config)#vtp mode server*

*S2(config)#vtp password cisco*

Настройка коммутатора S2 - клиентом VTP

*S1(config)#vtp domain CCNA*

*S1(config)#vtp mode client*

*S1(config)#vtp password cisco*

Настройка коммутатора S3 - клиентом VTP

*S1(config)#vtp domain CCNA*

*S1(config)#vtp mode client*

*S1(config)#vtp password cisco*

#### Часть 2. Настройка динамического протокола транкинга (DTP) ####

Вопрос 
Введите команду show interfaces f0/1 switchport на коммутаторах S1 и S2.
Какой административный и оперативный режим у коммутационного порта f0/1?

**Ответ**

*Administrative Mode: dynamic auto*
*Operational Mode: static access*

##### Настройка динамическго магистрального канала между S1 и S2 #####

*S1(config)# interface f0/1*

*S1(config-if)#switchport trunk encapsulation dot1q*

*S1(config-if)# switchport mode dynamic desirable*

##### Настройка статического магистрального канала между S1 и S3 #####

Коммутатор S1

*S1(config)# interface f0/3*

*S1(config-if)#switchport trunk encapsulation dot1q*

*S1(config-if)# switchport mode trunk*

Коммутатор S3

*S3(config)# interface f0/3*

*S3(config-if)#switchport trunk encapsulation dot1q*

*S3(config-if)# switchport mode trunk*

##### Настройка статического магистрального канала между S2 и S3 #####

Коммутатор S2

*S2(config)# interface f0/3*

*S2(config-if)#switchport trunk encapsulation dot1q*

*S2(config-if)# switchport mode trunk*

Коммутатор S3

*S3(config)# interface f0/1*

*S3(config-if)#switchport trunk encapsulation dot1q*

*S3(config-if)# switchport mode trunk*

#### Часть 3: Добавление сетей VLAN и назначение портов ####

**Вопрос**

На коммутаторе S1 добавьте сеть VLAN 10.

*S1(config)# vlan 10*

Удалось ли вам создать сеть VLAN 10 на коммутаторе S1?

**Ответ**

Нет создать VLAN 10 на коммутаторе не удалось, он находится в режиме клиента VTP. На клиенте VTP нельзя создавать, изменять и удалять VLAN.

Добавление VLAN на коммутатор S2.

*S2(config)# vlan 10*

*S2(config-vlan)# name Red*

*S2(config-vlan)# vlan 20*

*S2(config-vlan)# name Blue*

*S2(config-vlan)# vlan 30*

*S2(config-vlan)# name Yellow*

*S2(config-vlan)# vlan 99*

*S2(config-vlan)# name Management*

** Вопрос **
Какие команды show вы использовали для проверки обновлений VTP на коммутаторах S1 и S3?

** Ответ ** 

*S2#show vlan brief*

Для сравнения таблицы VLAN

S2#show vtp status

Для проверки даты обнвления конфигурации и номера ревизии

#### Назначение адресов коммутаторам и коммутаторам в разных VLAN ####

| Порты  | VLAN   | IP-адрес и префикс прикрепленного компьютера / коммутатора  |
| ------------ | ------------ | ------------ |
|  S1 F0/6 | VLAN 10  | 192.168.99.1 /24| 
|  S2 F0/18| VLAN 20  | 192.168.99.2 /24| 
|  S3 F0/18 | VLAN 10 | 192.168.99.3 /24| 
|  S1 | VLAN 99 | 192.168.99.1 /24|
|  S2 | VLAN 99 | 192.168.99.2 /24| 
|  S3 | VLAN 99 | 192.168.99.3 /24| 

Настройка портов коммутаторов

*S1(config)# interface f0/6*

*S1(config-if)# switchport mode access*

*S1(config-if)# switchport access vlan 10*

*S2(config)# interface f0/6*

*S2(config-if)# switchport mode access*

*S2(config-if)# switchport access vlan 20*

*S3(config)# interface f0/18*

*S3(config-if)# switchport mode access*

*S3(config-if)# switchport access vlan 10*

Настройка адресов на коммутаторах

*S1(config)# interface vlan 99*

*S1(config-if)# ip address 192.168.99.1 255.255.255.0*

*S1(config-fi)# no shutdown*

*S2(config)# interface vlan 99*

*S2(config-if)# ip address 192.168.99.2 255.255.255.0*

*S2(config-fi)# no shutdown*

*S3(config)# interface vlan 99*

*S3(config-if)# ip address 192.168.99.3 255.255.255.0*

*S3(config-fi)# no shutdown*

** Вопрос **
Отправьте ping-запрос с компьютера PC-B на PC-A и проверьте результат.

** Ответ **

Ошибка -  No gateway found

Компьютер-приемник находится в другой сети, но шлюза в настройках нет и программа не знает куда отправлять пакеты.

** Вопрос **
Отправьте ping-запрос с компьютера PC-A на PC-C и проверьте результат. 

** Ответ **

Все пакеты доставлены. Оба компьютера находтся в одной сети и в одном VLAN. 

** Вопрос **

Отправьте ping-запрос с коммутатора S1 на компьютер PC-A. Была ли проверка успешной? 

** Ответ **

Проверка не прошла. Причина ошибки не была указана. Компьютер приемник находится в другой сети и коммутатор не знет куда отправлять пакет, шлюз отсутсвует.

** Вопрос **

Отправьте ping-запрос с коммутатора S2 на коммутатор S1. Была ли проверка успешной?

** Ответ **

Все пакеты доставлены. Оба коммутатора находтся в одной сети и в одном VLAN, транки между ними настроены.

#### Настройка сети VLAN расширенного диапазона ####

Каковы преимущества и недостатки использования VTP?

Преимущества
1. Централизованное управление VLAN
2. Общая база VLAN на всех коммутаторах
3. Простота настройки
4. Простое добавление новых устройств в действующую конфигурацию.
5. Наличие резервных серверов.

Недостатки
1. Проприетарный протокол. Зависимость от cisco. 
2. Различные версии протокола с ограничениями. Например в версиии 3 можно настраивать VLAN из расширенного диапазона, но после этого перейти на версию 2 и 1 невозможно.



