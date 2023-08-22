# OSPF

Цель:

 - Настроить OSPF офисе Москва

- Разделить сеть на зоны

- Настроить фильтрацию между зонами


Описание/Пошаговая инструкция выполнения домашнего задания:

- Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
    
- Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.

- Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.

- Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.

- Настройка для IPv6 повторяет логику IPv4.

------------------------------------------
- Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.

    Настройку OSPF произведем на примере маршрутизатора R14. На нем настроим процесс OSPF, для примера зададим статический маршрут по умолчанию. На R15 настройки аналогичные.

R14:
```
   router ospf 1
    router-id 10.0.254.254
    area 101 stub no-summary
    network 10.0.0.240 0.0.0.3 area 0
    network 10.0.0.244 0.0.0.3 area 101
    network 10.0.0.248 0.0.0.3 area 0
    network 10.0.0.252 0.0.0.3 area 0
    network 54.168.0.252 0.0.0.3 area 0
    default-information originate

```

Интерфейсы e0/0, e0/1 относятся к 0-й зоне, интерфейс e0/3 к 101 зоне.

R15:
```
   router ospf 1
    router-id 10.0.254.251
    area 102 filter-list prefix acl-area-101 in
    network 10.0.0.200 0.0.0.3 area 102
    network 10.0.0.232 0.0.0.3 area 10
    network 10.0.0.236 0.0.0.3 area 10
    network 10.0.0.240 0.0.0.3 area 0
    network 53.168.0.252 0.0.0.3 area 0


```

- Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.

Настройку OSPF произведем на примере маршрутизатора R12. Настроим процесс OSPF. На R13 настройки аналогичные.

R12:
```

   router ospf 10
    router-id 10.0.254.248
    network 10.0.0.236 0.0.0.3 area 0
    network 10.0.0.252 0.0.0.3 area 0
    network 10.0.7.0 0.0.0.255 area 10
    network 10.0.10.0 0.0.0.255 area 10
    default-information originate always


```

R13:
```
   router ospf 10
    router-id 10.0.254.245
    network 10.0.0.232 0.0.0.3 area 0
    network 10.0.0.248 0.0.0.3 area 0
    network 10.0.7.0 0.0.0.255 area 10
    network 10.0.10.0 0.0.0.255 area 10
    default-information originate always


```

- Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.

```

 router ospf 1
  router-id 10.0.254.230
  area 101 stub no-summary
  network 10.0.0.244 0.0.0.3 area 101

```

- Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.

```

 router ospf 1
  router-id 10.0.254.227
  network 10.0.0.200 0.0.0.3 area 102

```

Как видно, в таблице маршрутизации на R20 маршрут до сети 10.0.0.243/30 отсутствует.

```

    R20-KAK-1001#sh ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
         D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
         N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
        E1 - OSPF external type 1, E2 - OSPF external type 2
         i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
        ia - IS-IS inter area, * - candidate default, U - per-user static route
        o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
        a - application route
        + - replicated route, % - next hop override

    Gateway of last resort is 10.0.0.202 to network 0.0.0.0

    S*    0.0.0.0/0 [1/0] via 10.0.0.202
      10.0.0.0/8 is variably subnetted, 15 subnets, 3 masks
    C        10.0.0.200/30 is directly connected, Ethernet0/0
    L        10.0.0.201/32 is directly connected, Ethernet0/0
    O IA     10.0.0.224/30 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.0.228/30 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.0.232/30 [110/20] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.0.236/30 [110/20] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.0.240/30 [110/20] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.0.248/30 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.0.252/30 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.7.0/24 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.10.0/24 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0
    C        10.0.254.227/32 is directly connected, Loopback0
    O IA     10.0.254.245/32 [110/31] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.254.248/32 [110/21] via 10.0.0.202, 00:00:30, Ethernet0/0
    O IA     10.0.254.254/32 [110/21] via 10.0.0.202, 00:00:30, Ethernet0/0
      53.0.0.0/30 is subnetted, 1 subnets
    O IA     53.168.0.252 [110/20] via 10.0.0.202, 00:00:30, Ethernet0/0
      54.0.0.0/30 is subnetted, 1 subnets
    O IA     54.168.0.252 [110/30] via 10.0.0.202, 00:00:30, Ethernet0/0

```


