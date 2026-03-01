#  Конфигурация безопасности коммутатора

![](figure1.png)

### Цели

### Часть 1. Настройка основного сетевого устройства

### Часть 2. Настройка сетей VLAN

### Часть 3. Настройки безопасности коммутатора

##  Исходные данные:

### Таблица адресации

| Устройство       | Интерфейс      | IP-адрес       | Маска подсети|
|:-----------------|:---------------|:--------------------|:-------|
| R1               | G0/0/1       | 192.168.10.1| 255.255.255.0|
|                  | Loopback 0       | 10.10.1.1| 255.255.255.0|
| S1               | VLAN 10       | 192.168.10.201| 255.255.255.0|
| S2               | VLAN 10       | 192.168.10.202| 255.255.255.0|
| PC-A              | NIC       | DHCP| 255.255.255.0|
| PC-B              | NIC       | DHCP| 255.255.255.0|

###  Решение:

# Часть 1. Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

###  1. Создайте сеть согласно топологии.

![](figure3.png)

### 2. Настройте базовые параметры каждого коммутатора.

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Отключите все неиспользуемые порты.

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

Файлы конфигурации коммутаторов [здесь](config_S1.txt) и [здесь](config_S2.txt)

### 3. Произведите базовую настройку маршрутизаторов.

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Активация IPv6-маршрутизации.

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

Файлы конфигурации коммутаторов [здесь](config_R1.txt) и [здесь](config_R2.txt)

### 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.

a.	Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.

```
R1#sh ipv6 int br
GigabitEthernet0/0/0       [up/up]
    FE80::1
    2001:DB8:ACAD:2::1
GigabitEthernet0/0/1       [up/up]
    FE80::1
    2001:DB8:ACAD:1::1
GigabitEthernet0/0/2       [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
    unassigned
```

```
R2#sh ipv6 int br
GigabitEthernet0/0/0       [up/up]
    FE80::2
    2001:DB8:ACAD:2::2
GigabitEthernet0/0/1       [up/up]
    FE80::1
    2001:DB8:ACAD:3::1
GigabitEthernet0/0/2       [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
```

b.	Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.

*R1(config)#ipv6 route ::/0 2001:DB8:ACAD:2::2*

*R2(config)#ipv6 route ::/0 2001:DB8:ACAD:2::1*

c.	Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1

```
R1#ping 2001:db8:acad:3::1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

d.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

# Часть 2. Проверка назначения адреса SLAAC от R1

В части 2 вы убедитесь, что узел PC-A получает адрес IPv6 с помощью метода SLAAC.

Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6.

Через несколько минут результаты команды ipconfig должны показать, что PC-A присвоил себе адрес из сети 2001:db8:1::/64.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::200:CFF:FE75:7976
   IPv6 Address....................: 2001:DB8:ACAD:1:200:CFF:FE75:7976
   Autoconfiguration IPv4 Address..: 169.254.121.118
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
```

### Откуда взялась часть адреса с идентификатором хоста? *в зависимости от операционной системы. Либо хост генерирует адрес EUI-64 на основе MAC-адреса интерфейса, либо хост генерирует случайный 64-разрядный адрес*

# Часть 3. Настройка и проверка сервера DHCPv6 на R1

### 1. Более подробно изучите конфигурацию PC-A.

a.	Выполните команду ipconfig /all на PC-A и посмотрите на результат.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0030.A364.0607
   Link-local IPv6 Address.........: FE80::230:A3FF:FE64:607
   IPv6 Address....................: 2001:DB8:ACAD:1:230:A3FF:FE64:607
   Autoconfiguration IP Address....: 169.254.6.7
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-35-70-BE-1D-00-30-A3-64-06-07
   DNS Servers.....................: ::
                                     0.0.0.0
```

b.	Обратите внимание, что основной DNS-суффикс отсутствует. Также обратите внимание, что предоставленные адреса DNS-сервера являются адресами «локального сайта anycast», а не одноадресные адреса, как ожидалось.

### 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A.

a.	Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :1, а имя домена — как stateless.com.

```
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATELESS.com
```

b.	Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.

```
R1(config)# interface g0/0/1
R1(config-if)# ipv6 nd other-config-flag 
R1(config-if)# ipv6 dhcp server R1-STATELESS
```

c.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

d.	Перезапустите PC-A.

e.	Проверьте вывод ipconfig /all и обратите внимание на изменения.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: STATELESS.com
   Physical Address................: 0030.A364.0607
   Link-local IPv6 Address.........: FE80::230:A3FF:FE64:607
   IPv6 Address....................: 2001:DB8:ACAD:1:230:A3FF:FE64:607
   Autoconfiguration IP Address....: 169.254.6.7
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 1198766745
   DHCPv6 Client DUID..............: 00-01-00-01-35-70-BE-1D-00-30-A3-64-06-07
   DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: STATELESS.com 
   Physical Address................: 0060.47C2.D438
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 1198766745
   DHCPv6 Client DUID..............: 00-01-00-01-35-70-BE-1D-00-30-A3-64-06-07
   DNS Servers.....................: ::
                                     0.0.0.0
```

f.	Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2.

```
C:\>ping 2001:db8:acad:3::1

Pinging 2001:db8:acad:3::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

Ping statistics for 2001:DB8:ACAD:3::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
    
# Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1

В части 4 настраивается R1 для ответа на запросы DHCPv6 от локальной сети на R2.

a.	Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com.

```
R1(config)# ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATEFUL.com
```

b.	Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1.

```
R1(config)# interface g0/0/0
R1(config-if)# ipv6 dhcp server R2-STATEFUL
```

# Часть 5. Настройка и проверка ретрансляции DHCPv6 на R2.

В части 5 необходимо настроить и проверить ретрансляцию DHCPv6 на R2, позволяя PC-B получать адрес IPv6.

### 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 00D0.D334.4327
   Link-local IPv6 Address.........: FE80::1
   IPv6 Address....................: 2001:DB8:ACAD:3::1
   Autoconfiguration IP Address....: 169.254.67.39
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-06-21-35-51-00-D0-D3-34-43-27
   DNS Servers.....................: ::
                                     0.0.0.0
```

Обратите внимание на вывод, что используется префикс 2001:db8:acad:3::

### 2. Настройте R2 для предоставления DHCPv6 без состояния для PC-B.

a.	Настройте команду ipv6 dhcp relay на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1. Также настройте команду managed-config-flag .

Откройте окно конфигурации

```
R2(config)#ipv6 dhcp pool R2-STATELESS
R2(config-dhcpv6)#dns-server 2001:db8:acad::254
R2(config-dhcpv6)#domain-name STATELESS.com
R2(config-dhcpv6)#ex
R2(config)#interface g0/0/1
R2(config-if)#ipv6 nd other-config-flag
R2(config-if)#ipv6 dhcp server R2-STATELESS

```

b.	Сохраните конфигурацию.

### 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B.

a.	Перезапустите PC-B.

b.	Откройте командную строку на PC-B и выполните команду ipconfig /all и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: STATELESS.com 
   Physical Address................: 0002.4A4B.E764
   Link-local IPv6 Address.........: FE80::202:4AFF:FE4B:E764
   IPv6 Address....................: 2001:DB8:ACAD:3:202:4AFF:FE4B:E764
   Autoconfiguration IP Address....: 169.254.231.100
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 920517970
   DHCPv6 Client DUID..............: 00-01-00-01-D1-37-12-E3-00-02-4A-4B-E7-64
   DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: STATELESS.com 
   Physical Address................: 000A.41BC.A147
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 920517970
   DHCPv6 Client DUID..............: 00-01-00-01-D1-37-12-E3-00-02-4A-4B-E7-64
   DNS Servers.....................: ::
                                     0.0.0.0

```
c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

```
C:\>ping 2001:db8:acad:1::1

Pinging 2001:db8:acad:1::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::1: Destination host unreachable.
Reply from 2001:DB8:ACAD:3::1: Destination host unreachable.
Reply from 2001:DB8:ACAD:3::1: Destination host unreachable.
Reply from 2001:DB8:ACAD:3::1: Destination host unreachable.

Ping statistics for 2001:DB8:ACAD:1::1:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

![](figure2.png)

Файл лабораторной работы Cisco PT [здесь](lab8.pkt).

