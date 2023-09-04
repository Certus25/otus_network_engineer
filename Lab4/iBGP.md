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

    15-KAK-1001(config)#router bgp 1001
    R15-KAK-1001(config-router)#bgp log-neighbor-changes
    R15-KAK-1001(config-router)#neighbor 10.0.0.242 remote-as 1001
    R15-KAK-1001(config-router)#neighbor 10.0.0.242 update-source loopback0
    R15-KAK-1001(config-router)#address-family ipv4
    R15-KAK-1001(config-router-af)#neighbor 10.0.0.242 activate
    R15-KAK-1001(config-router-af)#neighbor 10.0.0.242 next-hop-self
    R15-KAK-1001(config-router-af)#exit-address-family

```
- R14:
```

    R14-KAK-1001(config)#router bgp 1001
    R14-KAK-1001(config-router)#bgp log-neighbor-changes
    R14-KAK-1001(config-router)#neighbor 10.0.0.241 remote-as 1001
    R14-KAK-1001(config-router)#neighbor 10.0.0.241 update-source loopback0
    R14-KAK-1001(config-router)#address-family ipv4
    R14-KAK-1001(config-router-af)#neighbor 10.0.0.241 activate
    R14-KAK-1001(config-router-af)#neighbor 10.0.0.241 next-hop-self
    R14-KAK-1001(config-router-af)#exit-address-family

```
