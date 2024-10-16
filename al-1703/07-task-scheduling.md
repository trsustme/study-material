# Запуск заданий по расписанию

## Служба `cron`
- Служба `cron` предназначена для запуска заданий по расписанию
- Служба `cron` является базовой и запускается через `systemd systemctl status cron`
- Системные конфигурационные файлы: `/etc/crontab`, `/etc/cron.d/*`
- Диапазоны значений в записях конфигурационных файлов:
    - минута - 0-59
    - час - 0-23
    - день месяца - 1-31
    - месяц - 1-12
    - день недели - 0-7 (0 и 7 соответствуют воскресенью)

## Запуск заданий по расписанию пользователями

- Служба cron позволяет обычным пользователям запускать задания по расписанию
- Для настройки запуска используется команда `crontab:`
    - `crontab -e` - создание расписания для запуска заданий
    - `crontab -l` - просмотр расписания
- Формат `crontab` файлов: `минута час день_месяца месяц день_недели команда`
- Файлы индивидуальных пользователей хранятся в каталоге `/var/spool/cron/crontabs`

Чтобы разрешить пользователям использовать `crontab`, нужно добавить соответствующего пользователя в группу `crontab`
- `sudo usermod -aG crontab имя_пользователя`
- или установить соответствующие права на каталог `/var/spool/cron/crontabs`:
- `sudo chmod 1733 /var/spool/cron/crontabs`

С помощью файлов `/etc/cron.allow` и `/etc/cron.deny` можно определить кто из пользователей имеет право создавать
`crontab` файлы:
- Если файл `/etc/cron.allow` существует, то в нем перечисляются пользователи (каждый пользователь в отдельной строке),
  кому можно использовать `crontab`.
- Если файл `/etc/cron.deny` существует, то в нем перечисляются пользователи, кому запрещено использовать `crontab`.
- Если оба файла существуют, то информация в файле `/etc/cron.deny` игнорируется.
- Если обоих файлов не существует, то все пользователи могут использовать crontab.

## Периодический запуск заданий с помощью `anacron`

- Планировщик периодического запуска заданий `anacron` запускает задания интервалами, исчисляемыми в днях
- `anacron` используется для запуска заданий раз в день, неделю, месяц и т. д.
  Точное время запуска заданий не определяется
- `anacron` запускается через `systemd` примерно раз в час

## Запуск заданий по расписанию с помощью `systemd`

- B `systemd` для запуска заданий по расписанию предназначены юниты типа таймер (.timer)
- В таймер-юните указывается, когда будет запущена одноименная служба
- Таймер-юнит должен содержать секцию `[Timer]`, внутри которой размещаются параметры, устанавливающие расписание:
    - OnCalendar=день_недели YYYY-MM-DD HH:MM:SS
    - `AccuracySec=` - временнАя точность выполнения заданий (по умолчанию 1 мин)
- `systemctl list-timers` - получить информацию о запущенных таймерах

## Запуск отложенных заданий с помощью службы `at`

- `at время_запуска < имя_файла_с_командами`
- Просмотреть список существующих заданий можно командой `atq`
- Удаление заданий производится командой `atrm`
- Использовать службу могут обычные пользователи
- Файлы `/etc/at.allow` и `/etc/at.deny` предназначены для ограничения использования службы `at` пользователями

## Запуск отложенных заданий с помощью `systemd-run`

- Администратор может запускать отложенные задания с помощью команды `systemd-run`
- Разовое выполнение задания (команды) с указанием временного интервала после активации таймера:
    - `systemd-run --on-active=интервал команда`
- Выполнение задания в указанную дату:
    - `systemd-run --on-calendar=дата команда`
- При использовании `systemd-run` создаются временные юниты для службы и таймера, после чего таймер активируется и
  запускает службу, которая выполняет команду

