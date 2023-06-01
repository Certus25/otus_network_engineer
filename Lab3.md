# Лабораторная работа: реализация DHCPv4
# Задание: 
- Часть 1. Построение сети и настройка        основных параметров устройства
- Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
- Часть 3. Настройка и проверка DHCP-ретранслятора на R2
# Предпосылки / сценарий
Протокол динамической настройки хоста (DHCP) - это сетевой протокол, который позволяет сетевым администраторам управлять и автоматизировать назначение IP-адресов. Без DHCP для IPv4 администратор должен вручную назначать и настраивать IP-адреса, предпочтительные DNS-серверы и шлюзы по умолчанию. По мере увеличения размера сети это становится административной проблемой при перемещении устройств из одной внутренней сети в другую.

В этом сценарии компания увеличилась в размерах, и сетевые администраторы больше не могут назначать IP-адреса устройствам вручную. Ваша задача - настроить маршрутизатор R1 для назначения IPv4-адресов в двух разных подсетях.

Примечание: Маршрутизаторами, используемыми в практических лабораториях CCNA, являются Cisco 4221 с Cisco IOS XE версии 16.9.4 (образ universalk9). В лабораториях используются коммутаторы Cisco Catalyst 2960s с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели и версии Cisco IOS доступные команды и создаваемый результат могут отличаться от того, что показано в лабораториях. Обратитесь к сводной таблице интерфейса маршрутизатора в конце лабораторной работы для получения правильных идентификаторов интерфейса.

Примечание: Убедитесь, что маршрутизаторы и коммутаторы были удалены и не имеют конфигураций запуска. Если вы не уверены, обратитесь к своему преподавателю.

Необходимые ресурсы
2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или сопоставимым)

2 коммутатора (Cisco 2960 с образцом Cisco IOS версии 15.2 (2) lanbasek9 или сопоставимым)

2 компьютера (Windows с программой эмуляции терминала, такой как Tera Term)

Консольные кабели для настройки устройств Cisco IOS через консольные порты

Кабели Ethernet, как показано в топологии

# Схема лабораторного стенда:
 ![](https://github.com/Certus25/otus_network_engineer/blob/5fd00014feb97c5d769cd16dad6b9ed96283e895/Shema-Lab3.png)
# Таблица адресации
 | Устройство |	Интерфейс |	IP-адрес |	Маска подсети |	Шлюз по умолчанию |
 | :----------|:----------:| :-----------:|:-------------:| -----------------:|
 | R1 |	G0/0/0 |	10.0.0.1 |	255.255.255.252 |	N/A |
 | |G0/ 0 / 1 |	N/A  |	N/A | N/A |
 ||G0/0/1.100 |	192.168.1.1 |	255.255.255.192 | N/A|
 || G0/0/1.200 |	192.168.1.65 |	255.255.255.224 | N/A |
 ||G0/0/1.1000 |	N/A |	N/A | N/A |
 | R2 |	G0/0/0 |	10.0.0.2 |	255.255.255.252 |	N/A |
 ||G0/ 0 / 1 |	192.168.1.97 |	255.255.255.240 | N/A |
 | S1 |	VLAN 200 |	192.168.1.66 |	255.255.255.224 |	192.168.1.65 |
 | S2 | 	VLAN 1 |	192.168.1.98 |	255.255.255.240 |	192.168.1.97 |
 | PC-A |	Сетевой адаптер |	DHCP |	DHCP |	DHCP |
 | PC-B |	Сетевой адаптер |	 DHCP |	DHCP |	DHCP |
# Таблица VLAN
| VLAN |	Имя |	Интерфейс назначен |
|:-----------|:-------------:| --------------:|
| 1 |	N/A |	S2: F0/ 18 |
|100 |	Clients |	S1: F0 / 6 |
|200 |	Management |	S1: VLAN 200 |
|999 |	Parking_Lot |	S1: F0/ 1-4, F0 / 7-24, G0 / 1-2 |
|1000 |	Native |	N/A |
# Инструкции
- Построение сети и настройка основных параметров устройства

Шаг 1: Создание схемы адресации
Подсеть в сеть 192.168.1.0/24, чтобы соответствовать следующим требованиям:

 Одна подсеть, “Подсеть A”, поддерживающая 58 хостов (клиентская VLAN на R1).

- Подсеть A:

192.168.1.0/26 (.1 -.63)

Запишем первый IP-адрес в таблицу адресации для R1 e0/0.100. Запишите второй IP-адрес в таблицу адресов для S1 VLAN 200 и введем соответствующий шлюз по умолчанию.

- Одна подсеть, “Подсеть B”, поддерживающая 28 хостов (управляющая VLAN на R1).

Подсеть B:

192.168.1.64/27 (.65-.95)

Запишем первый IP-адрес в таблицу адресации для R1 e0/0.200.
Запишем второй IP-адрес в таблицу адресов для S1 VLAN 1 и введем соответствующий шлюз по умолчанию.

-  Одна подсеть, “Подсеть C”, поддерживающая 12 хостов (клиентская сеть на уровне R2).

Подсеть C:

192.168.1.96/28 (.97-.111)

Запишим первый IP-адрес в таблицу адресации для R2 e0/1.

Шаг 2: Подключем сеть, как показано в топологии.
Подсоединим устройства, как показано на схеме топологии.

Шаг 3: Настройте основные параметры для каждого маршрутизатора.
- Присвоим маршрутизатору имя устройства.
```
  router(config)# hostname R1
```
- Отключим поиск DNS, чтобы маршрутизатор не пытался перевести неправильно введенные команды, как если бы они были именами хостов.
```
R1(config)# no ip domain lookup
```
-  Назначить класс в качестве привилегированного зашифрованного пароля EXEC.
```
enable secret cisco
```
- Назначить cisco в качестве пароля консоли и включить вход в систему.
```
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
```
- Назначить cisco в качестве пароля VTY и включить вход в систему.
```
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
```
- Зашифруем пароли в виде открытого текста.
```
R1(config)# service password-encryption
```
- Создаем баннер, предупреждающий любого, кто обращается к устройству, о том, что несанкционированный доступ запрещен.
```
R1(config)#banner motd # Unauthorized access is prohibited!
Enter TEXT message.  End with the character '#'.
 #
```
- Сохраним текущую конфигурацию в файле конфигурации запуска.
```
R1# wr
```
- Установим часы на маршрутизаторе на сегодняшнее время и дату.
```
R1#clock set 22:45:00 31 May 2023
```
# Шаг 4: Настройка маршрутизации между VLAN на R1
- Активируем интерфейс e0/1 на маршрутизаторе.
```
R1(config)#interface e0/1
R1(config-if)#no sh
R1(config-if)#exit
```
- Настроим вспомогательные интерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q, и им присваивается первый полезный адрес из вычисленного нами пула IP-адресов. Убедимся, что субинтерфейсу для собственной VLAN не назначен IP-адрес. Включим описание для каждого подинтерфейса.
```
R1(config)# interface e0/1.100
R1(config-subif)# description Client Network
R1(config-subif)# encapsulation dot1q 100
R1(config-subif)# ip address 192.168.1.1 255.255.255.192
R1(config-subif)# exit
R1(config)# interface e0/1.200
R1(config-subif)# encapsulation dot1q 200
R1(config-subif)# description Management Network
R1(config-subif)# ip address 192.168.1.65 255.255.255.224
R1(config-subif)# exit
R1(config)# interface e0/1.1000
R1(config-subif)# encapsulation dot1q 1000 native
R1(config-subif)# description Native VLAN
```
- Убедимся, что вспомогательные интерфейсы работают.
```
R1#show ip interface brief

Interface                  IP-Address      OK? Method Status             Protocol
Ethernet0/0                unassigned      YES unset  administratively down down         
Ethernet0/1                unassigned      YES unset  up                    up           
Ethernet0/1.100            192.168.1.1     YES manual up                    up           
Ethernet0/1.200            192.168.1.65    YES manual up                    up           
Ethernet0/1.1000           unassigned      YES unset  up                    up   
```
# Шаг 5: Настроим е0/1 на R2, затем e0/0 и статическую маршрутизацию для обоих маршрутизаторов
- Настроим e0/1 на R2 с первым IP-адресом подсети C, который мы рассчитали ранее
```
R2(config)# interface g0/0/1
R2(config-if)# ip address 192.168.1.97 255.255.255.240
R2(config-if)# no shutdown
R2(config-if)# exit
```
- Настроим интерфейс e0/0 и e0/1 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.
```
R1(config)# interface e0/0
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# no shutdown

R2(config)# interface e0/0
R2(config-if)# ip address 10.0.0.2 255.255.255.252
R2(config-if)# no shutdown
```
- Настроим маршрут по умолчанию на каждом маршрутизаторе, указывающем на IP-адрес на другом маршрутизаторе
```
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2
R2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
- Убедимся, что статическая маршрутизация работает, отправим запрос на адрес R2 e0/1 из R1.
```
R1#ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

```
- Сохраним текущую конфигурацию в файле конфигурации запуска.
# Шаг 6: Настройте основные параметры для каждого коммутатора.
- а.Присвоим коммутатору имя устройства.
```
switch(config)# hostname S1
```
- Отключим поиск DNS, чтобы маршрутизатор не пытался перевести неправильно введенные команды, как если бы они были именами хостов.
```
S1(config)# no ip domain-lookup
```
- Назначим class в качестве привилегированного зашифрованного пароля EXEC.
```
S1(config)# enable secret cisco
```
- Назначим cisco в качестве пароля консоли и включите вход в систему.
```
S1(config)# line console 0
S1(config-line)# password cisco
S1(config-line)# login
```
- Назначим cisco в качестве пароля VTY и включите вход в систему.
```
S1(config)# line vty 0 4
S1(config-line)# password cisco
S1(config-line)# login
```
- Зашифруем пароли в виде открытого текста.
```
S1(config)# service password-encryption
```
- Создаем баннер, предупреждающий любого, кто обращается к устройству, о том, что несанкционированный доступ запрещен.
```
S1(config)#banner motd # Unauthorized access is prohibited!
Enter TEXT message.  End with the character '#'.
 #
```
-Сохраним текущую конфигурацию в файле конфигурации запуска.
```
S1# wr
```
- Установим часы на коммутаторе на сегодняшнее время и дату.
```
S1# clock set 23:15:00 31 May 2023
```
# Шаг 7: Создаем VLAN на S1.
- Создаем и назовем требуемые VLAN на коммутаторе S1 из приведенной выше таблицы.
```
S1(config)# vlan 100
S1(config-vlan)# name Clients
S1(config-vlan)# vlan 200
S1(config-vlan)# name Management
S1(config-vlan)# vlan 999
S1(config-vlan)# name Parking_Lot
S1(config-vlan)# vlan 1000
S1(config-vlan)# name Native
S1(config-vlan)# exit
```
-  Настроим и активируем интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, вычисленный ранее. Кроме того, установим шлюз по умолчанию на S1.

```
S1(config)# interface vlan 200
S1(config-if)# ip address 192.168.1.66 255.255.255.224
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.1.65
```

- Настроим и активируем интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, вычисленный ранее. Кроме того, установим шлюз по умолчанию на S2

```
S2(config)# interface vlan 1
S2(config-if)# ip address 192.168.1.98 255.255.255.240
S2(config-if)# no shutdown
S2(config-if)# exit
S2(config)# ip default-gateway 192.168.1.97
```
- Назначим все неиспользуемые порты на S1 VLAN Parking_Lot, настроим их для режима статического доступа и деактивируем их в административном порядке. На S2 в административном порядке деактивируем все неиспользуемые порты.

```
S1(config)# interface range e0/1 - 2
S1(config-if-range)# switchport mode access
S1(config-if-range)# switchport access vlan 999
S1(config-if-range)# shutdown
S1(config-if-range)# exit

S2(config)# interface range e0/2 - 3
S2(config-if-range)# switchport mode access
S2(config-if-range)# shutdown
S2(config-if-range)# exit
```
# Шаг 8: Назначим VLAN  интерфейсам коммутатора.
- Назначим используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настроим их для режима статического доступа.

```
S1(config)# interface e0/3
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 100
```
- Убедиимся, что VLAN назначены правильным интерфейсам.

```
S1#sh vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active
100  Clients                          active    Et0/3
200  Management                       active    Et0/0
999  Parking_Lot                      active    Et0/1, Et0/2
1000 Native                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

```
- Почему интерфейс e0 / 0 указан в VLAN 1?  
Порт 0 находится в VLAN по умолчанию и не был настроен как магистраль 802.1Q.
# Шаг 9: вручную настройте интерфейс S1 e0/ 0 в качестве магистрали 802.1Q.
- Изменим режим порта коммутатора в интерфейсе на принудительное подключение.

```
S1(config)# interface e0/0
S1(config-if)# switchport trunk encapsulation dot1q
S1(config-if)# switchport mode trunk
```
- Как часть конфигурации магистрали, установим для собственной VLAN значение 1000.
```
S1(config-if-range)# switchport trunk native vlan 1000
```
- В качестве другой части конфигурации магистрали укажим, что VLAN 100, 200 и 1000 разрешено пересекать магистраль.

```
S1(config-if-range)# switchport trunk allowed vlan 100,200,1000
```
- Сохраним текущую конфигурацию в файле конфигурации запуска.

```
sS1# wr
```

- Проверим состояние транкинга.

```
S1#show interfaces trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/0       100,200,1000

Port        Vlans allowed and active in management domain
Et0/0       100,200,1000

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       100,200,1000

```
- На этом этапе какой IP-адрес был бы у PC, если бы они были подключены к сети с использованием DHCP?
```
Они будут самонастраиваться с использованием автоматического частного IP-адреса (APIPA) в диапазоне 169.254.x.x.
```

# Часть 2: Настройка и проверка двух серверов DHCPv4 на R1

```
В части 2 мы будем настраивать и проверять сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.
```
 Шаг 1: Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A

- Исключим первые пять используемых адресов из каждого пула адресов.
```
R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.5
```
- Создаем пул DHCP (используйте уникальное имя для каждого пула).

```
R1(config)# ip dhcp pool R1_Client_LAN
```
- Укажим сеть, которую поддерживает этот DHCP-сервер.

```
R1(dhcp-config)# network 192.168.1.0 255.255.255.192
```
-  Настроим доменное имя как ccna-lab.com

```
R1(dhcp-config)# domain-name ccna-lab.com
```
- e. Настроим соответствующий шлюз по умолчанию для каждого пула DHCP.

```
R1(dhcp-config)# default-router 192.168.1.1
```

-f. Настроим время аренды на 2 дня по 12 часов 30 минут.

```
R1(dhcp-config)# lease 2 12 30
```

- g. Затем настроим второй пул DHCPv4, используя имя пула R2_Client_LAN и вычисляем сеть, маршрутизатор по умолчанию, и используем то же доменное имя и время аренды, что и в предыдущем пуле DHCP.

```
R1(config)# ip dhcp excluded-address 192.168.1.97 192.168.1.101
R1(config)# ip dhcp pool R2_Client_LAN
R1(dhcp-config)# network 192.168.1.96 255.255.255.240
R1(dhcp-config)# default-router 192.168.1.97
R1(dhcp-config)# domain-name ccna-lab.com
R1(dhcp-config)# lease 2 12 30
```
Шаг 2: Сохраним свою конфигурацию   

Сохраним текущую конфигурацию в файле конфигурации запуска.

```
R1# wr
```
Шаг 3: Проверим конфигурацию сервера DHCPv4    

- a. Выполним команду show ip dhcp pool для проверки сведений о пуле.
```
R1#show ip dhcp pool

Pool R1_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0
 Total addresses                : 62
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.7          192.168.1.1      - 192.168.1.62      1

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0
 Total addresses                : 14
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.103        192.168.1.97     - 192.168.1.110     1
```
- b. Выполним команду показать привязки ip dhcp для проверки установленных назначений адресов DHCP.
```
R1#show ip dhcp binding
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.6         0100.5079.6668.05       Jun 03 2023 10:07 AM    Automatic
192.168.1.102       0100.5079.6668.06       Jun 03 2023 10:14 AM    Automatic
```

- c. Выполним команду показать статистику ip dhcp-сервера для проверки сообщений DHCP.
```
R1#show ip dhcp server statistics
Memory usage         42093
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         5
DHCPREQUEST          3
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            3
DHCPACK              3
DHCPNAK              0
```


Шаг 4: Попытка получить IP-адрес от DHCP на ПК-A
- a. Откроим командную строку на PC-A и выполните команду ipconfig /renew.

- b. После завершения процесса обновления выполним команду ipconfig для просмотра новой информации об IP.

- c. Протестируем подключение, отправив запрос на IP-адрес интерфейса R1 e0/0.
```
VPCS> ping 10.0.0.1

84 bytes from 10.0.0.1 icmp_seq=1 ttl=255 time=0.436 ms
84 bytes from 10.0.0.1 icmp_seq=2 ttl=255 time=0.480 ms
84 bytes from 10.0.0.1 icmp_seq=3 ttl=255 time=0.455 ms
84 bytes from 10.0.0.1 icmp_seq=4 ttl=255 time=0.416 ms
84 bytes from 10.0.0.1 icmp_seq=5 ttl=255 time=0.454 ms
```

# Часть 3: Настройка и проверка DHCP-ретранслятора на R2

В части 3 мы настроим R2 для ретрансляции DHCP-запросов из локальной сети по интерфейсу e0/1 на DHCP-сервер (R1).

Шаг 1: Настроим R2 в качестве агента ретрансляции DHCP для локальной сети на e0/1
- a. Настроим команду ip helper-address для e0/1, указав IP-адрес R1 для e0/0.
```
R2(config)# interface e0/1
R2(config-if)# ip helper-address 10.0.0.1
```
- б. Сохраним свою конфигурацию.
```
R2# wr
```
Шаг 2: Попытка получить IP-адрес от DHCP на PC-B
- a. Откроем командную строку на PC-B и выполним команду ipconfig /renew.

- b. После завершения процесса обновления выполним команду ipconfig для просмотра новой информации об IP.

- c. Протестируем подключение, отправив запрос на IP-адрес интерфейса R1 e0/1.
```
VPCS> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=254 time=0.525 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=254 time=0.629 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=254 time=0.660 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=254 time=0.528 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=254 time=0.587 ms

VPCS> ping 192.168.1.65

84 bytes from 192.168.1.65 icmp_seq=1 ttl=254 time=0.598 ms
84 bytes from 192.168.1.65 icmp_seq=2 ttl=254 time=0.561 ms
84 bytes from 192.168.1.65 icmp_seq=3 ttl=254 time=0.550 ms
84 bytes from 192.168.1.65 icmp_seq=4 ttl=254 time=0.616 ms
84 bytes from 192.168.1.65 icmp_seq=5 ttl=254 time=0.602 ms

```

- d. Выполним команду показать привязку dhcp ip на R1 для проверки привязок DHCP.
```
show ip dhcp binding
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.6         0100.5079.6668.05       Jun 03 2023 10:07 AM    Automatic
192.168.1.102       0100.5079.6668.06       Jun 03 2023 10:14 AM    Automatic
```

- e. Выполним команду показать статистику ip dhcp-сервера для R1 и R2 для проверки сообщений DHCP.
```
R1#show ip dhcp server statistics
Memory usage         50285
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         9
DHCPREQUEST          6
DHCPDECLINE          0
DHCPRELEASE          1
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            6
DHCPACK              6
DHCPNAK              0
```
Конфигурации всех устройств:
S1![]() R1![]() R2![]() S2![]()
