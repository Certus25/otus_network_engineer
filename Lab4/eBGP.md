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
 bgp router-id 14.14.14.14
 bgp log-neighbor-changes
 neighbor 54.168.0.254 remote-as 101
 !
 address-family ipv4
  network 14.14.14.14 mask 255.255.255.255
  redistribute connected route-map MSK
  neighbor 54.168.0.254 activate
 exit-address-family

ip prefix-list MSK seq 5 permit 14.14.14.14/32

```

- R15:

```

R15-KAK-1001#sh run | sec bgp
router bgp 1001
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
 neighbor 53.168.0.254 remote-as 301
 !
 address-family ipv4
  network 15.15.15.15 mask 255.255.255.255
  redistribute connected route-map MSK
  neighbor 53.168.0.254 activate
 exit-address-family


ip prefix-list MSK seq 5 permit 15.15.15.15/32


```
### Киторн:

- R22:

```

router bgp 101
 bgp router-id 22.22.22.22
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
 bgp router-id 21.21.21.21
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
 bgp router-id 22.22.22.22
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
 bgp router-id 21.21.21.21
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
 bgp router-id 21.21.21.21
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
 bgp router-id 24.24.24.24
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
 bgp router-id 24.24.24.24
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
 bgp router-id 26.26.26.26
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
 exit-address-family

ip prefix-list SPB seq 5 permit 18.18.18.18/32


```
------------------------
## Организовать IP доступность между офисами Москва и С.-Петербург
На маршунизаторе R18, введем команду 
```
sh ip bgp
```
```
BGP table version is 9, local router ID is 18.18.18.18
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  14.14.14.14/32   55.168.0.246                           0 520 301 101 1001 i
 *>  15.15.15.15/32   55.168.0.246                           0 520 301 1001 i
 *>  18.18.18.18/32   0.0.0.0                  0         32768 i

``` 
Аналогично для маршунизатор R14 и R15:

```
R14-KAK-1001#sh ip bgp
BGP table version is 6, local router ID is 14.14.14.14
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  14.14.14.14/32   0.0.0.0                  0         32768 i
 *>  18.18.18.18/32   54.168.0.254                           0 101 301 520 2042 i

```

```

R15-KAK-1001#sh ip bgp
BGP table version is 6, local router ID is 15.15.15.15
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  15.15.15.15/32   0.0.0.0                  0         32768 i
 *>  18.18.18.18/32   53.168.0.254                           0 301 520 2042 i

```
```
R15-KAK-1001#ping 18.18.18.18 source l0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 18.18.18.18, timeout is 2 seconds:
Packet sent with a source address of 15.15.15.15
!!!!!

```