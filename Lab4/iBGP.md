# iBGP

## Цель:

- Настроить iBGP в офисе Москва

- Настроить iBGP в сети провайдера Триада

- Организовать полную IP связанность всех сетей
--------------------

### Описание/Пошаговая инструкция выполнения домашнего задания:

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

- Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.

-   Настроите iBGP в провайдере Триада, с использованием RR.

- Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.

- Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.
- Все сети в лабораторной работе должны иметь IP связность.
-----------

### Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.

- R15:
```

    router bgp 1001
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
 neighbor 14.14.14.14 remote-as 1001
 neighbor 14.14.14.14 update-source Loopback0
 neighbor 53.168.0.254 remote-as 301
 !
 address-family ipv4
  network 15.15.15.15 mask 255.255.255.255
  redistribute connected route-map MSK
  neighbor 14.14.14.14 activate
  neighbor 14.14.14.14 next-hop-self
  neighbor 14.14.14.14 soft-reconfiguration inbound
  neighbor 53.168.0.254 activate
 exit-address-family


```
Сохраняем все префиксы прилетевшие от пира
```
neighbor 14.14.14.14 soft-reconfiguration inbound
```

Меняем префикс
```
 next-hop 
neighbor 14.14.14.14 next-hop-self
```

- R14:
```

    router bgp 1001
 bgp router-id 14.14.14.14
 bgp log-neighbor-changes
 neighbor 15.15.15.15 remote-as 1001
 neighbor 15.15.15.15 update-source Loopback0
 neighbor 54.168.0.254 remote-as 101
 !
 address-family ipv4
  network 14.14.14.14 mask 255.255.255.255
  redistribute connected route-map MSK
  neighbor 15.15.15.15 activate
  neighbor 15.15.15.15 next-hop-self
  neighbor 15.15.15.15 soft-reconfiguration inbound
  neighbor 54.168.0.254 activate
 exit-address-family


```
--------
### Настроите iBGP в провайдере Триада, с использованием RR.

Настроим все роутеры, и определим R23, как роутер-рефлектор.
Создадим peer-group iBGP,добавим её AS-520 и настроем обращение, через Loopback 0.

```
neighbor iBGP peer-group
```
```
neighbor iBGP remote-as 520
```

```
neighbor iBGP update-source Loopback0
```
Для RR добавим в peer-group iBGP все роутеры AS-520, на оставших роутера добавляем адрес RR.

```
neighbor 24.24.24.24 peer-group iBGP
neighbor 25.25.25.25 peer-group iBGP
neighbor 26.26.26.26 peer-group iBGP
```
```
neighbor 23.23.23.23 peer-group iBGP
```
Конфигурации iBGP на всех роутера.
R23:

```
router bgp 520
 bgp router-id 23.23.23.23
 bgp log-neighbor-changes
 neighbor iBGP peer-group
 neighbor iBGP remote-as 520
 neighbor iBGP update-source Loopback0
 neighbor 24.24.24.24 peer-group iBGP
 neighbor 25.25.25.25 peer-group iBGP
 neighbor 26.26.26.26 peer-group iBGP
 neighbor 55.168.0.253 remote-as 101
 !
 address-family ipv4
  neighbor iBGP route-reflector-client
  neighbor iBGP next-hop-self
  neighbor 24.24.24.24 activate
  neighbor 25.25.25.25 activate
  neighbor 26.26.26.26 activate
  neighbor 55.168.0.253 activate
 exit-address-family

```
R24:

```
router bgp 520
 bgp log-neighbor-changes
 neighbor iBGP peer-group
 neighbor iBGP remote-as 520
 neighbor iBGP update-source Loopback0
 neighbor 23.23.23.23 peer-group iBGP
 neighbor 55.168.0.245 remote-as 2042
 neighbor 55.168.0.249 remote-as 301
 !
 address-family ipv4
  neighbor iBGP next-hop-self
  neighbor 23.23.23.23 activate
  neighbor 55.168.0.245 activate
  neighbor 55.168.0.249 activate
 exit-address-family
```
R25:

```
router bgp 520
 bgp log-neighbor-changes
 neighbor iBGP peer-group
 neighbor iBGP remote-as 520
 neighbor iBGP update-source Loopback0
 neighbor 23.23.23.23 peer-group iBGP
```
R26:

```
router bgp 520
 bgp router-id 26.26.26.26
 bgp log-neighbor-changes
 neighbor iBGP peer-group
 neighbor iBGP remote-as 520
 neighbor iBGP update-source Loopback0
 neighbor 23.23.23.23 peer-group iBGP
 neighbor 55.168.0.229 remote-as 2042
 !
 address-family ipv4
  neighbor 23.23.23.23 activate
  neighbor 55.168.0.229 activate
 exit-address-family

```
### Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.

В данном случае, я на R15 прописал в сторону R14: neighbor 14.14.14.14  next-hop-self, через route-map увеличил атрибут local-preference до 150 в сторону R14. Так же на R22 через route-map увеличил AS-Path.

R15:
```
router bgp 1001
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
 neighbor 14.14.14.14 remote-as 1001
 neighbor 14.14.14.14 update-source Loopback0
 neighbor 53.168.0.254 remote-as 301
 !
 address-family ipv4
  network 15.15.15.15 mask 255.255.255.255
  redistribute connected route-map MSK
  neighbor 14.14.14.14 activate
  neighbor 14.14.14.14 next-hop-self
  neighbor 14.14.14.14 soft-reconfiguration inbound
  neighbor 14.14.14.14 route-map BGP_TRAF_IN out
  neighbor 53.168.0.254 activate
 exit-address-family
!
ip prefix-list TRAF_IN seq 10 permit 0.0.0.0/0 le 32

```
R22:
```
router bgp 101
 bgp router-id 22.22.22.22
 bgp log-neighbor-changes
 neighbor 54.168.0.249 remote-as 301
 neighbor 54.168.0.253 remote-as 1001
 neighbor 55.168.0.254 remote-as 520
 !
 address-family ipv4
  neighbor 54.168.0.249 activate
  neighbor 54.168.0.253 activate
  neighbor 54.168.0.253 route-map as1001 out
  neighbor 55.168.0.254 activate
 exit-address-family
!

route-map as1001 permit 10
 match ip address prefix-list as1001
 set as-path prepend 101 101
```
----------------
### Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.

Так как BGP Multipath требует совпадения AS path, то балансировка нагрузки может выполняться в том случае, если клиент подключен к одному и тому же провайдеру несколькими линками.

В связи с эти используем команду "maximum-paths 2"
R18:
```
router bgp 2042
 bgp router-id 18.18.18.18
 bgp log-neighbor-changes
 neighbor 55.168.0.230 remote-as 520
 neighbor 55.168.0.246 remote-as 520
 !
 address-family ipv4
  network 18.18.18.18 mask 255.255.255.255
  redistribute connected route-map SPB
  neighbor 55.168.0.230 activate
  neighbor 55.168.0.246 activate
  maximum-paths 2
 exit-address-family
```