# Лабораторная работа: конфигурация VLAN и настройка Inter-VLAN Routing методом Router-on-a-Stick
# Задание:
- Построение сети и настройка основных параметров устройства
- Создание VLAN и назначение портов коммутатора
- Настройка магистрали 802.1Q между коммутаторами
- Настройка маршрутизации между VLAN на маршрутизаторе
- Проверка работоспособности маршрутизации между VLAN

 # Решение:
 
 # Схема лабораторного стенда:
 ![](https://github.com/Certus25/otus_network_engineer/blob/c29f069819e77a5c7fdacbb5cf60577f1f39fa31/lab1-top.PNG)

 # Таблица адресации:
| Устройство | Интерфейс  |   IP адрес   | Маска подсети | Шлюз по умолчанию |
| :----------|:----------:| :-----------:|:-------------:| -----------------:|
| R1         | e0/0.3   | 192.168.3.1  | 255.255.255.0 |                   |
|            | e0/0.4   | 192.168.4.1  | 255.255.255.0 |                   | 
|            | e0/0.8   |      N/A        |      N/A         |                   | 
| S1         | VLAN3      | 192.168.3.11 | 255.255.255.0 | 192.168.3.1       |
| S2         | VLAN3      | 192.168.3.12 | 255.255.255.0 | 192.168.3.1       |
| PC-A       | NIC        | 192.168.3.3  | 255.255.255.0 | 192.168.3.1       |
| PC-B       | NIC        | 192.168.4.3  | 255.255.255.0 | 192.168.4.1       |

# Таблица VLAN:
|     VLAN      | Имя | Интерфейс |
| :------------ |:---------------:| -----:|
| 3      | Management        | S1: VLAN3 S2: VLAN3 S1: e0/2 |
| 4      | Operations        | S2: e0/1 |
| 7      | ParkingLot        | S1: e0/3, S2: e0/2-3 |
| 8      | Native            |               |


# 1. Конфигурация S1.
- основные настройки коммутатора S1:
``` 
conf t
 hostname S1
 no ip domain lookup
 enable secret class
 line console 0
 password cisco
 login
 exit
 line vty 0 4
 password cisco
 login
 exit
 service password-encryption 
 banner motd # Unauthorized access is prohibited!
 #
 exit
 ```
- настройка транковых портов e0/0 и e0/1:
``` 
int e0/0
 switchport mode trunk
 switchport trunk native vlan 8
 switchport trunk allowed vlan 3,4
 int fa0/1
 switchport mode trunk
 switchport trunk allowed vlan 3,4
 switchport trunk native vlan 8
 ```
- настройка порта доступа e0/2:
```int e0/2
 switchport mode access
 switchport access vlan 3
 ```
Файл конфигурации [здесь]().
 
# 2. Конфигурация S2.
Сделаем основные настройки коммутатора S2, затем настроим порт e0/0 как транковый и e0/1 как порт доступа. Файл конфигурации [здесь]().

# 3. Конфигурация R1.
- основные настройки маршрутизатора R1:
``` 
conf t
 hostname R1
 no ip domain lookup
 enable secret class
 line console 0
 password cisco
 login
 exit
 line vty 0 4
 password cisco
 login
 exit
 service password-encryption
 exit
 copy run start
 clock timezone Moscow 3 0
 int e0/0
 no shut
 exit
 banner motd # Unauthorized access is prohibited!
 #
 ```
- настройка и активация сабинтерфейсов для каждого VLAN:
 ``` 
 int e0/0.3
 des Management
 encapsulation dot1q 3
 ip address 192.168.3.1 255.255.255.0
 int e0/0.4
 des Operations
 encapsulation dot1q 4
 ip address 192.168.4.1 255.255.255.0
 ```
Файл конфигурации [здесь]().

# 4. Проверка связности.
1. Выполните поиск с PC-A на его шлюз по умолчанию.
2. Пинг с ПК-A на ПК-B
3. Пинг с PC-A на S2

![](https://github.com/Certus25/otus_network_engineer/blob/b054bd011ef85cdc7c3196590532760aa9edce4c/VPC-A.PNG) 

Выполните следующий тест с PC-B:

Из командной строки на ПК-B отправьте команду tracert на адрес ПК-A.
![](https://github.com/Certus25/otus_network_engineer/blob/b054bd011ef85cdc7c3196590532760aa9edce4c/VPC-B.PNG)

Промежуточные IP-адреса:
  192.168.4.1 
  192.168.3.3

Видно, что пинги проходят, из этого делаем вывод, что все настроено верно.
