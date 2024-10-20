# Основы TCP/IP сетей. Настройка и диагностика сети

## Сети на основе стека Internet протоколов

- Современные компьютерные сети строятся на базе стека протоколов семейства TCP/IP
- Разработка стандартов, известных как Request for Comments (RFC), ведется организацией Инженерный Совет Интернета
  (Internet Engineering Task Force, IETF). Описания протоколов доступны по адресу https://www.rfc-editor.org/rfc
- Согласно rfc1122, стек Internet протоколов состоит из 4-х уровней:
    - Прикладной уровень (Application Level), протоколы которого разделяются на две категории
        - пользовательские протоколы (SSH, Telnet, FTP, SMTP)
        - протоколы поддержки (DNS, BOOTP, SNMP).
    - Транспортный уровень (Transport Level) - протоколы транспортного уровня обеспечивают взаимодействие между сетевыми
      приложениями.
      На транспортном уровне определяются адреса сетевых приложений в рамках хоста - порты.
      Описаны два протокола данного уровня:
        - TCP (Transmission Control Protocol)
        - UDP (User Datagram Protocol)
    - Межсетевой уровень (Internet Level)
        - На межсетевом уровне данные передаются от одного хоста другому (хосты находятся в разных сетях).
        - За маршрутизацию отвечает основной протокол данного уровня Internet Protocol (IP).
        - На этом же уровне определены управляющие протоколы ІСМР и IGMP и маршрутизирующие протоколы RIP, OSPF, BGP и
          др.
    - Канальный уровень (Link Layer)|
        - Канальный уровень в модели ТСР/Р подробно не описывается, протоколы в рамках модели не конкретизируются.

## IP адреса

- Каждому сетевому интерфейсу должен быть присвоен IP адрес
- IР адрес можно назначить статически, либо динамически, с помощью служб DHCP или ВООТР
- Существует две версии IP протокола, которым соответствуют разные форматы адресов:
    - IPv4 - четыре байта. Записывается в виде 4-х десятичных чисел, разделенных точками. В сетях IPv4 поддерживаются
      три типа адресов:
        - unicast - адрес хоста
        - broadcast - адрес всех хостов в сети
        - multicast - адрес группы хостов
    - IPv6 - шестнадцать байтов (128 бит). Записывается в виде 16-ти чисел в шестнадцатеричном формате, разделяемых
      двоеточиями
- IP адрес состоит из двух частей:
    - номер сети
    - номер хоста в этой сети
- Первый адрес в сети - адрес самой сети, последний адрес - адрес всех хостов в данной сети.

## Классы IP адресов

- ІР адреса разделены на 5 классов: A, B, C, D, E:
    - Класс А - для номера сети используется первый байт, первый бит - 0
    - Класс В - для номера сети используются первые два байта, первые 2 бита - 10
    - Класс С - для номера сети используются первые три байта, первые 3 бита - 110
    - Класс D предназначен для поддержки групповой рассылки данных, первые 4 бита - 1110
    - Класс Е зарезервирован, первые 4 бита - 1111

- Согласно RFC 1700 выделены специальные IP адреса:
    - <номер сети, 0> - обозначает всю сеть
    - <номер сети, все 1> - широковещательный адрес сети (используется только как адрес назначения)
    - <все 1, все 1> - локальная рассылка (рассылка внутри своей сети, используется только как адрес назначения)
    - <все 0, все 0> - текущий хост в текущей сети (используется только как адрес источника)
    - <127, произвольно> - loopback (любой пакет, направленный на этот адрес, не достигая передающей среды, передается
      локальному сетевому приложению)

## Адреса для частных сетей

- RFC 1597 и 1918 определяют понятие частных (private) сетей
- Приватные сети предназначены для:
    - хостов, которым не нужен доступ к публичной сети Интернет
    - хостов, изолированных от внешней сети шлюзом прикладного уровня (прокси)
- IANA (Internet Assigned Numbers Authority) выделила три блока адресов для частных сетей:
    - 10.0.0.0 - 10.255.255.255 - одна сеть класса А
    - 172.16.0.0 - 172.31.255.255 - 16 сетей класса В
    - 192.168.0.0 - 192.168.255.255 - 256 сетей класса С
- Блок адресов 169.254.0.0/16 выделен для локальной коммуникации между хостами в рамках одного сетевого сегмента
  (Link-Local addresses). Адреса из данного блока предназначены для автоматической настройки сетевых интерфейсов в
  случае, если отсутствует сервер DHCP (rfc 3330, rfc 5735).

## Методы для гибкого управления пространством IP- адресов

- Для преодоления недостатков схемы адресации, базирующейся на классах, было предложено следующее:
- разбиение на подсети (subnetting) и сетевая маска
- Сетелы маски переменной длины (VLSM, Variable Length Subnet Mask)
- бесклассовая адресация (CIDR, Classless Inter-Domain Routing)

## Подсети

- Концепция подсети (subnet), которая позволяет разбивать сети класса А, В и С на более мелкие сети, была введена RFC
  917 и 950
- В рамках концепции подсети, адрес из любой сети класса А, В или С состоит из трех частей: номера сети, номера подсети,
  номера хоста
- Номер подсети и номер хоста содержатся в номере хоста исходной сети
- Номер сети и номер подсети образуют вместе расширенный сетевой префикс
- Для выделения сетевого префикса и номера хоста используется маска подсети
- Маска подсети - это 32-битовое число. Биты маски, равные 1, выделяют биты в адресе, используемые в качестве сетевого
  префикса. Биты маски, равные 0, используются для нумерации хостов.

## CIDR и VLSM

- B RFC 1009 определен механизм поддержки нескольких масок в одной сети. Механизм получил название масок подсетей
  переменной длины (VLSM) и позволяет разбивать сеть на подсети разных размеров
- B RFC 1517, 1518, 1519, 1520 был описан механизм бесклассовой междоменной маршрутизации CIDR
- Идеология CIDR заключается в:
    - отказе от классовой адресации
    - агрегировании маршрутных данных
    - концепции суперсетей

## Основные проктоколы семейства TCP/IP

- протокол определения аппаратных адресов (ARP, Address Resolution Protocol), выполняющий определение аппаратных
  (Ethernet) адресов по известным логическим (ІР) сетевым адресам
- межсетевой протокол (IP, Internet Protocol) обеспечивает доставку с одной машины на другую без дополнительной
  обработки данных
- протокол управления передачей данных(TCP, Transmission Control Protocol), который отвечает за гарантированную доставку
  данных между сетевыми процессами
- протокол пользовательских датаграмм (UDP, User Datagram Protocol), который обеспечивает негарантированную пересылку
  данных между сетевыми процессами
- межсетевой протокол управления сообщениями ICMP (Internet Control Message Protocol) отвечает за низкоуровневую
  поддержку работы протокола IP: сообщения об ошибках, содействие в маршрутизации и т.п.

## Address Resolution Protocol (ARP)

- Протокол ARP (RFC 826) предназначен для автоматического определения аппаратного адреса (МАС-адреса) по заданному
  IP-адресу
- Информация о соответствии IP-адресов МАС-адресам хранится в памяти ядра в ARP-таблице (ARP cache)
- Записи в ARP таблице могут быть статическими и динамическими
- Команды `ip` `neigh` и `агр` позволяют просматривать, добавлять и удалять записи в ARP таблице
- Параметры для настройки сетевого модуля, реализующего протокол ARP, находятся в `/proc/sys/net/ipv4/neigh/default`
- ARP протокол не является маршрутизируемым (ARP-пакеты не выходят за пределы локального сетевого сегмента)

## Internet Protocol (IP)

- IP-датаграмма состоит из заголовка и данных. Данные поступают (инкапсулируются) от протоколов верхнего уровня (ТСР,
  UDP, ICMP и др.)
- Если размер IP-датаграмммы больше, чем максимальный размер данных в кадре канального уровня (MTU, maximum transmission
  unit), то IP-датаграмма разбивается на IP-фрагменты для последующей инкапсуляции в кадры канального уровня

### Метки безопасности в IP-датаграммах

- Классификационные метки (уровни конфиденциальности и категории) помещаются в дополнительную часть IP-заголовка - опцию
  типа базовая безопасность (Basic Security Option)
- В случае отсутствия в заголовке IP-датаграммы опции безопасности считается, что информация передается с нулевым
  уровнем конфиденциальности и без категорий

## Transmission Control Protocol

- Протокол ТСР (RFC 793, 1122, 3168, 6093, 6528) ориентирован на установление соединения между сетевыми процессами и
  надежную доставку данных от одного сетевого процесса другому
- Модуль ТСР-протокола:
    - принимает данные из модулей протоколов верхнего прикладного уровня, при этом данные воспринимаются как
      неструктурированный поток байтов
    - разбивает данные на сегменты
    - добавляет в каждый сегмент ТСР-заголовок
    - отправляет ТСР-сегменты в модуль IP-протокола
- В ТСР-заголовок помещаются номер порта отправителя и номер порта получателя
- Порты классифицируются следующим образом (rfc6335):
    - общеизвестные (системные) (0 - 1023) - зарезервированы IANA для популярных сетевых служб
    - зарегистрированные (пользовательские) (1024 - 49151) - используются для сетевых служб, данные порты выделяются и
      регистрируются IANA
    - динамические (49152-65535) - используются клиентскими сетевыми процессами
- Соответствие имен сетевых служб и номеров портов хранится в файле `/etc/services`.
- У становление ТСР-соединения (сокета) происходит путем троекратного обмена специальными сегментами, использующими
  флаги SYN и АСК, которые устанавливаются в ТСР-заголовке (процедура тройного рукопожатия)

### Состояния TCP-соединений

- Типовая последовательность состояний ТСР-соединения со стороны службы (сервера):
    - LISTEN - ожидание запроса от клиента на установление соединения
    - SYN_RCVD - получен запрос от клиента на установление соединения
    - ESTABLISHED - соединение установлено, передаются данные
    - CLOSE_WAIT - клиенту отправлено подтверждение о получении запроса о завершения соединения, продолжается передача
      данных клиенту
    - LAST_ACK - передача данных клиенту прекращено, клиенту отправлен запрос о завершении соединения
    - CLOSED - поступило подтверждение о получении клиентом запроса о завершении соединения, соединение закрыто

- Типовая последовательность состояний ТСР-соединения со стороны клиента:
    - CLOSED - начальное состояние клиента
    - SYN_SENT - клиент отправил запрос на установление соединения с сервером и ожидает подтверждения
    - ESTABLISHED - соединение установлено, передаются данные
    - FIN_WAIT_1 - клиент завершает соединение
    - FIN_WAIT_2 - получено подтверждение от сервера, ожидается запрос от сервера о завершении соединения
    - TIME_WAIT - получен запрос от сервера о завершении соединения, отправлено подтверждение серверу, клиент ждет `2*
      MSL` секунд (Maximum Segment Lifetime), все данные, поступившие от сервера во время ожидания, отбрасываются, что
      позволяет избавить новое соединение от данных, полученных из старого соединения.

## User Datagram Protocol (UDP)

- Протокол UDP (User Datagram Protocol, rfc 768) не устанавливает соединение
- UDP не пытается упорядочить данные, упорядочивание данных возлагается на приложение
- Протокол UDP (также как и ТСР) может различать прикладные процессы по номерам портов
- UDP поддерживается проверка контрольной суммы данных
- UDP быстрее и эффективней, чем ТСР, но уступает в надежности. Также могут возникать проблемы с перегрузкой

## Протокол ICMP (Internet Control Message Protocol, RFC 792)

- предназначен для обмена информацией сетевого уровня между хостами, как правило, для передачи сообщений об ошибках и
  исключительных ситуаций
- ІСМР работает поверх IP протокола, помещая ІСМР сообщения внутрь ІР пакета.

## Основные протоколы уровня приложений

- DNS
- DHCP
- SMTP, POP, IMAP
- SSH
- NTP
- NFS
- CIFS
- FTP, TFTP, Telnet
- IPP

## Именование сетевых интерфейсов

- Традиционные имена для Ethernet интерфейсов: ethX
- Предсказуемые имена для Ethernet интерфейсов:
    - B Astra Linux нужно убрать параметр `net.ifnames=0,` передаваемый ядру (`etc/default/grub`)
    - Политики именования:
        - BIOS - enoX
        - PCIe - ensX
        - Физическое расположение - enpXsY
        - МАС-адрес - еnхХХХХХХХХХХХХ
    - Префиксы для других сетевых интерфейсов (ib, sl, wl, ww)

## Настройка сетевых интерфейсов

- NetworkManager:
    - `NetworkManager.service`
    - `nmcli`, `nmtui`, `nm-connection-editor`
- ifup/ifdown команды:
    - networking.service
    - `/etc/network/interfaces`
- Команды для ручной настройки сетевых интерфейсов
- Просмотр настроек: `ip address show` или `ifconfig -а`
- Изменение настроек: `ip address add A.B.C.D/M dev ethO` или `ifconfig ethX A.B.C.D/M`

## Настройка с помощью NetworkManager

- Конфигурационный файл `/etc/NetworkManager/NetworkManager.conf`
- Основные типы объектов:
    - `device` и `connection`
    - объект `device` является частью объекта `connection`
    - каждый объект имеет свой набор свойств
- Просмотр настроек: команда `show`
    - `nmcli device show имя_интерфейса`
    - `nmcli connection show имя_соединения`
- Создание/изменение соединений: команды `add`/`modify`
    - `nmcli con mod Проводное\ соединение\ 1 ipv4.addresses A.B.C.D/M ipv4.method manual`

Изменить настройки соединение можно также с помощью:
- графической утилиты Сетевые соединения (`Advanced Networking Configuration`), которую можно вызвать из Панели управления
  (Control center) или командой `nm-connection-editor`
- псевдографической утилиты `nmtui`

## Настройка с помощью ifup/ifdown команд

- Команды: `ifup`, `ifdown`, `ifquery`
- Служба `networking.service`
- Конфигурационный файл `/etc/network/interfaces`
    - `auto ethX` - активация интерфейса при загрузке
    - `iface ethX inet dhcp/static` - метод получение адреса (dhcp или статика)
    - `address A.B.C.D/M` - статическое определение адреса/маски
    - `gateway A.B.C.D` - статическое определение шлюза
- В случае, если настройка сетевых интерфейсов будет производиться через `ifup`/`ifdown` команды, то рекомендуется
  отключить службу `NetworkManager`: `systemctl mask NetworkManager`

## Агрегирование (объединение) Ethernet интерфейсов

- Агрегирование сетевых интерфейсов - это объединение нескольких физических интерфейсов в один логический
- Агрегирование сетевых интерфейсов позволяет:
    - увеличить пропускную способность (но не скорость) каналов передачи данных за счет различных методов балансировки
      нагрузки
    - повысить отказоустойчивость
- Основные режимы агрегирования:
    - balance-rr (0) - циклический (карусельный) — баланс., отказ
    - active-backup (1) - активный-резервный — отказ
    - balance-xor (2) - каждый интерфейс работает со своим получателем - баланс., отказ
    - balance-tlb (5) - исходящий трафик распределяется исходя из загруженности каждого интерфейса (transmit load
      balancing)
    - balance-alb (6) - входящий и исходящий трафики распределяются исходя из загруженности каждого интерфейса (adaptive
      load balancing)]

## Маршрутизация

- Маршрутизация позволяет составить путь для доставки сетевых (IP) пакетов от хоста-отправителя, находящегося в одной
  сети, хосту-получателю, расположенному в другой сети
- Таблица маршрутизации - это структура данных, хранящаяся в ядре и содержащая информацию о доступных путях в сети
  (`netstat -r`, `ip route`, `route`)
- Два типа маршрутизации в IP сетях: статическая и динамическая (внутренняя (`interior`) и внешняя (`exterior`))
- В корпоративных сетях используют протоколы внутренней динамической маршрутизации: RIP (Routing Information Protocol) и
  OSPF (Open Shortest Path First) (bird, quagga)
- Алгоритм построения маршрута:
    - перед отсылкой пакета хост-отправитель определяет по маске сети, в какой сети находится хост-получатель
    - если хост-получатель находится в той же сети, что и хост-отправитель, то пакет доставляется с использованием
      механизмов уровней ниже сетевого (IP)
    - в этом случае используется ARP протокол для определения аппаратного (МАС) адреса, соответствующего IP адресу
      хоста-получателя
    - если хост-получатель находится в другой сети, то хост-отправитель определяет, на какой хост-маршрутизатор следует
      отправить пакет
    - когда маршрутизатор получает пакет, то он по таблице маршрутов (маршрутизации) определяет, в какую сеть данный
      пакет должен быть направлен
    - если маршрутизатор не может найти маршрут, по которому следует отправить пакет, то хосту-источнику отправляется
      ІСМР сообщение "Destination Unreachable", а сам пакет уничтожается (отбрасывается)
    - если определен так называемый маршрут по умолчанию (default route), то пакет не отбрасывается, а направляется по
      этому маршруту

- Во время доставки до хоста-получателя пакет может проходить через несколько маршрутизаторов. Каждый шаг в таком
  процессе (т.е. прохождение каждого маршрутизатора) называется хопом (hop, прыжок)
- При статической маршрутизации маршруты устанавливаются и изменяются администратором системы
- В случае динамической маршрутизации таблица маршрутизации конструируется и перестраивается динамически. Динамические
  маршруты вычисляются в режиме реального времени, в зависимости от сетевых условий
- Маршрутизируемый (routed) протокол - протокол, пакеты которого могут пересылаться в другие сети. (Например, IP
  протокол)
- Маршрутизирующий (routing) протокол - протокол, который определяет, куда нужно направить пакеты маршрутизируемого
  протокола

### Статическая маршрутизация

- Путь по умолчанию - статический маршрут, который используется, когда другие правила маршрутизации не срабатывают
- Для обозначения маршрута по умолчанию используются `default` или `0.0.0.0`
- Для временного определения статических маршрутов можно использовать команды `ip route` или `route`
- Постоянное определение статических маршрутов:
    - в команде `nmcli` используются параметры `ipv4.gateway` для шлюза по умолчанию и `ipv4.routes` - для статических
      маршрутов
    - в файле `/etc/network/interfaces` - GATEWAY - для шлюза по умолчанию и `post-up`/`pre-down` команда - для
      установки статических маршрутов

## Типовые проблемы, связанные с сетевыми неисправностями

- Отсутствие сетевого соединения:
    - проблемы с сетевым оборудованием
    - проблемы с сетевыми настройками
- Отсутствие доступа к удаленному хосту:
    - проблемы с маршрутизацией IP пакетов
- Невозможность получения доступа к удаленной службе:
    - проблемы с разрешением имен
    - DoS атаки
    - фильтрация сетевых портов

## Модель ТСР/IР и инструменты диагностики

Анализ сетевых проблем следует производить, начиная с нижнего уровня модели ТСР/Р и нижних уровней стека протоколов.
- Уровень сетевого доступа (др. названия: канальный уровень, уровень сетевого интерфейса) описывает среду передачи
  данных и передачу данных в локальной сети. На этом уровне определяются канальные (МАС) адреса
    - Протоколы канального уровня: Ethernet, PPP, L2TP, ARP
    - `ethtool`, `mii-tool`, `ifconfig`, `arp`, `ip neigh`, `arping`, `ip link`
- Межсетевой уровень (др. названия: сетевой уровень) определяет передачу данных между разными сетями. На этом уровне
  определяются IP адреса
    - Протоколы межсетевого уровня: IP, ICMP, IGMP, IPsec.
    - `ip address`, `ping`, `ifconfig`, `ip route`, `traceroute`, `netstat`, `tcpdump`
- Транспортный уровень определяет передачу данных между сетевыми приложениями. На этом уровне определяются адреса
  сетевых приложений - порты
    - Протоколы транспортного уровня: ТСР, UDP.
    - `nmap`, `ss`, `tcpdump`, `netstat`
- Прикладной уровень описывает работу сетевых приложений.
    - Протоколы прикладного уровня: DHCP, HTTP, HTTPS, IMAP, LDAP, NTP, PTP, RPC, SMTP, SNMP, SSH и др.
    - `wget`, `curl`, `nslookup`, `dig`, `tcpdump`

### Диагностика уровня сетевого доступа

- Проверка сетевого оборудования, сетевых кабелей
- Проверка цвета светодиода на Ethernet сетевой карте
- Выполнить команду `ip link show`:
    - UP - сетевой интерфейс подсоединен
    - NO-CARRIER - на сетевой интерфейс не приходит сигнал

### Сетевые настройки и маршрутизация

- Проверка сетевых настроек:
    - ІР адрес
    - сетевая маска
    - маршрут по умолчанию
    - разрешение имен хостов (DNS)
- Способы настройки сетевых интерфейсов:
    - `NetworkManager`
    - `ifup`/`ifdown` команды, файл `/etc/network/interfaces`
- Динамическое и статическое назначение IP адреса и сетевых настроек
- С помощью команды `ip address show` следует убедиться, что IP адрес и сетевая маска настроены правильно
- Если обнаружены ошибки в значениях IP адреса или сетевой маски, то следует:
    - установить способ настройки сетевых интерфейсов (NetworkManager или ifup/ifdown команды)
    - выяснить способ назначения IP адреса (статический или динамический).
    - Если адрес сетевому интерфейсу назначается динамически (через DHCP), то следует проверить доступность сервера DHCP
      командой: `sudo dhclient -v`
- Для определения способа настройки следует выяснить статус соответствующих служб: `NetworkManager.service` и
  `networking.service` командой: `systemctl status имя_службы`
    - Если активна только служба `NetworkManager.service`, то необходимо исправить сетевые настройки с помощью любой из
      утилит: `nm-connection-editor` (графическая утилита), `nmtui` (псевдографическая утилита), `nmcli` (утилита
      командной строки).
    - Если активна только служба `networking.service` - исправить сетевые настройки в файле `/etc/network/interfaces` и
      проверить содержимое файлов в каталоге `/etc/network/interfaces.d`
    - Если активны обе службы, то нужно убедиться, что под управлением NetworkManager находятся все интерфейсы кроме
      интерфейса loopback. Выполните команду `nmcli | grep unmanaged` и обратите внимание, у каких сетевых интерфейсов
      стоит отметка "без управления" (unmanaged)
- Если какой-то из сетевых интерфейсов (кроме loopback) находится не под управлением NetworkManager, то следует выбрать
  один из двух вариантов настройки:
    - Отключить службу `NetworkManager.service`:
        - `systemctl stop NetworkManager.service`
        - `systemctl mask NetworkManager.service`
        и настроить все интерфейсы в файле `/etc/network/interfaces`
    - Настроить все интерфейсы (кроме loopback) через `NetworkManager`
- На любом хосте, подключенном к сети, должен быть настроен маршрут (шлюз) по умолчанию. Проверку параметров такого
  маршрута (прежде всего, IP адрес шлюза) можно осуществить командой `ip route show`.
- Следует убедиться, что шлюз по умолчанию находится в одной из сетей, непосредственно подключенных к хосту.
    - Пример: шлюз с адресом `10.0.2.2` находится внутри сети `10.0.2.0/24`, к которой хост полключен через интерфейс
      `eth0`.
- Временно исправить шлюз по умолчанию можно командой: `ip route add default via IP_адрес_шлюза`
- Для изменения маршрута по умолчанию следует:
    - использовать утилиты для `NetworkManager`, если интерфейсы управляются этой службой
    - указать параметр gateway `IP_адрес` в файле `/etc/network/interfaces`.
- Настройку DNS можно проверить путем просмотра содержимого файла `/etc/resolv.conf`
- Если адреса DNS серверов указаны неверно, то следует:
    - использовать утилиты для `NetworkManager`, если интерфейсы управляются этой службой
    - исправить адреса серверов DNS в файле `/etc/resolv.conf`, если интерфейсы настраиваются в
      `/etc/network/interfaces`

## Доступность хостов

- Проверка доступности хостов по IP адресу или DNS имени:
    - работоспособность ТСР/Р стека на локальном хосте
    - доступность шлюза
    - доступность удаленного хоста
- Утилита `ping` используется, главным образом, для проверки доступности хостов в локальной сети (сети предприятия),
  поскольку принято блокировать ICMP сообщения на периметре сети предприятия.
- Утилита `ping` может также использоваться для оценки качества линии связи.
- Порядок использования команды `ping`:
    - `ping localhost` или `ping 127.0.0.1` - проверка работоспособности локального ТСР/Р стека
    - `ping IP_адреса_шлюза` - проверка доступности хоста в локальной сети и шлюза по умолчанию
- Утилита `traceroute` используется в основном для проверки доступности хостов, находящихся вне локальной сети, и
  определения возможных проблем с маршрутизацией за пределами сети предприятия
- Утилита `traceroute` по умолчанию использует протокол UDP. Поэтому пакеты, отсылаемые данной утилитой, реже
  отфильтровываются внешними маршрутизаторами


## Доступность сетевых служб удаленных хостов

- Для адресации сетевых процессов на хосте используются номера портов. Номер порта может принимать значения от 0 до
  65535
- Для серверных сетевых процессов порты выделяются статически, а порты для клиентских серверных процессов - динамически
  из диапазона, определенного в параметре ядра `/proc/sys/net/ipv4/ip_local_port_range` (по умолчанию 32768-60999)
- Порты определяются на транспортном уровне ТСР/Р модели (протоколы TCP и UDP)
- Сетевой сокет - это программный интерфейс, предназначенный для обмена данными между процессами и который определяется
  IP адресом хоста и портом, соответствующим процессу на данном хосте
- Сетевое сокетное соединении - это связь двух сокетов: сокета отправителя (клиента) и сокета получателя (сервера).
- Серверные сокеты в начале находятся в состояние "прослушивание" (listening), ожидая, когда клиентский сокет отправит
  запрос
    - Доступность удаленных портов (удаленных сетевых служб) может быть проверена командами:
        - `ncat` (nc) (программный пакет `ncat` из `extended` репозитория)
        - `nmap` (программный пакет nmap из `extended` репозитория)

## Обнаружение и устранение неисправностей в работе сетевых служб

- Проверка работы локальных сетевых служб:
    - `netstat -tlnp` или `ss -tlnp` - проверить, что сетевой процесс стартовал и процессу назначен соответствующий порт
    - `journalctl -и имя_службы.service` - в случае, если сетевая служба не стартовала или не использует требуемый
      сетевой интерфейс, следует проанализировать сообщения из журнала
- Обнаружение DoS(Denial of Service - отказ в обслуживании) атак:
    - DoS атака, в частности, может быть реализована с помощью отправки с различных поддельных хостов большого
      количества пакетов, в которых установлен флаг SYN, - начальная стадия установления ТСР-соединения
    - Обработка большого количества SYN-пакетов и существование большого количества полуоткрытых соединений могут
      существенно нагрузить вычислительные ресурсы сервера (процессор, память), что будет приводить к замедлению работы
      сервера, вплоть до состояния невозможности дальнейшего обслуживания запросов клиентов
    - `watch "netstat -tan | grep -i syn | wc -l"` - посчитать количество полуоткрытых соединений
        - большое количество соединений может означать, что идет "SYN Flood" атака

## Поиск и решение проблем на прикладном уровне

- Анализ перехваченного сетевого трафика - `tcpdump`
- Анализ журнальных файлов
- Запуск служб в режиме отладки (debug)
- Диагностика неисправностей с помощью специфических инструментов для разных протоколов прикладного уровня:
    - DNS: `nslookup,` `dig`
    - DHCP: `dhclient`
    - HTTP/HTTPS: `curl`, `wget`
    - SMTP: `nc адрес_сервера 25`

- Самым универсальным средством для выявления проблем в функционировании сетевых процессов, работающих на прикладном
  уровне модели ТСР/Р, является перехват и анализ трафика между клиентом и сервером
- Два наиболее популярных средства для перехвата трафика:
    - `tcpdump` (программный пакет `tcpdump` из репозитория `base`)
    - `wireshark` (программный пакет `wireshark` из репозитория `base`)
        - Утилита wireshark имеет графический интерфейс и поддерживает интерпретацию данных для огромного количества
          протоколов
