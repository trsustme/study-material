# Архивация и сжатие данных

## Сжатие файлов

- `.gz`: `gzip`, `gunzip`, `zcat` -
- `.bz2`: `bzip2`, `bunzip2`, `bzcat`
- `.xz`: `xz`, `unxz`, `xzcat`
- `.zip`: `zip`, `unzip`

Сжатие:
- xz (max)
- bzip2
- gzip

Скорость:
- gzip (max)
- bzip2
- xz

Команды для сжатия (.gz, .bz2, .xz соответственно):
- 9z1p;
 bzip2
- xz

Команды для распаковки:
- gunzip
- bunzip2
- unxz

Команды для просмотра сжатого файла:
- zcat
- bzcat
- xzcat

`dd` - копировать один файл в другой
- `dd if=/dev/sr0 of=cdrom.iso` - создать образ

`tar` - архивация файлов

`rsync` - локальное и удаленное копирование и синхронизация файлов и каталогов

