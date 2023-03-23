# Руководство по использованию AIDE 0.17.4

## Введение

AIDE (Advanced Intrusion Detection Environment) - это IDS (Intrusion Detection System) с открытым 
исходным кодом, который помогает отслеживать и обнаруживать любые
изменения в системных файлах, директориях и конфигурациях.

## Установка 

* Debian GNU/Linux | Ubuntu: `apt install aide` 

* FreeBSD: `pkg install aide`

* Gentoo: `emerge aide`

* Homebrew: `brew install aide`

* MacPorts: `port install aide`

* NixOS: `nix-env -iA nixos.aide`

* OpenBSD: `pkg_add aide`

* openSUSE: `zypper install aide`

* Red Hat | CentOS | Fedora: `yum install aide`


## Конфигурация

Конфигурация AIDE выполняется через файл конфигурации, расположенный
в директории /etc/aide/aide.conf

###  Сам файл конфигурации состоит из следующих параметров:


#### 1. Местоположение базы данных:

`database_in=file:/var/lib/aide/aide.db` указывает, что AIDE должен использовать файл `/var/lib/aide/aide.db` в качестве исходной базы данных при запуске.

`database_out=file:/var/lib/aide/aide.db.new` указывает, что AIDE должен записать изменения, которые он обнаружил в файл `/var/lib/aide/aide.db.new`.

`database_new=file:/var/lib/aide/aide.db.new` указывает, что AIDE должен использовать файл `/var/lib/aide/aide.db.new` как новую базу данных при следующем запуске.
#### 2. Правила
Правила отвечают
Более подробно с правилами можно ознакомиться тут https://linux.die.net/man/5/aide.conf

#### 3. Выбор файлов и директорий для мониторинга

Файлы и директорий, которые нужно исключить из мониторинга должны быть помечены `!` в начале.

`!/home` - исключить из мониторинга домашнюю папку

Пример:
```
!/home/user/.cache/
```


## Использование 

1. Обновляем файл конфигурации:
   `aide --update --config /etc/aide/aide.conf`
2. Обновляем базу данных:
    `cp /var/log/aide.db.new /var/log/aide/aide.db`
3. Запускаем AIDE: `aide --check --config /etc/aide/aide.conf`


## Логи

По умолчанию логи хранятся как /var/log/aide/*.log


### Использованный материал: 

* https://linux.die.net/man/5/aide.conf
* https://www.pc-freak.net/blog/install-configure-aide-advanced-intrusion-detection-environment-debian-gnu-linux-11-monitor-files/