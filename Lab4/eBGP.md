# Задание eBGP:

 - Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас

- Настроить eBGP между провайдерами Киторн и Ламас

- Настроить eBGP между Ламас и Триада

- Настроить eBGP между офисом С.-Петербург и провайдером Триада

- Организовать IP доступность между офисами Москва и С.-Петербург
-----------------

### Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас
-------------

![](https://github.com/Certus25/otus_network_engineer/blob/6a392a09de57acd91ead1ece023cd5b72103b65b/Lab4/eBGP.PNG)


## Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас

### Москва:
- R14:

```

router bgp 1001
 bgp log-neighbor-changes
 neighbor 54.168.0.254 remote-as 101
 !
 address-family ipv4
  redistribute ospf 1
  neighbor 54.168.0.254 activate
 exit-address-family

```

- R15:

```

router bgp 1001
 bgp log-neighbor-changes
 neighbor 53.168.0.254 remote-as 301
 !
 address-family ipv4
  redistribute ospf 1
  neighbor 53.168.0.254 activate
 exit-address-family

```
### Киторн:

- R22:

```

router bgp 101
 bgp log-neighbor-changes
 neighbor 54.168.0.253 remote-as 1001
 !
 address-family ipv4
  neighbor 54.168.0.253 activate
 exit-address-family

```

### Ламас

- R21:

```

router bgp 301
 bgp log-neighbor-changes
 neighbor 53.168.0.253 remote-as 1001
 !
 address-family ipv4
  neighbor 53.168.0.253 activate
 exit-address-family

```

---------------------------

## Настроить eBGP между провайдерами Киторн и Ламас

### Киторн

- R22:

```

router bgp 101
 bgp log-neighbor-changes
 neighbor 54.168.0.249 remote-as 301
 neighbor 54.168.0.253 remote-as 1001
 !
 address-family ipv4
  neighbor 54.168.0.249 activate
  neighbor 54.168.0.253 activate
 exit-address-family

```

### Ламас

- R21:

```

router bgp 301
 bgp log-neighbor-changes
 neighbor 53.168.0.253 remote-as 1001
 neighbor 54.168.0.250 remote-as 101
 !
 address-family ipv4
  neighbor 53.168.0.253 activate
  neighbor 54.168.0.250 activate
 exit-address-family

```
--------------
## Настроить eBGP между Ламас и Триада

### Ламас

- R21:

```

router bgp 301
 bgp log-neighbor-changes
 neighbor 53.168.0.253 remote-as 1001
 neighbor 54.168.0.250 remote-as 101
 neighbor 55.168.0.250 remote-as 520
 !
 address-family ipv4
  neighbor 53.168.0.253 activate
  neighbor 54.168.0.250 activate
  neighbor 55.168.0.250 activate
 exit-address-family
```

### Триада 

- R24:

```
router bgp 520
 bgp log-neighbor-changes
 neighbor 55.168.0.249 remote-as 301
 !
 address-family ipv4
  neighbor 55.168.0.249 activate
 exit-address-family
```

---------
## Настроить eBGP между Триада и С.-Петербург

### Триада 

- R24:

```

router bgp 520
 bgp log-neighbor-changes
 neighbor 55.168.0.245 remote-as 2042
 neighbor 55.168.0.249 remote-as 301
 !
 address-family ipv4
  neighbor 55.168.0.245 activate
  neighbor 55.168.0.249 activate
 exit-address-family

```

- R26:

```
router bgp 520
 bgp log-neighbor-changes
 neighbor 55.168.0.229 remote-as 2042
 !
 address-family ipv4
  neighbor 55.168.0.229 activate
 exit-address-family

```

### Санкт-Петербург

-R18:

```

router bgp 2042
 bgp log-neighbor-changes
 neighbor 55.168.0.230 remote-as 520
 neighbor 55.168.0.246 remote-as 520
 !
 address-family ipv4
  neighbor 55.168.0.230 activate
  neighbor 55.168.0.246 activate
 exit-address-family

```