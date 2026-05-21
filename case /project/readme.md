#  Проектная работа

# Тема: Спутниково-проводная сеть для удалённых геологических баз

##  Спутниково-проводная сеть для удалённых геологических баз — это гибридная система связи, которая объединяет спутниковые технологии (например, VSAT) с проводными каналами для обеспечения надёжного доступа к данным и управления в труднодоступных регионах.


##  Схема сети: спутниково‑проводная сеть для удалённой геологической базы

![](figure1.png)

Компоненты сети

1. Удалённая геологическая база:
   
    • Router_Base (Cisco 1941) — маршрутизатор, выполняющий роль спутникового терминала;
   
    • Switch_Base (Cisco 2960) — коммутатор с поддержкой VLAN;
   
    • PC_Geology1, PC_Geology2 — рабочие станции геологов (подключены к VLAN 20);
   
    • Server_Data — сервер хранения данных (подключён к VLAN 10);
   
    • PC_Guest — гостевая рабочая станция (подключена к VLAN 30).

2. Центральный офис:
   
    • Router_Office (Cisco 1941) — главный маршрутизатор офиса;
   
    • Server_Central — центральный сервер (корпоративная база данных, ГИС).

3. Спутниковый канал: GRE‑туннель между Router_Base и Router_Office.

## Протоколы и сервисы

    • VLAN: сегментация сети на три логических сегмента.
    
    • DHCP: автоматическая раздача IP‑адресов в каждой VLAN.
     
    • GRE‑туннель: имитация спутникового канала между Router_Base и Router_Office.
    
    • Router‑on‑a‑Stick: маршрутизация между VLAN через под‑интерфейсы на Router_Base.

	• ACL:
	
			1) изолировать гостевую сеть (VLAN 30) от рабочих сегментов (VLAN 10, 20);
	
			2) ограничить доступ к серверу данных (Server_Data) только для геологов (VLAN 20);
			
			3) защитить маршрутизаторы от несанкционированных подключений

## Логическая топология и ip-адресация

|Устройство |	Интерфейс	|IP‑адрес / Сеть	| Назначение
|:----------|:----------|:------------|:----------|
|Router_Office|	Gi0/0	|172.16.1.1/24|	Локальная сеть офиса|
| - |	Tunnel0 |	10.0.0.2/24	|GRE‑туннель к базе|
|Router_Base |	Gi0/0.10 (VLAN 10)	| 192.168.10.1/24 |	Управление, серверы|
|- |	Gi0/0.20 (VLAN 20)	| 192.168.20.1/24	Геологи, датчики|
|- |	Gi0/0.30 (VLAN 30)	|192.168.30.1/24	Гостевая сеть|
|- |	Gi0/1	|— (источник туннеля)	|Подключение к спутнику|
|- |	Tunnel0	|10.0.0.1/24|	GRE‑туннель к офису|
|Server_Data	|NIC|	DHCP (из пула 192.168.10.0/24)	|Хранение данных разведки|
|PC_Geology1|	NIC|	DHCP (из пула 192.168.20.0/24)	|Работа геологов|
|PC_Geology2	|NIC|	DHCP (из пула 192.168.20.0/24)	|Работа геологов|
|PC_Guest	|NIC|	DHCP (из пула 192.168.30.0/24)	|Гостевой доступ|
|Server_Central	|NIC	|172.16.1.10/24	|Корпоративная БД, ГИС|
	

## VLAN‑конфигурация на Switch_Base

|Порт коммутатора	|VLAN	|Назначение|
|:----------|:----------|:------------|
|Fa0/1–2	|20 (Geology)	|Рабочие станции геологов|
|Fa0/3	|10 (Management)	|Сервер данных|
|Fa0/4	|30 (Guest)|	Гостевые устройства|
|Fa0/24	|Trunk (10,20,30,1000)	|Соединение с Router_Base|


## Создание сети и настройка параметров устройств

1.Добавим и подключим устройства согласно схеме:

![](figure1.png)

2.Выполним базовую конфигурацию для Router_Office, Router_Base и Switch_Base:

Пример для Router_Office (для Router_Base и Switch_Base аналогично):

```
Router> enable

conf t
no ip domain-lookup
hostname R_Office

enable secret class
line console 0
password cisco
exit
line vty 0 15
password cisco
exit
service password-encryption
banner motd 7 Authorized users only!!!! 7
line console 0
logging synchronous
exit
```

3.Настройте VLAN на Switch_Base:

```
! Создание VLAN
vlan 10
name Management
exit

vlan 20
name Geology
exit

vlan 30
name Guest
exit

vlan 1000
name Native
exit
```

```
! Назначение портов VLAN
interface range fastethernet 0/1-2
switchport mode access
switchport access vlan 20
exit

interface fastethernet 0/3
switchport mode access
switchport access vlan 10
exit

interface fastethernet 0/4
switchport mode access
switchport access vlan 30
exit
```

```
! Настройка trunk‑порта к маршрутизатору

interface fastethernet 0/24
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,30,1000
exit
```

4.Настройте Router_Base:

•Создайте под интерфейсы для VLAN (Gi0/0.10, Gi0/0.20, Gi0/0.30) с инкапсуляцией dot1Q.

```
int gi0/0.10
description Management
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

int gi0/0.20
description Geology
encapsulation dot1Q 20
ip add 1192.168.20.1 255.255.255.0
exit

int gi0/0.30
description Guest
encapsulation dot1Q 30
ip add 192.168.30.1 255.255.255.0
exit

int gi0/0.1000
description Native
encapsulation dot1Q 1000 native
exit
```

```
! Физический интерфейс к коммутатору

int gi0/0/0
description Trunk link to SW_Base
no shut
exit
```

•Настройте GRE туннель (Tunnel0) с IP 10.0.0.1.

```
interface Tunnel0
ip address 10.0.0.1 255.255.255.0
tunnel source gi0/1
tunnel destination 10.0.0.2
exit
```

•Настройте DHCP пулы для каждой VLAN.

```
ip dhcp pool VLAN10_Management
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
exit

ip dhcp pool VLAN20_Geology
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
exit

ip dhcp pool VLAN30_Guest
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.4.4
exit
```

5.Настройте Router_Office:

•Назначьте IP 172.16.1.1 на Fa0/0.

```
interface gi0/0
ip address 172.16.1.1 255.255.255.0
no shutdown
exit
```

•Настройте GRE туннель (Tunnel0) с IP 10.0.0.2.

```
interface Tunnel0
ip address 10.0.0.2 255.255.255.0
tunnel source gi0/1
tunnel destination 10.0.0.1
exit
```

## Проверка конфигурации

•Убедитесь, что ПК получают IP адреса по DHCP.

```
Для PC_Geology1
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: 
Physical Address................: 000B.BE0D.DE57
Link-local IPv6 Address.........: FE80::20B:BEFF:FE0D:DE57
IPv6 Address....................: ::
IPv4 Address....................: 192.168.20.3
Subnet Mask.....................: 255.255.255.0
Default Gateway.................: ::
192.168.20.1
DHCP Servers....................: 192.168.20.1
DHCPv6 IAID.....................: 
DHCPv6 Client DUID..............: 00-01-00-01-10-4B-7B-3A-00-0B-BE-0D-DE-57
DNS Servers.....................: ::
8.8.8.8
Для PC_Geology2
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: 
Physical Address................: 0004.9A57.2140
Link-local IPv6 Address.........: FE80::204:9AFF:FE57:2140
IPv6 Address....................: ::
IPv4 Address....................: 192.168.20.2
Subnet Mask.....................: 255.255.255.0
Default Gateway.................: ::
192.168.20.1
DHCP Servers....................: 192.168.20.1
DHCPv6 IAID.....................: 
DHCPv6 Client DUID..............: 00-01-00-01-CA-60-C7-9B-00-04-9A-57-21-40
DNS Servers.....................: ::
8.8.8.8
Для PC_Guest
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: 
Physical Address................: 0060.2F6B.4713
Link-local IPv6 Address.........: FE80::260:2FFF:FE6B:4713
IPv6 Address....................: ::
IPv4 Address....................: 192.168.30.2
Subnet Mask.....................: 255.255.255.0
Default Gateway.................: ::
192.168.30.1
DHCP Servers....................: 192.168.30.1
DHCPv6 IAID.....................: 
DHCPv6 Client DUID..............: 00-01-00-01-6B-E2-54-7D-00-60-2F-6B-47-13
DNS Servers.....................: ::
8.8.4.4
```

•Выполните ping между PC_Geology1 и Server_Central.

## Выводы и результаты

Эта схема полностью соответствует требованиям проекта:

•реализует VLAN для сегментации трафика (управление, геология, гости);

•использует DHCP для автоматической настройки IP адресов;

•имитирует спутниковый канал через GRE туннель;

•обеспечивает связь между удалённой базой и центральным офисом.

Файл лабораторной работы Cisco PT [здесь](lab11_v1.pkt).
