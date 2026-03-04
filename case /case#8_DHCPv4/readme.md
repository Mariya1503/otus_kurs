#  Реализация DHCPv4

![](figure1.png)

###  Задание:

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

Часть 3. Настройка и проверка DHCP-ретрансляции на R2

###  Исходные данные:

## 1 Таблица адресации

| Устройство|	Интерфейс|	IP-адрес|	Маска подсети|	Шлюз по умолчанию|
|:----------|:----------|:----------|:----------|:----------|
|R1|	G0/0/0|	10.0.0.1|	255.255.255.252	|—|
|R1|	G0/0/1|	—	|—|	—|
|R1|	G0/0/1.100|	*192.168.1.1*	| *255.255.255.192* |	—|
|R1|	G0/0/1.200|*192.168.1.65*|*255.255.255.224*|	—|
|R1|	G0/0/1.1000|	—	|—|	—|
|R2|	G0/0	|10.0.0.2	|255.255.255.252|	—|
|R2|	G0/0/1|*192.168.1.97*|*255.255.255.240*|	—|		
|S1|	VLAN 200|*192.168.1.66*| *255.255.255.224*|	*192.168.1.65*|
|S2|	VLAN 1| *192.168.1.98*	|*255.255.255.240*|	*192.168.1.97*|
|PC-A|	NIC|	DHCP|	DHCP|	DHCP|
|PC-B|	NIC|	DHCP|	DHCP|	DHCP|

## 2 Таблица VLAN

|VLAN|	Имя	|Назначенный интерфейс|
|:----------|:----------|:--------|
|1|	Нет|	S2: F0/18|
|100|	Клиенты|	S1: F0/6| 
|200|	Управление|	S1: VLAN 200  |
|999|	Parking_Lot	|S1: F0/1-4, F0/7-24, G0/1-2|
|1000	|Собственная|	—|


###  Решение:

# Часть 1. Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

###  1. Создание схемы адресации

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:

                a. Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
                
```
Подсеть A: 192.168.1.0 / 26
        ◦ диапазон хостов: 192.168.1.1 - 192.168.1.62
        ◦ маска сети: 255.255.255.192
        ◦ количество хостов: 62
```

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 . 
               
                b. Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 

```
Подсеть B: 192.168.1.64 / 27
        ◦ диапазон хостов: 192.168.1.65 - 192.168.1.94
        ◦ маска сети: 255.255.255.224
        ◦ количество хостов: 30
```

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

                c. Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
                
```
Подсеть C: 192.168.1.96 / 28
        ◦ диапазон хостов: 192.168.1.97 - 192.168.1.110
        ◦ маска сети: 255.255.255.240
        ◦ количество хостов: 14
```

Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.

###  2. Создайте сеть согласно топологии.

![](figure2.png)

### 3. Произведите базовую настройку маршрутизаторов.

a. Назначьте маршрутизатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i. Установите часы на маршрутизаторе на сегодняшнее время и дату.

### 4. Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

a. Активируйте интерфейс G0/0/1 на маршрутизаторе.

b. Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
R1(config)#int gi0/0/1.100
R1(config-subif)#
R1(config-subif)#description Clients
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip add 192.168.1.1 255.255.255.192 
R1(config-subif)#exit

R1(config)#int gi0/0/1.200
R1(config-subif)#
R1(config-subif)#description MGNT
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#ip add 192.168.1.65 255.255.255.224 
R1(config-subif)#exit

R1(config)#int gi0/0/1.1000
R1(config-subif)#
R1(config-subif)#description Native
R1(config-subif)#encapsulation dot1Q 1000 native
R1(config-subif)#exit

R1(config)#int gi0/0/1
R1(config-if)#description Trunk link to S1
R1(config-if)#no shut
R1(config-if)#exit
```
```
R1(config)#int g0/0/0
R1(config-if)#ip add 10.0.0.1 255.255.255.252
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2\
```

c. Убедитесь, что вспомогательные интерфейсы работают.

```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   10.0.0.1        YES manual up                    down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.100192.168.1.1     YES manual up                    up 
GigabitEthernet0/0/1.200192.168.1.65    YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
```

### 5. Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

a. Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.

```
R2(config)#int g0/0/1
R2(config-if)#ip add 192.168.1.97 255.255.255.240
R2(config-if)#no shut
```

b. Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.

```
R2(config)#int g0/0/0
R2(config-if)#ip add 10.0.0.2 255.255.255.252
R2(config-if)#no shut
```

c. Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.

```
R2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

d. Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.

```
R1#ping 10.0.0.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

e. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

Файлы конфигурации  [здесь](config_R1.txt) и [здесь](config_R2.txt)

### 6. Настройте базовые параметры каждого коммутатора.

a. Присвойте коммутатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i. Установите часы на маршрутизаторе на сегодняшнее время и дату.

j. Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

Файлы конфигурации  [здесь](config_S1.txt) и [здесь](config_S2.txt)

### 7. Создайте сети VLAN на коммутаторе S1.

a. Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.

b. Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.

c. Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2

d. Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

```
S1# sh run
...
interface FastEthernet0/1
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/3
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/4
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/5
!
interface FastEthernet0/6
 switchport access vlan 100
 switchport mode access
!
interface FastEthernet0/7
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/8
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/9
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/10
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/11
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/12
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/13
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/14
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/15
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/16
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/17
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/18
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/19
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/20
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/21
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/22
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/23
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/24
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet0/1
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan200
 ip address 192.168.1.66 255.255.255.224
!
ip default-gateway 192.168.1.65
!

S1# sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
...
FastEthernet0/5        unassigned      YES manual up                    up 
FastEthernet0/6        unassigned      YES manual up                    up 
...
Vlan1                  unassigned      YES manual administratively down down 
Vlan200                192.168.1.66    YES manual up                    down
```

```
S2#sh run

interface FastEthernet0/1
 shutdown
!
interface FastEthernet0/2
 shutdown
!
interface FastEthernet0/3
 shutdown
!
interface FastEthernet0/4
 shutdown
!
interface FastEthernet0/5
!
interface FastEthernet0/6
 shutdown
!
interface FastEthernet0/7
 shutdown
!
interface FastEthernet0/8
 shutdown
!
interface FastEthernet0/9
 shutdown
!
interface FastEthernet0/10
 shutdown
!
interface FastEthernet0/11
 shutdown
!
interface FastEthernet0/12
 shutdown
!
interface FastEthernet0/13
 shutdown
!
interface FastEthernet0/14
 shutdown
!
interface FastEthernet0/15
 shutdown
!
interface FastEthernet0/16
 shutdown
!
interface FastEthernet0/17
 shutdown
!
interface FastEthernet0/18
 switchport mode access
!
interface FastEthernet0/19
 shutdown
!
interface FastEthernet0/20
 shutdown
!
interface FastEthernet0/21
 shutdown
!
interface FastEthernet0/22
 shutdown
!
interface FastEthernet0/23
 shutdown
!
interface FastEthernet0/24
 shutdown
!
interface GigabitEthernet0/1
 shutdown
!
interface GigabitEthernet0/2
 shutdown
!
interface Vlan1
 ip address 192.168.1.98 255.255.255.240
!
ip default-gateway 192.168.1.97
!

S2#

### 8. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
Откройте окно конфигурации

b. Убедитесь, что VLAN назначены на правильные интерфейсы.

### Вопрос:

### Почему интерфейс F0/5 указан в VLAN 1?

### 9. Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.

a. Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.

b. В рамках конфигурации транка  установите для native  VLAN значение 1000.

c. В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.

d. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

e. Проверьте состояние транка.

### Вопрос:

### Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

# Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.

### 1. Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A

a. Исключите первые пять используемых адресов из каждого пула адресов.

b. Создайте пул DHCP (используйте уникальное имя для каждого пула).

c. Укажите сеть, поддерживающую этот DHCP-сервер.

d. В качестве имени домена укажите CCNA-lab.com.

e. Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.

f. Настройте время аренды на 2 дня 12 часов и 30 минут.

g. Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

 ### 2. Сохраните конфигурацию.

Сохраните текущую конфигурацию в файл загрузочной конфигурации.

### 3. Проверка конфигурации сервера DHCPv4

a. Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool

b. Выполните команду show ip dhcp bindings для проверки установленных назначений адресов DHCP.

c. Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

### 4. Попытка получить IP-адрес от DHCP на PC-A

a. Из командной строки компьютера PC-A выполните команду ipconfig /all.

b. После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

c. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

# Часть 3. Настройка и проверка DHCP-ретрансляции на R2

В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1). 

### 1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

a. Настройте команду ip helper-address на G0/0/1, указав IP-адрес G0/0/0 R1.

b. Сохраните конфигурацию.

### 2. Попытка получить IP-адрес от DHCP на PC-B

a. Из командной строки компьютера PC-B выполните команду ipconfig /all.

b. После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

c. Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

d. Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.

e. Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

Файл лабораторной работы Cisco PT [здесь](lab8_DHCP_IPv4.pkt).

