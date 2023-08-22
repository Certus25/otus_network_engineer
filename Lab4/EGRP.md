# Лабораторная работа н атему EGRP.
 
 Задание:

 - В офисе С.-Петербург настроить EIGRP
- R32 получает только маршрут по-умолчанию
- R16-17 анонсируют только суммарные префиксы
- Использовать EIGRP named-mode для настройки сети
---------------------------------------------------

Решение:

Схема:

![](https://github.com/Certus25/otus_network_engineer/blob/060e48910a2a904eb60cf88f93b7e4cfad227909/Lab4/EGRP.PNG)

----------------------------------------------------------------

# Настройка R16,R17.

Настройку EIGRP произведем на примере маршрутизатора R16. На нем настроим процесс EIGRP. По заданию необходимо использовать named mode. Так же по заданию необходимо, чтобы R32 получал только маршрут по умолчанию, поэтому на интерфейсе e0/3 пропишем summary-address.
```

router eigrp Saint-P
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/0
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/1
   summary-address 192.168.0.0 255.255.0.0
   summary-address 192.168.0.0 255.255.255.0
  exit-af-interface
  !
  af-interface Ethernet0/3
   summary-address 0.0.0.0 0.0.0.0
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 192.168.0.0 0.0.255.255
  network 192.168.0.244 0.0.0.3
  network 192.168.0.248 0.0.0.3
  network 192.168.10.0
  network 192.168.20.0
  eigrp router-id 1.1.1.16
 exit-address-family
!



```
На R17 настройки аналогичные.

```

router eigrp Saint-P
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/0
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/1
   summary-address 192.168.0.0 255.255.0.0
   summary-address 192.168.0.0 255.255.255.0
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 192.168.0.0 0.0.255.255
  network 192.168.0.252 0.0.0.3
  network 192.168.10.0
  network 192.168.20.0
  eigrp router-id 1.1.1.17
 exit-address-family


```
------------------------------------------------

# Настройка R18

На R18 будут аналогичные настройки. Только здесь для примера пропишем маршрут по умолчанию в сторону R24.

```
router eigrp Saint-P
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
  exit-af-topology
  network 192.168.0.248 0.0.0.3
  network 192.168.0.252 0.0.0.3
  eigrp router-id 1.1.1.18
 exit-address-family
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 55.168.0.246

```
------------------------------------------------

# Настройка R32.

```

router eigrp Saint-P
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
  exit-af-topology
  network 192.168.0.244 0.0.0.3
  eigrp router-id 1.1.1.32
 exit-address-family

```

-----------------------------------

# Проверка.
Посмотрим таблицу мрашрутизации R32.
![](https://github.com/Certus25/otus_network_engineer/blob/12a3aecf6395c218f81245d22d19b9b8e46981ec/Lab4/EGRP-R32.PNG)

Видим, что приходит только маршрут по умолчанию.

Проверим таблицу мрашрутизации R17.

![](https://github.com/Certus25/otus_network_engineer/blob/12a3aecf6395c218f81245d22d19b9b8e46981ec/Lab4/EGRP-R17.PNG)