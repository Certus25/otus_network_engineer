# Настроить IS-IS офисе Триада
- Настроите IS-IS в ISP Триада.

- R23 и R25 находятся в зоне 2222.

- R24 находится в зоне 24.

- R26 находится в зоне 26.

Настройка осуществляется одновременно для IPv4 и IPv6.

--------------------------------------------------------
# Решение:
Схема сегмента сети:
![](https://github.com/Certus25/otus_network_engineer/blob/24c7df12b5922bcae9f7b09e90eaeb2e412a18b8/Lab4/IS-IS.PNG)

--------------------------------------------------------
# 1.Настройка R23 и R25.
Настройку IS-IS произведем на примере маршрутизатора R23. В режиме конфигурации запустим процесс IS-IS и внесем номер зоны и Router-id.
```
R23-KAK-520(config)#router isis
R23-KAK-520(config-router)#net 49.2222.0023.0023.0023.00
R23-KAK-520(config-router)#log-adjacency-changes

```
И запустим на интерфейсах:
```

R23-KAK-520(config-router)#int e0/0
R23-KAK-520(config-if)#ip router isis
R23-KAK-520(config-if)#ipv6 enable
R23-KAK-520(config-if)#ipv6 router isis

R23-KAK-520(config-if)#int e0/1
R23-KAK-520(config-if)#ip router isis
R23-KAK-520(config-if)#ipv6 enable
R23-KAK-520(config-if)#ipv6 router isis

R23-KAK-520(config-if)#int e0/2
R23-KAK-520(config-if)#ip router isis
R23-KAK-520(config-if)#ipv6 enable
R23-KAK-520(config-if)#ipv6 router isis

```
На R25 настройка проводится аналогично.
```

R25-KAK-520(config)#router isis
R25-KAK-520(config-router)#net 49.2222.0025.0025.0025.00
R25-KAK-520(config-router)#log-ad
R25-KAK-520(config-router)#log-adjacency-changes


```

```

R25-KAK-520(config-router)#int e0/0
R25-KAK-520(config-if)#ip router isis
R25-KAK-520(config-if)#ipv6 enable
R25-KAK-520(config-if)#ipv6 router isis

R25-KAK-520(config-if)#int e0/1
R25-KAK-520(config-if)#ip router isis
R25-KAK-520(config-if)#ipv6 enable
R25-KAK-520(config-if)#ipv6 router isis

R25-KAK-520(config-if)#int e0/2
R25-KAK-520(config-if)#ip router isis
R25-KAK-520(config-if)#ipv6 enable
R25-KAK-520(config-if)#ipv6 router isis

R25-KAK-520(config-if)#int e0/3
R25-KAK-520(config-if)#ip router isis
R25-KAK-520(config-if)#ipv6 enable
R25-KAK-520(config-if)#ipv6 router isis


```
--------------------------------------------
# Настройка R24.
В режиме конфигурации запустим процесс IS-IS и внесем номер зоны и Router-id.
```

R24-KAK-520(config)#router isis
R24-KAK-520(config-router)#net 49.0024.0024.0024.0024.00
R24-KAK-520(config-router)#log-adjacency-changes


```
И запустим на интерфейсах:
```

R24-KAK-520(config)#int e0/0
R24-KAK-520(config-if)#ip router isis
R24-KAK-520(config-if)#ipv6 enable
R24-KAK-520(config-if)#ipv6 router isis

R24-KAK-520(config-if)#int e0/1
R24-KAK-520(config-if)#ip router isis
R24-KAK-520(config-if)#ipv6 enable
R24-KAK-520(config-if)#ipv6 router isis

R24-KAK-520(config-if)#int e0/2
R24-KAK-520(config-if)#ip router isis
R24-KAK-520(config-if)#ipv6 enable
R24-KAK-520(config-if)#ipv6 router isis

R24-KAK-520(config-if)#int e0/3
R24-KAK-520(config-if)#ip router isis
R24-KAK-520(config-if)#ipv6 enable
R24-KAK-520(config-if)#ipv6 router isis

```
------------------------------------------------------------
# Настройка R26.
В режиме конфигурации запустим процесс IS-IS и внесем номер зоны и Router-id.

```

R26-KAK-520(config)#router isis
R26-KAK-520(config-router)#net 49.0026.0026.0026.0026.00
R26-KAK-520(config-router)#log-adjacency-changes

```
И запустим на интерфейсах:
```

R26-KAK-520(config-router)#int e0/0
R26-KAK-520(config-if)#ip router isis
R26-KAK-520(config-if)#ipv6 enable
R26-KAK-520(config-if)#ipv6 router isis

R26-KAK-520(config-if)#int e0/1
R26-KAK-520(config-if)#ip router isis
R26-KAK-520(config-if)#ipv6 enable
R26-KAK-520(config-if)#ipv6 router isis

R26-KAK-520(config-if)#int e0/2
R26-KAK-520(config-if)#ip router isis
R26-KAK-520(config-if)#ipv6 enable
R26-KAK-520(config-if)#ipv6 router isis

R26-KAK-520(config-if)#int e0/3
R26-KAK-520(config-if)#ip router isis
R26-KAK-520(config-if)#ipv6 enable
R26-KAK-520(config-if)#ipv6 router isis

```
На примере R23 проверим таблицы маршрутизации IPv4 
![](https://github.com/Certus25/otus_network_engineer/blob/84f7dd85a544f838381733d7ea88f1ddefc8d26c/Lab4/is-is-ip-route.PNG)