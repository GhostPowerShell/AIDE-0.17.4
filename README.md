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

После установки инициализируем базу данных:

`aideinit --config /etc/aide/aide.conf`


## Конфигурация

Конфигурация AIDE выполняется через файл конфигурации, расположенный
в директории /etc/aide/aide.conf

###  Сам файл конфигурации состоит из следующих параметров:


#### 1. Местоположение базы данных:

`database_in=file:/var/lib/aide/aide.db` указывает, что AIDE должен использовать файл `/var/lib/aide/aide.db` в качестве исходной базы данных при запуске.

`database_out=file:/var/lib/aide/aide.db.new` указывает, что AIDE должен записать изменения, которые он обнаружил в файл `/var/lib/aide/aide.db.new`.

`database_new=file:/var/lib/aide/aide.db.new` указывает, что AIDE должен использовать файл `/var/lib/aide/aide.db.new` как новую базу данных при следующем запуске.
#### 2. Правила

Для каждой директории или файла можно задавать правила проверки.

К примеру: 

`/etc p+i+u+g`  - проверять только разрешения, inode, пользователя и группу для /etc

Так же присутствует возможность создать пользовательскую группу правил

К примеру:

`MyRule = p+i+n+u+g+s+b+m+c+md5+sha1`

Более подробно с правилами можно ознакомиться тут https://linux.die.net/man/5/aide.conf

#### 3. Выбор файлов и директорий для мониторинга

Файлы и директорий, которые нужно исключить из мониторинга должны быть помечены `!` в начале.

`!/home` - исключить из мониторинга домашнюю папку

Пример:
```
!/home/user/.cache/
```

Пример использования: 
    
Открываем /etc/aide/aide.conf через любой редактор и добавляем в конец:

```
/etc p+i+u+g     
/bin MyRule     
/sbin MyRule     
/var MyRule		
!/var/log/.*     
!/var/spool/.*
!/var/adm/utmp$  
```
## Использование 


1. Обновляем базу данных:
    `cp /var/log/aide.db.new /var/log/aide/aide.db`
2. Запускаем AIDE: `aide --check --config /etc/aide/aide.conf`

Стоит заметить, что выполнение этой команды займет некоторое время в зависимости от размера
файловой системы и объема ОЗУ на сервере.
```
Start timestamp: 2023-03-23 16:17:16 +0000 (AIDE 0.17.4)
AIDE found NO differences between the two databases. Looks okay!!
```
#
Допустим мы создали файл student.txt в /home/student/:

`$ echo "FLAG{TEST}" > student.txt`

Далее запускаем AIDE:
```
$ cp /var/lib/aide/aide.db.new /var/lib/aide/aide.db
$ aide --check --config /etc/aide/aide.conf
```

Получаем ответ:
```
Start timestamp: 2023-03-23 17:14:35 +0000 (AIDE 0.17.4)
AIDE found differences between database and filesystem!!
Ignored e2fs attributes: EIh

Summary:
  Total number of entries:	15055
  Added entries:		1
  Removed entries:		0
  Changed entries:		1

---------------------------------------------------
Added entries:
---------------------------------------------------

f+++++++++++++++++: /home/student/student.txt

---------------------------------------------------
Changed entries:
---------------------------------------------------

d =.... mc.. .. . : /home/student
```


## Логи

По умолчанию логи хранятся как /var/log/aide/*.log.



### Источники: 

* https://linux.die.net/man/5/aide.conf
* https://www.pc-freak.net/blog/install-configure-aide-advanced-intrusion-detection-environment-debian-gnu-linux-11-monitor-files/