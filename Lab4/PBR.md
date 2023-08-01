Цель:

    Настроить политику маршрутизации в офисе Чокурдах
    Распределить трафик между 2 линками


Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

    1.Настроите политику маршрутизации для сетей офиса.

    2.Распределите трафик между двумя линками с провайдером.

    3.Настроите отслеживание линка через технологию IP SLA.(только для IPv4)

    4.Настройте для офиса Лабытнанги маршрут по-умолчанию.
--------------------------------------------------
# 1.Настроите политику маршрутизации для сетей офиса.

- Настройки маршунизатора R28
    ```
        interface Loopback0
        description 4okurdax-R28
         ip address 10.0.100.28 255.255.255.255
        !
        interface Ethernet0/0
        ip address 55.168.0.233 255.255.255.252
        !
        interface Ethernet0/1
        ip address 55.168.0.237 255.255.255.252
        !
        interface Ethernet0/2
        description trunk link SW29
        no ip address
        !
        interface Ethernet0/2.30
        description Client-VPC30
        encapsulation dot1Q 30
        ip address 10.0.30.254 255.255.255.0
        ip policy route-map VLAN30
        !
        interface Ethernet0/2.31
        encapsulation dot1Q 31
        ip address 10.0.31.254 255.255.255.0
        ip policy route-map VLAN31
        !
        interface Ethernet0/2.200
        description MGMT
        encapsulation dot1Q 200
        ip address 10.0.100.254 255.255.255.252
    ```

- Настройка коммутатора SW29

    ```
        interface Loopback0
        description 4okurdax-Sw29
        ip address 10.0.100.29 255.255.255.255
        !
        interface Ethernet0/0
        switchport access vlan 30
        switchport mode access
        !
        interface Ethernet0/1
        switchport access vlan 31
        switchport mode access
        !
        interface Ethernet0/2
        switchport access vlan 200
        switchport trunk allowed vlan 30,31,200
        switchport trunk encapsulation dot1q
        switchport trunk native vlan 1000
        switchport mode trunk
        !
        interface Ethernet0/3
        !
        interface Vlan30
        no ip address
        !
        interface Vlan200
        ip address 10.0.100.253 255.255.255.252
        !
        ip default-gateway 10.0.100.254

    ```

    ```
    
        1    default                          active    Et0/3
        30   LAN-VPC30                        active    Et0/0
        31   LAN-VPC31                        active    Et0/1
        200  MGMT                             active
        999  ParkingLot                       active
        1000 Native                           active
        1002 fddi-default                     act/unsup
        1003 token-ring-default               act/unsup
        1004 fddinet-default                  act/unsup
        1005 trnet-default                    act/unsup

    ```
    VPC30:

        ```
            VPCS> dhcp
            DDORA IP 10.0.30.2/24 GW 10.0.30.254

        ```
    
    VPC31:

    ```
        VPCS> dhcp DDORA IP 10.0.31.2/24 GW 10.0.31.254

    ```
--------------------------------------------------
# 2.Распределите трафик между двумя линками с провайдером.

```
    route-map VLAN30 permit 10
    match ip address 130
    set ip next-hop 55.168.0.234
    !
    route-map VLAN31 permit 10
    match ip address 131
    set ip next-hop 55.168.0.238
    !
    !
    access-list 130 permit ip 10.0.30.0 0.0.0.255 any
    access-list 131 permit ip 10.0.31.0 0.0.0.255 any

    track 1 ip sla 130 reachability
    !
    track 2 ip sla 131 reachability

    ip route 0.0.0.0 0.0.0.0 55.168.0.234 track 1
    ip route 0.0.0.0 0.0.0.0 55.168.0.238 track 2
    ip route 0.0.0.0 0.0.0.0 10.0.100.253
    ip route 0.0.0.0 0.0.0.0 55.168.0.238 10
    ip route 0.0.0.0 0.0.0.0 55.168.0.234 10


```

--------------------------------------------------

# 3.Настроите отслеживание линка через технологию IP SLA.(только для IPv4)

```
    R28(config)#ip sla 1
    R28(config-ip-sla)#icmp-echo 55.168.0.234 source-ip 55.168.0.233
    R28(config-ip-sla-echo)#frequency 10
    R28(config-ip-sla-echo)#exit
    R28(config)#ip sla schedule 1 life forever start-time now

    R28(config)#ip sla 2
    R28(config-ip-sla)#icmp-echo 55.168.0.238 source-ip 55.168.0.237
    R28(config-ip-sla-echo)#frequency 10
    R28(config-ip-sla-echo)#exit
    R28(config)#ip sla schedule 2 life forever start-time now
    R28(config)#sh ip sla st
    R28(config)#exit
```

Проверим отслеживания трафика:

```
    R28#sh ip sla statistics
    IPSLAs Latest Operation Statistics
```

```
    IPSLA operation id: 1
            Latest RTT: 2 milliseconds
    Latest operation start time: 22:54:20 UTC Sun Jul 30 2023
    Latest operation return code: OK
    Number of successes: 8
    Number of failures: 0
    Operation time to live: Forever



    IPSLA operation id: 2
         Latest RTT: 1 milliseconds
    Latest operation start time: 22:54:27 UTC Sun Jul 30 2023
    Latest operation return code: OK
    Number of successes: 4
    Number of failures: 0
    Operation time to live: Forever
    
```

![](https://github.com/Certus25/otus_network_engineer/blob/0099a457852f46ab7a2ede44a9bd3080ca8283f1/Lab4/PBR30.PNG)
![](https://github.com/Certus25/otus_network_engineer/blob/0099a457852f46ab7a2ede44a9bd3080ca8283f1/Lab4/PBR31.PNG)
--------------------------------------------------

# 4.Настройте для офиса Лабытнанги маршрут по-умолчанию.

```
   R27-Labitnangi(config)#ip route 0.0.0.0 0.0.0.0 55.168.0.242

```