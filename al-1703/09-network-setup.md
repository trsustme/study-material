# Настройка сети в Astra Linux

## Сетевой интерфейс

- Сетевой интерфейс - программное соответствие физического или виртуального сетевого устройства, предназначенное для
  передачи данных между процессами через компьютерную сеть
- Сетевым интерфейсам не соответствуют никакие специальные файлы в каталоге `/dev`
- Интерфейсы создаются автоматически для каждого обнаруженного сетевого устройства при загрузке ядра ОС
- Существует соглашение о именовании интерфейсов, в соответствии с которым имя интерфейса состоит из префикса, характеризующего его тип, и числа, соответствующего номеру интерфейса данного типа в системе
    - например: `ethO` - сетевой интерфейс к карте Ethernet
- Примеры сетевых интерфейсов:
    - интерфейсы физических устройств (сетевых карт, wi-fi адаптеров и так далее)
    - интерфейсы локальной обратной петли существуют для обмена данными между сетевыми процессами на одном компьютере
    - туннели - для инкапсуляции протокола того же или более низкого уровня в другой протокол
    - интерфейсы виртуальных сетей (VLAN)

## Настройка сетевых интерфейсов

- Network Manager:
    - `NetworkManager.service`
    - Клиентские приложения (`nmcli`, `nmtui`, `nm-connection-editor`)
- `ifup`/`ifdown` команды:
    - `networking.service` (служба)
    - `/etc/network/interfaces` (конфигурационный файл)
- Команды для ручной временной настройки сетевых интерфейсов:
    - Просмотр настроек: `ip address show`
    - Изменение настроек: `ip address add A.B.C.D/M dev ethO`

## Network Manager

- Конфигурационный файл `/etc/NetworkManager/NetworkManager.conf`
- Основные типы объектов:
    - устройство (`device`) и соединение (`connection`)
        - устройство (device) - соответствует сетевому интерфейсу
        - соединение (connection) - сетевой интерфейс плюс дополнительные сетевые настройки
    - объект `device` является частью объекта `connection`
    - каждый объект имеет свой набор свойств
- Просмотр настроек: команда `show`
    - `nmcli device show имя_интерфейса`
    - `nmcli connection show имя_соединения`
- Создание/изменение соединений: команды `add/modify`
    - `mcli con mod path 1 ip4 A.B.C.D/M ipv4.method manual`

## Настройка с помощью ifup/ifdown команд

- Команды: `ifup`, `ifdown`, `ifquery`
- Служба `networking.service`
- Конфигурационный файл `/etc/network/interfaces`
    - `auto ethX` - активация интерфейса при загрузке
    - `iface ethX inet dhcp/static` - метод получение адреса (dhcp или статика)
    - `address A.B.C.D/M` - статическое определение адреса/маски
    - `gateway A.B.C.D` - статическое определение шлюза
- Настройка сети `ТСР/IР` из командной строки может выполняться с использованием инструментов `ifup` и `ifdown`,
  входящих в пакет `ifupdown`.
- `/etc/network/interfaces` - основной конфигурационный файл для ifup/ifdown команд
    - см. `man interfaces`
- `/etc/resolv.conf` - прописываются адреса используемых DNS-серверов

## Команды диагностики сети

- `ip address show`/`ip a` - проверка правильности реквизитов ІР-адреса
- `ip route`/`ip r` - проверка маршрута (шлюза) по умолчанию (default)
- `ping ip-шлюза` - проверка соединения до шлюза утилитой
- `traceroute` - проверка подключения к сети интернет (при необходимости)
- `/etc/resolv.conf` - проверка настройки используемых DNS-серверов
