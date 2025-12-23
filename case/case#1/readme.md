Лабораторная работа. Базовая настройка коммутатора 

Топология
 
Таблица адресации
Устройство	Интерфейс	IP-адрес / префикс
S1	VLAN 1	192.168.1.2 /24
S1	VLAN 99	
S1	VLAN 99	
PC-A	NIC	192.168.1.10 /24
PC-A	NIC	
PC-A	NIC	
Задачи
Часть 1. Проверка конфигурации коммутатора по умолчанию
Часть 2. Создание сети и настройка основных параметров устройства
•	Настройте базовые параметры коммутатора.
•	Настройте IP-адрес для ПК.
Часть 3. Проверка сетевых подключений
•	Отобразите конфигурацию устройства.
•	Протестируйте сквозное соединение, отправив эхо-запрос.
•	Протестируйте возможности удаленного управления с помощью Telnet.
Общие сведения/сценарий
На коммутаторах Cisco можно настроить особый IP-адрес, который называют виртуальным интерфейсом коммутатора (SVI). SVI или адрес управления можно использовать для удаленного доступа к коммутатору в целях отображения или настройки параметров. Если для SVI сети VLAN 1 назначен IP-адрес, то по умолчанию все порты в сети VLAN 1 имеют доступ к IP-адресу управления SVI. 
В ходе данной лабораторной работы вам предстоит построить простую топологию, используя Ethernet-кабель локальной сети, и получить доступ к коммутатору Cisco, используя консольное подключение и методы удаленного доступа.
Примечание. В лабораторной работе используются коммутаторы Cisco Catalyst 2960s с операционной системой Cisco IOS 15.2(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. 
Необходимые ресурсы
•	1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
•	1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
•	1 консольный кабель для настройки устройства на базе Cisco IOS через консольный порт.
•	1 кабель Ethernet, как показано в топологии.
Часть 1. Создание сети и проверка настроек коммутатора по умолчанию
В первой части лабораторной работы вам предстоит настроить топологию сети и проверить настройку коммутатора по умолчанию.
Шаг 1. Создайте сеть согласно топологии.
a.	Подсоедините консольный кабель, как показано в топологии. На данном этапе не подключайте кабель Ethernet компьютера PC-A.
b.	Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.
Вопрос:
Почему нужно использовать консольное подключение для первоначальной настройки коммутатора?
- данный метод подключения позволяет получить доступ к устройству даже без настройки сетевых сервисов
Почему нельзя подключиться к коммутатору через Telnet или SSH?
- В отличие от консольного подключения для SSH или Telnet -подключений на устройстве должны быть активны сетевые службы, включая активный интерфейс с настроенным адресом.

Шаг 2. Проверьте настройки коммутатора по умолчанию.
На данном этапе вам нужно проверить такие параметры коммутатора по умолчанию, как текущие настройки коммутатора, данные IOS, свойства интерфейса, сведения о VLAN и флеш-память.
К привилегированному набору команд относятся команды пользовательского режима, а также команда configure, при помощи которой выполняется доступ к остальным командным режимам. Введите команду enable, чтобы войти в привилегированный режим EXEC.

a.	Предположим, что коммутатор не имеет файла конфигурации, сохраненного в энергонезависимой памяти (NVRAM). Консольное подключение к коммутатору с помощью Tera Term или другой программы эмуляции терминала предоставит доступ к командной строке пользовательского режима EXEC в виде Switch>. Введите команду enable, чтобы войти в привилегированный режим EXEC.
Откройте окно конфигурации
Обратите внимание, что измененная в конфигурации строка будет отражать привилегированный режим EXEC.

На коммутаторе находится пустой файл конфигурации по умолчанию.
b.	Изучите текущий файл running configuration.
Switch#sh running-config
Building configuration...
Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
no ip address
shutdown
!
!
!
!
line con 0
!
line vty 0 4
login
line vty 5 15
login
!
!
!
!
end
Switch#
Вопросы:
Сколько интерфейсов FastEthernet имеется на коммутаторе 2960? - 24
Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960? - 2
Каков диапазон значений, отображаемых в vty-линиях? - 0-15
c.	Изучите файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM).
Почему появляется это сообщение?
Файл загрузочной конфигурации отображает конфигурацию, которая будет применена на устройстве после перезагрузки. 
d.	Изучите характеристики SVI для VLAN 1.сы:interface Vlan1

Назначен ли IP-адрес сети VLAN 1? — нет
Switch# show mac address-table
Mac Address Table
-------------------------------------------
Vlan Mac Address Type Ports
---- ----------- -------- -----
Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.
Данный интерфейс включен? - отключен
e.	Изучите IP-свойства интерфейса SVI сети VLAN 1.
Вопрос:
Какие выходные данные вы видите? – статус, порты, размер MTU
f.	Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК.


Какие выходные данные вы видите?

g.	Изучите сведения о версии ОС Cisco IOS на коммутаторе.
Вопросы:

Под управлением какой версии ОС Cisco IOS работает коммутатор? - Version 15.0(2)SE4
Как называется файл образа системы? - System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"
h.	Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A.
Switch# show interface f0/6

Вопрос:
Интерфейс включен или выключен? - включен
Что нужно сделать, чтобы включить интерфейс?
Какой MAC-адрес у интерфейса?
Какие настройки скорости и дуплекса заданы в интерфейсе?
i.	Изучите флеш-память.
Выполните одну из следующих команд, чтобы изучить содержимое флеш-каталога.
Switch# show flash 
Switch# dir flash: 
В конце имени файла указано расширение, например .bin. Каталоги не имеют расширения файла.
Switch# show flash
Directory of flash:/

1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)

Вопрос:
Какое имя присвоено образу Cisco IOS? - 2960-lanbasek9-mz.150-2.SE4
Часть 2. Настройка базовых параметров сетевых устройств
Во второй части необходимо будет настроить основные параметры коммутатора и компьютера.
Шаг 1. Настройте базовые параметры коммутатора.
a.	В режиме глобальной конфигурации скопируйте следующие базовые параметры конфигурации и вставьте их в файл на коммутаторе S1. 
no ip domain-lookup
hostname S1
service password-encryption
enable secret class
banner motd # Unauthorized access is strictly prohibited. #
b.	Назначьте IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому вы получите возможность удаленного управления коммутатором.
Прежде чем вы сможете управлять коммутатором S1 удаленно с компьютера PC-A, коммутатору нужно назначить IP-адрес. Согласно конфигурации по умолчанию коммутатором можно управлять через VLAN 1.
c.	Доступ через порт консоли также следует ограничить  с помощью пароля. Используйте cisco в качестве пароля для входа в консоль в этом задании. Конфигурация по умолчанию разрешает все консольные подключения без пароля. Чтобы консольные сообщения не прерывали выполнение команд, используйте параметр logging synchronous.
S1(config)# line con 0
S1(config-line)# logging synchronous 
d.	Настройте каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.
Вопрос:
Для чего нужна команда login? — команда требует ввода пароля при подключении по консоли
 Введите ваш ответ здесь.
йте окно настройки.
Шаг 2. Настройте IP-адрес на компьютере PC-A.
Назначьте компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации.
1)	Перейдите в Панель управления. (Control Panel)
2)	В представлении «Категория» выберите « Просмотр состояния сети и задач».
3)	Щелкните Изменение параметров адаптера на левой панели.
4)	Щелкните правой кнопкой мыши интерфейс Ethernet и выберите «Свойства» .
5)	Выберите Протокол Интернета версии 4 (TCP/IPv4) > Свойства.
6)	Выберите Использовать следующий IP-адрес и введите IP-адрес и маску подсети  и нажмите ОК.
Часть 3. Проверка сетевых подключений
В третьей части лабораторной работы вам предстоит проверить и задокументировать конфигурацию коммутатора, протестировать сквозное соединение между компьютером PC-A и коммутатором S1, а также протестировать возможность удаленного управления коммутатором.
Шаг 1. Отобразите конфигурацию коммутатора.
a.	Используйте консольное подключение на компьютере PC-A для отображения и проверки конфигурации коммутатора. Команда show run позволяет постранично отобразить всю текущую конфигурацию. Для пролистывания используйте клавишу пробела.
S1#sh run
Building configuration...

Current configuration : 1318 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
ip address 192.168.1.2 255.255.255.0
!
banner motd ^C Unauthorized access is strictly prohibited. ^C
!
!
!
line con 0
password 7 0822455D0A16
logging synchronous
login
!
line vty 0 4
password 7 0822455D0A16
login
line vty 5 15
password 7 0822455D0A16
login
!
!
!
!
end


b.	Проверьте параметры VLAN 1.
S1# show interface vlan 1
S1#show interface vlan 1
Vlan1 is up, line protocol is up
Hardware is CPU Interface, address is 00e0.b051.4598 (bia 00e0.b051.4598)
Internet address is 192.168.1.2/24
MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
ARP type: ARPA, ARP Timeout 04:00:00
Last input 21:40:21, output never, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
1682 packets input, 530955 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicast)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
563859 packets output, 0 bytes, 0 underruns
0 output errors, 23 interface resets
0 output buffer failures, 0 output buffers swapped out

Какова полоса пропускания этого интерфейса? - MTU 1500 bytes
Закройте окно настройки.
Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос.
a.	В командной строке компьютера PC-A с помощью утилиты ping проверьте связь сначала с адресом PC-A.
C:\> ping 192.168.1.10

C:\>ping 192.168.1.10

Pinging 192.168.1.10 with 32 bytes of data:

Reply from 192.168.1.10: bytes=32 time=1ms TTL=128
Reply from 192.168.1.10: bytes=32 time=4ms TTL=128
Reply from 192.168.1.10: bytes=32 time=3ms TTL=128
Reply from 192.168.1.10: bytes=32 time=4ms TTL=128

Ping statistics for 192.168.1.10:
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 1ms, Maximum = 4ms, Average = 3ms

b.	Из командной строки компьютера PC-A отправьте эхо-запрос на административный адрес интерфейса SVI коммутатора S1.
C:\> ping 192.168.1.2
C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.2:
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 0ms, Average = 0ms

Шаг 3. Проверьте удаленное управление коммутатором S1.
a.	Откройте Tera Term или другую программу эмуляции терминала с возможностью Telnet. 
b.	Выберите сервер Telnet и укажите адрес управления SVI для подключения к S1.  Пароль: cisco.
c.	После ввода пароля cisco вы окажетесь в командной строке пользовательского режима. Для перехода в исполнительский режим EXEC введите команду enable и используйте секретный пароль class.
d.	Сохраните конфигурацию.
Trying 192.168.1.2 ...Open Unauthorized access is strictly prohibited. 


User Access Verification

Password: 
S1>enable
Password: 
S1#sh run
Building configuration...

Current configuration : 1318 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
ip address 192.168.1.2 255.255.255.0
!
banner motd ^C Unauthorized access is strictly prohibited. ^C
!
!
!
line con 0
password 7 0822455D0A16
logging synchronous
login
!
line vty 0 4
password 7 0822455D0A16
login
line vty 5 15
password 7 0822455D0A16
login
!
!
!
!
end


S1#
Закройте окно настройки.
Вопросы для повторения
1.	Зачем необходимо настраивать пароль VTY для коммутатора?
Чтобы ограничить удаленный доступ к устройству через Telnet или SSH.
2.	Что нужно сделать, чтобы пароли не отправлялись в незашифрованном виде?
Чтобы зашифровать пароли, нужно воспользоваться командой service password-encryption в режиме глобальной конфигурации.

