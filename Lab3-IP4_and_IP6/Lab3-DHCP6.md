# Лабораторная работа: реализация DHCPv6
# Задание: 
- Часть 1. Построение сети и настройка основных параметров устройства

- Часть 2. Проверка назначения адреса SLAAC из R1

- Часть 3. Настройка и проверка сервера DHCPv6 без состояния на R1

- Часть 4. Настройка и проверка сервера DHCPv6 с отслеживанием состояния на R1

- Часть 5. Настройка и проверка реле DHCPv6 на R2

# Предпосылки / сценарий
 Динамическое назначение глобальных одноадресных адресов IPv6 (GUA) может быть настроено следующими тремя способами:

- Автоконфигурация адреса без состояния (SLACC)

- Протокол динамической настройки хоста без состояния для IPv6 (DHCPv6)

- DHCPv6 с сохранением состояния

При использовании SLACC для назначения IPv6-адресов хостам сервер DHCPv6 не используется. Поскольку сервер DHCPv6 не используется при реализации SLACC, хосты не могут получать дополнительную важную сетевую информацию, включая адрес сервера доменных имен (DNS), а также доменное имя.

При использовании DHCPv6 без состояния для назначения IPv6-адресов хосту сервер DHCPv6 используется для назначения дополнительной важной сетевой информации, однако IPv6-адрес назначается с помощью SLACC.

При внедрении DHCPv6 с отслеживанием состояния сервер DHCPv6 присваивает всю сетевую информацию, включая IPv6-адрес.

Определение того, как хосты получают динамическую IPv6-адресацию, зависит от установки флага, содержащегося в сообщениях с рекламой маршрутизатора (RA).

В этом сценарии компания увеличилась в размерах, и сетевые администраторы больше не могут назначать IP-адреса устройствам вручную. Ваша задача - настроить маршрутизатор R2 для назначения адресов IPv6 в двух разных подсетях, подключенных к маршрутизатору R1.

Примечание: Маршрутизаторами, используемыми в практических лабораториях CCNA, являются Cisco 4221 с Cisco IOS XE версии 16.9.4 (образ universalk9). В лабораториях используются коммутаторы Cisco Catalyst 2960s с Cisco IOS версии 15.2(2) (образ lanbasek9). Могут использоваться другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели и версии Cisco IOS доступные команды и создаваемый результат могут отличаться от того, что показано в лабораторных исследованиях. Обратитесь к сводной таблице интерфейса маршрутизатора в конце лабораторной работы для получения правильных идентификаторов интерфейса.

Примечание: Убедитесь, что маршрутизаторы и коммутаторы были удалены и не имеют конфигураций запуска. Если вы не уверены, обратитесь к своему инструктору.

Необходимые ресурсы
2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или сопоставимым)

2 коммутатора (Cisco 2960 с образом Cisco IOS версии 15.2 (2) lanbasek9 или аналогичным) - Необязательно

2 компьютера (Windows с программой эмуляции терминала, такой как Tera Term)

Консольные кабели для настройки устройств Cisco IOS через консольные порты

Кабели Ethernet, как показано в топологии



# Схема лабораторного стенда:
 ![](https://github.com/Certus25/otus_network_engineer/blob/5fd00014feb97c5d769cd16dad6b9ed96283e895/Shema-Lab3.png)
# Таблица адресации
 | Устройство |	Интерфейс |	IPv6-адрес  |
 | :----------|:----------:| ----------------------:|
 | R1         |	  e0/0     |2001:db8:acad:2::1/64      fe80::1
 ||e0/1 |		2001:db8:acad: 1::1/64     	fe80::1
 |R2 | e0/0 |	2001:db8:acad:2::2/64  	fe80::2 |
 | | e0/1 | 	2001:db8:acad:3::1/64 	fe80::1 |
 VPC-A | 	Сетевой адаптер | DHCP |
 VPC-B | 	Сетевой адаптер | DHCP |
 

# Инструкции
- 1.Создайте сеть и настройте основные параметры устройства

```
В части 1 вы настроите топологию сети и настроите основные параметры на хостах ПК и коммутаторах.
```
- Подключите сеть, как показано в топологии.
```
  Подсоедините устройства, как показано на схеме топологии, и при необходимости подключите кабель.
```
2. Настройте основные параметры для каждого коммутатора.

   a. Откройте окно конфигурации

   b. Присвойте коммутатору имя устройства.
   ```
   conf t
   hostname S1
   ```
   b. Отключите поиск DNS, чтобы маршрутизатор не пытался перевести неправильно введенные команды, как если бы они были именами хостов.
   ```
   no ip domain-lookup
   ```
   c. Назначьте класс в качестве привилегированного зашифрованного пароля EXEC.
   ```
   enable secret cisco
   ```
   d. Назначьте cisco в качестве пароля консоли и включите вход в систему.
   ```
   S1(config)# line console 0
   S1(config-line)# password cisco
   S1(config-line)# login
   ```
   e.Назначьте cisco в качестве пароля VTY и включите вход в систему.
   ```
   S1(config)# line vty 0 4
   S1(config-line)# password cisco
   S1(config-line)# login
   ```

   e. Зашифруйте пароли с открытым текстом.
   ```
   S1(config)# service password-encryption
   ```
   f. Создайте баннер, предупреждающий любого, кто обращается к устройству, о том, что несанкционированный доступ запрещен.
   ```
   S1(config)#banner motd # Unauthorized access is prohibited!
   Enter TEXT message.  End with the character '#'.
   #
   ```
   g. Выключите все неиспользуемые порты
   ```
   no sh
   ```
   h. Сохраните текущую конфигурацию в файле конфигурации запуска.
   ```
   S1# wr
   ```   
 - 3 . Настройте основные параметры для каждого маршрутизатора.

    a. Присвойте маршрутизатору имя устройства.
    ```
    conf t
    hostname R1
    ```

    b.Отключите поиск DNS, чтобы маршрутизатор не пытался перевести неправильно введенные команды, как если бы они были именами хостов.
     ```
    no ip domain-lookup
    ```
    c. Назначьте класс в качестве привилегированного зашифрованного пароля EXEC.
    ```
    enable secret cisco
    ```

    d. Назначьте cisco в качестве пароля консоли и включите вход в систему.
    ```
    R1(config)# line console 0
    R1(config-line)# password cisco
    R1(config-line)# login
    ```

    e.Назначьте cisco в качестве пароля VTY и включите вход в систему.
    ```
    R1(config)# line vty 0 4
    R1(config-line)# password cisco
    R1(config-line)# login
    ```

    f. Зашифруйте пароли с открытым текстом.
    ```
    S1(config)# service password-encryption
    ```

    g. Создайте баннер, предупреждающий любого, кто обращается к устройству, о том, что несанкционированный доступ запрещен.
     ```
    S1(config)#banner motd # Unauthorized access is prohibited!
    Enter TEXT message.  End with the character '#'.
    #
    ```

    h. Включить маршрутизацию IPv6
    ```
    ipv6 unicast-routing
    ```
    i. Сохраните текущую конфигурацию в файле конфигурации запуска.
    ```
    R1# wr
    ```
- Настройте интерфейсы и маршрутизацию для обоих маршрутизаторов.

  a. Настройте интерфейсы e0/0 и e0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.

  ```
  R1
  interface e0/0
  ipv6 add 2001:db8:acad:2::1/64
  ipv6 add 	fe80::1 link-local
  exit

  interface e0/1
  ipv6 add 2001:db8:acad: 1::1/64
  ipv6 add 	fe80::1 link-local
  exit

  R2

  interface e0/0
  ipv6 add 2001:db8:acad:2::2/64
  ipv6 add 	fe80::2 link-local
  exit

  interface e0/1
  ipv6 add 	2001:db8:acad:3::1/64
  ipv6 add 	fe80::1 link-local
  exit
  ```

  b. Настройте маршрут по умолчанию на каждом маршрутизаторе, указывающем на IP-адрес e0/0 на другом маршрутизаторе.
  ```
  R1(config)#ipv6 route 2001:db8:acad:2::2/64 e0/0 fe80::2
  
  R2(config)#ipv6 route 2001:db8:acad:2::1/64 e0/0 fe80::1

  ```


  c. Убедитесь, что маршрутизация работает, отправив запрос на адрес R2 e0/1 с R1
  ```
  R2>ping fe80::1
  Output Interface: Ethernet0/0
  Type escape sequence to abort.
  Sending 5, 100-byte ICMP Echos to FE80::1, timeout is 2 seconds:
  Packet sent with a source address of FE80::2%Ethernet0/0
  !!!!!
  Success rate is 100 percent (5/5), round-trip min/avg/max = 1/4/17 ms
  R2>

  ```

  d. Сохраните текущую конфигурацию в файле конфигурации запуска.
  ```
  wr
  ```
# Проверьте назначение адреса SLAAC из R1

 В части 2 мы убедимся, что хост-компьютер-A получает IPv6-адрес с использованием метода SLAAC.

  Включите PC-A и убедитесь, что сетевой адаптер настроен на автоматическую настройку IPv6.
  ```
  
  VPCS> ip auto
  GLOBAL SCOPE      : 2001:db8:acad:3:2050:79ff:fe66:6806/64
  ROUTER LINK-LAYER : aa:bb:cc:00:20:10

  ```

 Откуда взялась часть адреса с идентификатором хоста?
```
  Ответ будет зависеть от конфигурации операционной системы. Либо хост генерирует адрес EUI-64 на основе интерфейса MAC, либо хост генерирует случайный 64-разрядный адрес.  
```

# Часть 3. Настройте и проверьте сервер DHCPv6 на R1

  В части 3 мы будем настраивать и проверять DHCP-сервер без состояния на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.

  Обратите внимание, что отсутствует основной DNS-суффикс. Также обратите внимание, что указанные адреса DNS-серверов являются адресами “локальной любой рассылки сайта”, а не одноадресными адресами, как можно было бы ожидать.

  - Шаг 2: Настройте R1 для предоставления DHCPv6 без состояния для PC-A.

  a. Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В качестве части этого пула назначьте адрес DNS-сервера как 2001:db8:acad::1, а доменное имя как stateless.com .

  ```
  R1(config)# ipv6 dhcp pool R1-STATELESS
  R1(config-dhcp)# dns-server 2001:db8:acad::254
  R1(config-dhcp)# domain-name STATELESS.com
  ```
  b. Настройте интерфейс e0/1 на R1, чтобы предоставить локальной сети R1 флаг OTHER config, и укажите пул DHCP, который вы только что создали, в качестве ресурса DHCP для этого интерфейса.
  ```
  R1(config)# interface e0/1
  R1(config-if)# ipv6 nd other-config-flag
  R1(config-if)# ipv6 dhcp server R1-STATELESS
  ```
  c. Сохраните текущую конфигурацию в файле конфигурации запуска.
  ```
  R1# copy running-config запуск-config
  ```
  d. Перезагрузите компьютер-A.
  ```
  NAME              : VPCS[1]
  LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6805/64
  GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6805/64
  DNS               :
  ROUTER LINK-LAYER : aa:bb:cc:00:10:10
  MAC               : 00:50:79:66:68:05
  LPORT             : 20000
  RHOST:PORT        : 127.0.0.1:30000
  MTU:              : 1500
  ```
  f. Протестируйте подключение, запросив IP-адрес интерфейса R2 G0 / 0 / 1.

  - Часть 4. Настройка сервера DHCPv6 с отслеживанием состояния на R1

  В части 4 вы настроите R1 для ответа на запросы DHCPv6 из локальной сети на R2.

  a. Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaaa::/80. При этом будут предоставлены адреса локальной сети, подключенной к интерфейсу e0/1 на R2. В качестве части пула задайте DNS-серверу значение 2001:db8:acad::254, а доменному имени - значение STATEFUL.com.

  ```
  R1(config)# ipv6 dhcp pool R2-STATEFUL
  R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
  R1(config-dhcp)# dns-server 2001:db8:acad::254
  R1(config-dhcp)# domain-name STATEFUL.com
  ```

  б. Назначьте пул DHCPv6, который вы только что создали, для интерфейса e0/0 на R1

  ```
  R1(config)# interface g0/0/0
  R1(config-if)# ipv6 dhcp server R2-STATEFUL
  ```
- Часть 5: Настройка и проверка реле DHCPv6 на R2.
  
  В части 5 вы будете настраивать и проверять ретрансляцию DHCPv6 на R2, позволяя PC-B получать IPv6-адрес.

  Шаг 1: Включите PC-B и проверьте адрес SLAAC, который он генерирует.

  ```
  NAME              : VPCS[1]
  LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6806/64
  GLOBAL SCOPE      : 2001:db8:acad:3:2050:79ff:fe66:6806/64
  DNS               :
  ROUTER LINK-LAYER : aa:bb:cc:00:20:10
  MAC               : 00:50:79:66:68:06
  LPORT             : 20000
  RHOST:PORT        : 127.0.0.1:30000
  MTU:              : 1500
  ```
  Обратите внимание, что в выходных данных используется префикс 2001:db8:acad: 3::

  Шаг 2: Настройте R2 в качестве агента ретрансляции DHCP для локальной сети на e0/1.

  a. Настройте команду ipv6 dhcp relay на интерфейсе R2 e0/1, указав адрес назначения интерфейса e0/0 на R1. Также настройте команду managed-config-flag.

  б. Сохраните свою конфигурацию.

  ```
  R2# wr
  ```
  Шаг 3: Попытайтесь получить IPv6-адрес от DHCPv6 на PC-B.
  
  a. Перезагрузите компьютер-B.

  б. Откройте командную строку на PC-B и выполните команду show ipv6 и проверьте выходные данные, чтобы увидеть результаты работы реле DHCPv6.

  ```
  NAME              : VPCS[1]
  LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6806/64
  GLOBAL SCOPE      : 2001:db8:acad:3:2050:79ff:fe66:6806/64
  DNS               :
  ROUTER LINK-LAYER : aa:bb:cc:00:20:10
  MAC               : 00:50:79:66:68:06
  LPORT             : 20000
  RHOST:PORT        : 127.0.0.1:30000
  MTU:              : 1500
  ```

  c. Протестируйте подключение, запросив IP-адрес интерфейса R1 e0/1.
  
 - Настройки устройства – окончательный

 S1
 ```
 version 15.1
  service timestamps debug datetime msec
  service timestamps log datetime msec
  service password-encryption
  service compress-config
  !
  hostname S1
  !
  boot-start-marker
  boot-end-marker
  !
  enable secret 4 tnhtc92DXBhelxjYk8LWJrPV36S2i4ntXrpb4RFmfqY
  !
  no aaa new-model
  !
  ip cef
  !
  no ip domain-lookup
  no ipv6 cef
  ipv6 multicast rpf use-bgp
  !
  spanning-tree mode pvst
  spanning-tree extend system-id
  !
  vlan internal allocation policy ascending
  !
  interface Ethernet0/0
  duplex auto
  !
  interface Ethernet0/1
  duplex auto
  !
  interface Ethernet0/2
  shutdown
  duplex auto
  !
  interface Ethernet0/3
  shutdown
  duplex auto
  !
  no ip http server
  !
  control-plane
  !
  line con 0
  password 7 00071A150754
  logging synchronous
  login
  line aux 0
  line vty 0 4
  password 7 121A0C041104
  login
  !
  end
  ```
  S2
  ```
  version 15.1
  service timestamps debug datetime msec
  service timestamps log datetime msec
  service password-encryption
  service compress-config
  !
  hostname S2
  !
  boot-start-marker
  boot-end-marker
  !
  !
  enable secret 4 tnhtc92DXBhelxjYk8LWJrPV36S2i4ntXrpb4RFmfqY
  !
  no aaa new-model
  !
  ip cef
  !
  no ip domain-lookup
  no ipv6 cef
  ipv6 multicast rpf use-bgp
  !
  spanning-tree mode pvst
  spanning-tree extend system-id
  !
  vlan internal allocation policy ascending
  !
  interface Ethernet0/0
  duplex auto
  !
  interface Ethernet0/1
  duplex auto
  !
  interface Ethernet0/2
  shutdown
  duplex auto
  !
  interface Ethernet0/3
  shutdown
  duplex auto
  !
  no ip http server
  !
  control-plane
  !
  line con 0
 password 7 104D000A0618
 logging synchronous
 login
  line aux 0
  line vty 0 4
  password 7 0822455D0A16
  login
  !
  end
  ```

 Маршрутизатор R1
 ```
  R1#sh run
  Building configuration...

  Current configuration : 1574 bytes
  !
  ! Last configuration change at 20:40:17 UTC Thu Jun 15 2023
  !
  version 15.5
  service timestamps debug datetime msec
  service timestamps log datetime msec
  service password-encryption 
  !
  hostname R1
  !
  boot-start-marker
  boot-end-marker
  !
  !
  enable secret 5 $1$mS7w$X3/BwXEcyPCdm1WYC/JG30
  !
  no aaa new-model
  !
  !
  !
  bsd-client server url https://cloudsso.cisco.com/as/token.oauth2
  mmi polling-interval 60
  no mmi auto-configure
  no mmi pvc
  mmi snmp-timeout 180
  !
  no ip domain lookup
  ip cef
  ipv6 unicast-routing
  ipv6 cef
  ipv6 dhcp pool R1-STATELESS
  dns-server 2001:DB8:ACAD::254
  domain-name STATELESS.com
  !
  ipv6 dhcp pool R2-STATEFUL
  address prefix 2001:DB8:ACAD:3:AAA::/80
  dns-server 2001:DB8:ACAD::254
  domain-name STATEFUL.com
  !
  !
  multilink bundle-name authenticated
  !
  !
  !
  !
  !
  !
  !
  cts logging verbose
  !
  !
  !
  redundancy
  !
  !
  interface Ethernet0/0
  no ip address
  ipv6 address FE80::1 link-local
  ipv6 address 2001:DB8:ACAD:2::1/64
  ipv6 dhcp server R2-STATEFUL
  !
  interface Ethernet0/1
  no ip address
  ipv6 address FE80::1 link-local
  ipv6 address 2001:DB8:ACAD:1::1/64
  ipv6 nd other-config-flag
  ipv6 dhcp server R1-STATELESS
  !
  interface Ethernet0/2
  no ip address
  shutdown
  !
  interface Ethernet0/3
  no ip address
  shutdown
  !
  ip forward-protocol nd
  !
  no ip http server
  no ip http secure-server
  !
  ipv6 route ::/0 2001:DB8:ACAD:2::2
  !
  !
  control-plane
  !
  !
  line con 0
  password 7 00071A150754
  logging synchronous
  login
  line aux 0
  line vty 0 4
  password 7 1511021F0725
  login
  transport input none
  !
  !
  end
```
Маршрутизатор R2
```
  R2#sh run
  Building configuration...

  Current configuration : 1360 bytes
  !
  ! Last configuration change at 20:32:39 UTC Thu Jun 15 2023
  !
  version 15.5
  service timestamps debug datetime msec
  service timestamps log datetime msec
  service password-encryption
  !
  hostname R2
  !
  boot-start-marker
  boot-end-marker
  !
  !
  enable secret 5 $1$u98q$LXAPEYIGzDxNMeRgJxPYI/
  !
  no aaa new-model
  !
  !
  !
  bsd-client server url https://cloudsso.cisco.com/as/token.oauth2
  mmi polling-interval 60
  no mmi auto-configure
  no mmi pvc
  mmi snmp-timeout 180
  !
  no ip domain lookup
  ip cef
  ipv6 unicast-routing
  ipv6 cef
  !
  multilink bundle-name authenticated
  !
  !
  !
  !
  !
  !
  !
  cts logging verbose
  !
  !
  !
  redundancy
  !
  !
  !
  !
  !
  ! 
  !
  !
  !
  !
  !
  !
  !
  !
  !
  interface Ethernet0/0
  no ip address
  ipv6 address FE80::2 link-local
  ipv6 address 2001:DB8:ACAD:2::2/64
  !
  interface Ethernet0/1
  no ip address
  ipv6 address FE80::1 link-local
  ipv6 address 2001:DB8:ACAD:3::1/64
  ipv6 nd managed-config-flag
  ipv6 dhcp relay destination 2001:DB8:ACAD:2::1 Ethernet0/0
  !
  interface Ethernet0/2
  no ip address
  shutdown
  !
  interface Ethernet0/3
  no ip address
  shutdown
  !
  ip forward-protocol nd
  !
  !
  no ip http server
  no ip http secure-server
  !
  ipv6 route ::/0 2001:DB8:ACAD:2::1
  !
  !
  !
  control-plane
  !
  !
  !
  !
  !
  !
  !
  !
  line con 0
  password 7 060506324F41
  logging synchronous
  login
  line aux 0
  line vty 0 4
  password 7 045802150C2E
  login
  transport input none
  !
  !
  end
```
