#  Настройка протоколов CDP, LLDP и NTP

![](figure1.png)

###  Задание:

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP

Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP

Часть 4. Настройка и проверка NTP

###  Исходные данные:

### Таблица адресации

| Устройство|	Интерфейс|	IP-адрес|	Маска подсети| Шлюз по умолчанию |
|:----------|:----------|:----------|:----------|:----------|
|R1|	Loopback1 |	*172.16.1.1*|*255.255.255.0*| - |
|R1|	G0/0/1|	*10.22.0.1*|*255.255.255.0*| - |
|S1|	SVI VLAN 1|*10.22.0.2*| *255.255.255.0*| 10.22.0.1 |
|S2|	SVI VLAN 1|*10.22.0.3*| *255.255.255.0*| 10.22.0.1 |


###  Решение:

# Часть 1. Создание сети и настройка основных параметров устройства


###  1. Создайте сеть согласно топологии.

![](figure2.png)

### 2. Произведите базовую настройку маршрутизатора.

Файлы конфигурации [здесь](config_R1.txt)

### 3. Настройте базовые параметры каждого коммутатора.

Файлы конфигурации [здесь](config_S1.txt) и [здесь](config_S2.txt)

# Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP

### 1. На устройствах Cisco протокол CDP включен по умолчанию. Воспользуйтесь CDP, чтобы обнаружить порты, к которым подключены кабели.

a. На R1 используйте соответствующую команду show cdp, чтобы определить, сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.

```
R1#sh cdp interface
Vlan1 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/0 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/1 is up, line protocol is up
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/2 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
R1#
```
 
#### Вопрос: Сколько интерфейсов участвует в объявлениях CDP? Какие из них активны?

#### протокол CDP работает на всех доступных интерфейсах, активный только один интерфейс - GigabitEthernet0/0/1

b. На R1 используйте соответствующую команду show cdp, чтобы определить версию IOS, используемую на S1.

```
R1#sh cdp entry S1

Device ID: S1
Entry address(es): 
  IP address : 10.22.0.2
Platform: cisco 2960, Capabilities: Switch
Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime: 179

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

advertisement version: 2
Duplex: full
```

#### Вопрос: Какая версия IOS используется на  S1?

##### Version 15.0(2)SE4

c. На S1 используйте соответствующую команду show cdp, чтобы определить, сколько пакетов CDP было выданных.

#### show cdp traffic - не поддерживается версией CPT :(

d. Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше. Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов.

e. На R1 выполните команду show cdp entry S1 . 

```
R1#show cdp entry S1

Device ID: S1
Entry address(es): 
  IP address : 10.22.0.2
Platform: cisco 2960, Capabilities: Switch
Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime: 132

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

advertisement version: 2
Duplex: full 
```

#### Вопрос: Какие дополнительные сведения доступны теперь?

#### я сразу настроила адреса на S1 и S2, поэтому вывод команд не отличается.

f. Отключить CDP глобально на всех устройствах.

```
R1(config)#no cdp run
R1(config)#ex
R1#sh cdp
% CDP is not enabled
```

```
S1(config)#no cdp run
S1(config)#ex
S1#sh cdp
% CDP is not enabled
```
аналогично для S2

# Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP

На устройствах Cisco протокол LLDP может быть включен по умолчанию. Воспользуйтесь LLDP, чтобы обнаружить порты, к которым подключены кабели.

a. Введите соответствующую команду lldp, чтобы включить LLDP на всех устройствах в топологии - *lldp run*

b. На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2. 

```
S1#show lldp entry S2
             ^
% Invalid input detected at '^' marker.
	
S1#show lldp ?
  neighbors  LLDP neighbor entries
  <cr>
S1#show lldp neighbors
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
R1                  Fa0/5          120        R               Gig0/0/1
S2                  Fa0/1          120        B               Fa0/1

Total entries displayed: 2
S1#
```

#### отдельно комадна *show lldp entry S2* не поддерживается версией CPT.

#### Вопрос: Что такое chassis ID для коммутатора S2?

#### идентификатор устройства 

c. Соединитесь через консоль на всех устройствах и используйте команды LLDP, необходимые для отображения топологии физической сети только из выходных данных команды show.

```
R1#sh lldp neighbors
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S1                  Gig0/0/1       120        B               Fa0/5

Total entries displayed: 1
```

```
S1#sh lldp neighbors
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
R1                  Fa0/5          120        R               Gig0/0/1
S2                  Fa0/1          120        B               Fa0/1

Total entries displayed: 2
```

```
S2#sh lldp neighbors
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S1                  Fa0/1          120        B               Fa0/1

Total entries displayed: 1
```
 
# Часть 4. Настройка NTP

В части 4 необходимо настроить маршрутизатор R1 в качестве сервера NTP, а маршрутизатор R2 в качестве клиента NTP маршрутизатора R1. Необходимо выполнить синхронизацию времени для Syslog и отладочных функций. Если время не синхронизировано, сложно определить, какое сетевое событие стало причиной данного сообщения.

### 1. Выведите на экран текущее время.

Введите команду show clock для отображения текущего времени на R1. Запишите отображаемые сведения о текущем времени в следующей таблице.

```
R1#sh clock detail
*0:40:10.165 UTC Mon Mar 1 1993
Time source is hardware calendar
```

| Дата|	Время|	Часовой пояс|	Источник времени| 
|:----------|:----------|:----------|:----------|
|Mar 1 1993|	0:40:10.165 |	не указан |аппаратный календарь (внутренние часы устройства)|
			
### 2. Установите время.

С помощью команды clock set установите время на маршрутизаторе R1. Введенное время должно быть в формате UTC. 

```
R1#sh clock detail
20:51:40.397 UTC Tue Apr 14 2026
Time source is user configuration
```
 
### 3. Настройте главный сервер NTP.

Настройте R1 в качестве хозяина NTP с уровнем слоя 4 - *R1(config)#ntp master 4*
 
### 4. Настройте клиент NTP.

a. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. Запишите текущее время,  в следующей таблице.

b. Настройте S1 и S2 в качестве клиентов NTP. Используйте соответствующие команды NTP для получения времени от интерфейса G0/0/1 R1, а также для периодического обновления календаря или аппаратных часов коммутатора.

```
S1(config)# ntp server 10.22.0.1
```
```
S1#sh clock detail
20:56:57.879 UTC Tue Apr 14 2026
Time source is NTP
```

| Дата|	Время|	Часовой пояс|
|:----------|:----------|:----------|
|Apr 14 2026|	20:56:57.879 |	не указан |

```
S2(config)# ntp server 10.22.0.1
S2(config)# do sh clock detail
20:58:39.93 UTC Tue Apr 14 2026
Time source is NTP
```

### 5. Проверьте настройку NTP.
                
a. Используйте соответствующую команду show , чтобы убедиться, что S1 и S2 синхронизированы с R1.

```
R1# sh clock detail
21:10:7.533 UTC Tue Apr 14 2026
Time source is NTP
```

```
S1#show ntp associations

address         ref clock       st   when     poll    reach  delay          offset            disp
*~10.22.0.1     127.127.1.1     4    15       16      377    0.00           1.00              0.12
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```
S2#show ntp associations

address         ref clock       st   when     poll    reach  delay          offset            disp
*~10.22.0.1     127.127.1.1     4    1        16      377    0.00           0.00              0.12
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

b. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время.

```
S1#show ntp status
Clock is synchronized, stratum 5, reference is 10.22.0.1
nominal freq is 250.0000 Hz, actual freq is 249.9990 Hz, precision is 2**24
reference time is ED604D5E.000003C7 (21:10:54.967 UTC Tue Apr 14 2026)
clock offset is 0.00 msec, root delay is 4.00  msec
root dispersion is 10.45 msec, peer dispersion is 0.12 msec.
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is - 0.000001193 s/s system poll interval is 4, last update was 1 sec ago.
```

```
S2#show ntp status
Clock is synchronized, stratum 16, reference is 10.22.0.1
nominal freq is 250.0000 Hz, actual freq is 249.9990 Hz, precision is 2**24
reference time is 2BA3091B.0000013B (16:58:3.315 UTC Thu May 29 2059)
clock offset is 0.00 msec, root delay is 0.00  msec
root dispersion is 10.92 msec, peer dispersion is 0.12 msec.
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is - 0.000001193 s/s system poll interval is 4, last update was 16 sec ago.
```

#### Вопрос для повторения: Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов?

#### Протоколы обнаружения сетевых ресурсов не следует использовать для интерфейсов, которые используются для подключения к внешним сетям, в целях безопасности.

Файл лабораторной работы Cisco PT [здесь](lab13.pkt).
