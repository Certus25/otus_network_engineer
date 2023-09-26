# BGP. Фильтрация

### Цель:

- 1.Настроить фильтрацию для офисе Москва
- 2.Настроить фильтрацию для офисе С.-Петербург


### Описание/Пошаговая инструкция выполнения домашнего задания:

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

- 1.Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).

- 2.Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).

- 3.Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.

- 4.Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.

- 5.Все сети в лабораторной работе должны иметь IP связность.
--------------------
### 1.Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).

R14:
Пропишем на R14 в as-path, access-list 1 с атрибутами ^$, где
^-начало строки
$-конец строки
так как между ними нет ничего означает пустую строку.
Следовтельно, такой префикс матчит только те префиксы, где as-path равен 0.
Дальше сделаем явный deny .* 
.- любой символ
*- повторяеться 0 или любое колличество раз
```
ip as-path access-list 1 permit 7 ^$
ip as-path access-list 1 deny .*

```
```
sh run | sec as-path
```
```
R14-KAK-1001(config)#do sh run | sec as-path
    ip as-path access-list 1 permit 7 ^$
    ip as-path access-list 1 deny .*

```
Разрешаем те as-path у которых автономная система пустая.
```
R14-KAK-1001(config)#route-map MSK permit 5
R14-KAK-1001(config-route-map)#match as-path 1
```

  Аналогично прписываем для R15.

---

### 2.Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).

Идея в том, чтобы префиксы с Москвы не принимались R18 через R26, а префиксы из Чокурдах не принимались R18 через R24. Для проверки я поднял BGP на R28.

```
!
ip prefix-list OUT seq 5 permit 18.18.18.18/32
ip prefix-list OUT seq 10 deny 0.0.0.0/0 le 32
!
route-map BGP_OUT permit 10
 match ip address prefix-list OUT
!
router bgp 2042
 address-family ipv4
  neighbor 55.168.0.246  route-map BGP_OUT out
  neighbor 55.168.0.230  route-map BGP_OUT out
```
### 3.Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.

Настроим с помощью prefix-list и route-map.
R22:
```
ip prefix-list DEF seq 5 permit 0.0.0.0/0
ip prefix-list DEF seq 10 deny 0.0.0.0/0 le 32
router bgp 101
  neighbor 54.168.0.253 activate
  neighbor 54.168.0.253 default-originate
  neighbor 54.168.0.253 prefix-list DEF out

```
И на R14:
```
ip prefix-list DEF1 seq 5 permit 0.0.0.0/0
ip prefix-list DEF1 seq 10 deny 0.0.0.0/0 le 32
router bgp 1001
    neighbor 54.168.0.254 prefix-list DEF1 in

```
### 4.Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.

Настроим с помощью prefix-list.
```
!
ip prefix-list DEFA seq 5 permit 0.0.0.0/0
ip prefix-list DEFA seq 15 permit 18.18.18.18/32 le 32
ip prefix-list DEFA seq 20 deny 0.0.0.0/0 le 32
!
router bgp 301
 address-family ipv4
  neighbor 53.168.0.253 activate
  neighbor 53.168.0.253 default-originate
  neighbor 53.168.0.253 prefix-list DEFA out
```
И на R15:
```
!
ip prefix-list DEFA1 seq 5 permit 0.0.0.0/0
ip prefix-list DEFA1 seq 10 permit 18.18.18.18/32 le 32
ip prefix-list DEFA1 seq 15 deny 0.0.0.0/0 le 32
!
router bgp 1001
 address-family ipv4
  neighbor 53.168.0.254 prefix-list DEF1 in
```