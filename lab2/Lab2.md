# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
# Задание:
 Часть 1. Создание сети и настройка основных параметров устройства

 Часть 2. Выбор корневого моста

 Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

 Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов


 # Решение:
 
 # Схема лабораторного стенда:
 ![](https://github.com/Certus25/otus_network_engineer/blob/11e33e371afc65a65f7784a17a9fb60e750a14ca/shema.png)

 # Таблица адресации:
| Устройство | Интерфейс  |   IP адрес   | Маска подсети |
| :----------|:----------:| :-----------:|:-------------:|
| S1         | VLAN 1   | 192.168.1.1  | 255.255.255.0   |
| S2         | VLAN 1   | 192.168.1.2  | 255.255.255.0   |
| S3         | VLAN 1   | 192.168.1.3  | 255.255.255.0   | 


# 1. Базовая конфигурация устройств.
Сделаем основные настройки коммутаторов S1, S2, S3:
```
conf t
 #hostname S1
 #no ip domain lookup
 #enable secret cisco
 #line console 0
 #password cisco
 #login
 #logging synchronous 
 #line vty 0 15
 #password cisco
 #login
 #logging synchronous 
 #exit
 service password-encryption 
 banner motd # Unauthorized access is prohibited!
 #
 end
 ```
Конфигурации находятся по ссылкам: [S1](https://github.com/Certus25/otus_network_engineer/blob/11e33e371afc65a65f7784a17a9fb60e750a14ca/lab2/config%20S1), [S2](https://github.com/Certus25/otus_network_engineer/blob/11e33e371afc65a65f7784a17a9fb60e750a14ca/lab2/config%20S2), [S3](https://github.com/Certus25/otus_network_engineer/blob/11e33e371afc65a65f7784a17a9fb60e750a14ca/lab2/config%20S3).

# 2. Определение корневого моста и состояния портов.
На всех трех коммутаторах введем команду show spanning-tree, чтобы определить корневой мост. Из предоставленных данных видно, что корневым мостом является коммутатор S1.
![](https://github.com/Certus25/otus_network_engineer/blob/525781df770198916408c6a3cb856fe006f3b689/shema.png)
На схеме ниже определены состояния всех подключенных портов.
![](https://github.com/Certus25/otus_network_engineer/blob/525781df770198916408c6a3cb856fe006f3b689/shema1.png)

# 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.
С помощью команды show spanning-tree на коммутаторе S3 определим корневой и заблокированный порт:
![](https://github.com/Certus25/otus_network_engineer/blob/b117a2354a661f15118701b787cc53fbfe93a17a/S3%20spanning-tree.png)
Порт e0/0 является корневым, порт  e0/2 - заблокированными. Далее с помощью команды spanning-tree vlan 1 cost 18 изменим стоимость порта e0/2:
```
int e0/2
spanning-tree vlan 1 cost 18
```
После этого заметим, что топология протокола перестроилась и порт e0/0 стал назначенным. Так же заметно, что на коммутаторе S2 стал заблокированным порт e0/2. Так произошло, потому что из сегмента сети между S2 и S3 теперь стоимость пути до S1 меньше через S3.
![](https://github.com/Certus25/otus_network_engineer/blob/469bc01612084ee89de2ca7c61383d29afbee97b/S2-spanning-tree%2018.png)
![](https://github.com/Certus25/otus_network_engineer/blob/469bc01612084ee89de2ca7c61383d29afbee97b/S3%20spanning-tree%2018.png)
Удаление записи стоимости порта e0/2:
```
int e0/2
no spanning-tree vlan 1 cost 18
```

# 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета порта.
После активации портов e0/1 и e0/3, протокол STP перестроил топологию. Теперь на коммутаторе S2 порта e0/2 стал корневым, а e0/3 - заблокирован.
![](https://github.com/Certus25/otus_network_engineer/blob/b117a2354a661f15118701b787cc53fbfe93a17a/S2-spanning-tree%200-3.png)
На коммутаторе S3 порт e0/0 стал корневым, а e0/1-3 - заблокирован.
![](https://github.com/Certus25/otus_network_engineer/blob/b117a2354a661f15118701b787cc53fbfe93a17a/S3%20spanning-tree%200-3.png)
Так произошло, потому что протокол выбрал порт с наименьшим значение - корневым.


