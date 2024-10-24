# Команды Linux

## Типы Shells

* Bourne Shell - sh

* C Shell - csh и tcsh

* Z Shell - zsh

* Bourne again Shell - bash

Чтобы увидеть, какая оболочка сейчас:

```bash
~$ echo $SHELL

/bin/bash
```

Команда `echo` выводит на экран текстовую информацию. Символ доллара призывает переменную среды.

## Основные команды

### Вывод на экран

```bash
~$ echo Hello

Hello
```

```bash
~$ echo $USER

admin
```

### Показать список папок и файлов

```bash
~$ ls

some-dir text.txt Read.me
```

### Сменить каталог

```bash
~$ cd some-dir
```

### Показать рабочую директорию

```bash
~$ pwd

/home/admin/some-dir
```

### Создать каталог

```bash
~$ mkdir my-dir
```

### Несколько команд

```bash
~$ cd my-dir; mkdir tmp; pwd

/home/admin/some-dir/my-dir
```

Несмотря на то, что эти команды запускаются за один раз, по факту они выполняются одна за другой.

## Работа с каталогами

```bash
/home/admin/Honda/Accord/8gen
```

### Создать иерархию директорий

```bash
~$ mkdir /home/admin/Honda
~$ mkdir /home/admin/Honda/Accord
~$ mkdir /home/admin/Honda/Accord/8gen
```

То же самое, но проще:

```bash
~$ mkdir -p /home/admin/Honda/Accord/8gen
```

### Удалить каталог

```bash
~$ rm -r /tmp/BMW
```

### Скопировать каталог

```bash
~$ cp -r M3 /tmp/BMW
```

## Работа с файлами

### Создать новый пустой файл

```bash
~$ touch new-file.txt
```

### Добавить контент в файл

```bash
~$ cat > new-file.txt
```

> Ctrl + d

```bash
Sho-to tam gte-to tut.
```

### Просмотр содержимого файла

```bash
~$ cat new-file.txt 

Sho-to tam gte-to tut.
```

### Скопировать файл

```bash
~$ cp new-file.txt my-copy.bak
```

### Переместить (переименовать) файл

```bash
~$ mv new-file.txt current.txt
```

### Удалить файл

```bash
~$ rm current.txt
```

## Текстовые редакторы

### Текстовый редактор Vi

```bash
~$ vi text.txt
```

Command Mode (Командный)

> Esc

Insert Mode (Ввод)

>i

### Командный режим Vi

#### Перемещение стреклами

#### Удаление

> x

> dd

#### Копировать

> yy

#### Вставить

> p

#### Прокрутка

> Ctrl + u

> Ctrl + d

#### Выйти без сохранения

> :q

#### Сохранить и выйти

> :wq

#### Найти

В командном режиме

> /shto

Чтобы перейти к следующему найденному

> n

## Аккаунты пользователей

```bash
~$ whoami

soju
```

```bash
~$ id

uid=1001(soju)gid=1001(soju)groups=1001(soju)
```

```bash
~$ su ivan

Password:
```

### Подключение используя протокол SSH

```bash
~$ ssh sanya@192.168.100.10
```

### Пользователь root

```bash
~$ ls /root

ls: cannot access 'root': Permission denied
```

```bash
~$ sudo ls /root

[sudo] password for soju:
anaconda-ks.cfg initial-setup-ks.cfg
```

Почти никогда не войти в систему как пользователь root.

Пользователь root может дать возможность выполнить некоторые действия обычному пользователю, предоставив ему sudo-привелегии.

Для этого нужно сделать запись в файл `/ect/soduers`.

Пользователи с такими привилегиями могут повышать свой уровень разрешений и
выполнять задачи как root.

Для этого перед требуемой командой нужно написать префикс sudo.

Далее, система спросит у них пароль и, при успешном его вводе, команда выполнится с повышенными привилегиями.

## Скачивание файлов

```bash
~$ curl https://keycdn.com/img/example.jpg -O
```

Чтобы скачать файл пишем curl, за ним URL-путь к файлу, а дальше флаг `-O`.

Последняя опция инструктирует curl сохранить результат в файл, вместо того, чтобы вывести его на экран.

В противном случае, он просто покажет файл на экран и это может быть проблемой,
если файл большого размера.


```bash
~$ wget https://keycdn.com/img/example.jpg
```

Им проще пользоваться, поскольку по умолчанию wget сразу сохраняет скачанное в
файл.

## Менеджмент пакетов

### Пакетный менеджер RPM (Red Hat Package Manager)

RPM является базовым менеджером для Red Hat Enterprise Linux и Fedora. Эти
дистрибутивы, как и Centos связаны с компанией Red Hat. Но менеджером RPM
пользуются и другие крупные дистрибутивы, например Suse.

#### Установить пакет

```bash
~$ rpm -i telnet-0.17-85.el9.x86_64.rpm
```

#### Удалить пакет

```bash
~$ rpm -e telnet
```

#### Получить информацию о пакете

```bash
~$ rpm -q telnet
```

RPM требует, чтобы ты точно указал ему место, где находится его пакет.

Rpm не заботится о нужных для работы приложения зависимостях, без которых программа не сможет запуститься. Он установит его как есть.

Скажем, ты устанавливаешь программное обеспечение вроде Ansible.

Для его работы в первую очередь потребуется Python, а также несколько других
программных компонентов.

Так вот, просто установив Ansible через RPM мы скорее всего получим его в нерабочем виде.

Менеджер rpm не озаботится установкой Python и других зависимых библиотек.

### Пакетный менеджер DNF

#### Установить пакет

```bash
~$ dnf install ansible
```

DNF — это высокоуровневый менеджер пакетов, который управляет более простым
менеджером RPM.

Запуск простой команды `dnf install ansible` установит Ansible и все его зависимые пакеты.

Dnf может самостоятельно заходить на специальные склады-хранилища, где хранятся
программные пакеты и искать там нужные.

Такие хранилища называются репозитории и они содержат сотни и тысячи
RPM-файлов.

Эти репозитории могут быть локальными, который ты установил на свой компьютер,
или удаленные, например приватный репо твоего предприятия, либо общедоступный в
интернете.

Информация о репозитории расположена в конфигурационном файле в размещении `/etc/yum.repos.d`

### Репозитории DNF

```bash
~$ dnf list ansible

ansible.noarch  7.2.0-1el9next  @epel-next
```

```bash
~$ dnf remove ansible
```

```bash
~$ dnf install epel-release
```

```bash
~$ dnf --showduplicates list ansible

ansible.noarch      6.3.0-2.el9         epel
ansible.noarch      7.2.0-1.el9.next    epel-next
```

```bash
~$ dnf install ansible-6.3.0-2.el9
```

```bash
~$ dnf list ansible

Installed Package
ansible.noarch      6.3.0-2.el9         epel
Available Package
ansible.noarch      7.2.0-1.el9.next    epel-next
```

## Службы

### Запустить службу 'httpd'

```bash
~$ service httpd start
```

```bash
~$ systemctl start httpd
```

### Остановить службу 'httpd'

```bash
~$ systemctl stop httpd
```

### Узнать статус службы 'httpd'

```bash
~$ systemctl status httpd
```

### Настроить автозагрузку службы 'httpd'

```bash
~$ systemctl enable httpd
```

### Убрать автозагрузку службы 'httpd'

```bash
~$ systemctl disable httpd
```

Скажем, у нас есть простая программа на Flask, это веб-сервер написанный на Python.

Его код доступен по пути `/opt/web/run.py`.

Чтобы запустить такое приложение, мы должны запустить интерпретатор Python и
указать путь к файлу программы.

```bash
~$ /usr/bin/python3 /opt/web/run.py

* Serving Flask app 'app'
* Debug mode: off
WARNING: bla-bla-bla
* Running on http://127/0/0/1:500
Press CTRL+C to quit

[Service]
ExecStart=/usr/bin/python3 /opt/web/run.py
```

Как видишь он запустился и готов обслуживать подключения на ip-адресе `127.0.0.1` или на `localhost`, что означает вот этот наш локальный компьютер на порту `5000`.

Таким образом, любой, кто запустит браузер локально на этом компьютере и укажет в строке `http://127.0.0.1:5000` получит ответ от этого веб-сервера.

Если кто-то введет этот адрес на другой машине, то не увидит ничего. Так работает localhost. Принимает запросы только внутри самого хоста.

```bash
~$ systemctl start simple_app
```

```bash
~$ systemctl stop simple_app
```

> /etc/systemd/system

```bash
~$ systemctl status simple_app

* simple_app.service
    Loaded: loaded (/etc/systemd/system/simple_app.service; static)
    Active: active (running) since Mon 2023-03-13 19:50:00 UTC; 2min Os
ago
    Main PID: 6459 (python3)
        Tasks: 1 (limit: 34570)
        Memory: 35.7M
        CGroup:
/system.slice/docker-76099d64b0a7512d761ce36ca1301d8cf0b5e37dda
1e8b2e3642a9e540b1eed2.scope/system.slice/simple_app.se>
        6459/usr/bin/python3/opt/web/run.py

...
```

Запрос к нему с помощью утилиты curl.

```bash
~$ curl http://localhost:5000

Hello, DevOps!
```

Службы systemd настраиваются через специальные unit-файлы. В этом файле в особом формате описывается то, на что мы хотим, чтобы systemd обращал внимание
при работе службы.

Эти unit-файлы могут быть расположены в нескольких местах, мы рассмотрим
размещение `/etc/systemd/system`.

Файлы отсюда systemd найдет и прочитает.

Теперь требуется запустить команду `systemctl daemon-reload`, которая позволит
systemd заново прочитать unit-файлы и взять во внимание только что созданную
службу.

```bash
~$ systemctl daemon-reload
```

Далее мы можем использовать обычный подход для запуска службы.

```bash
~$ systemctl start simple_app
```

```bash
~$ systemctl stop simple_app
```

```bash
~$ systemctl enable simple_app
```

```bash
/etc/systemd/system/simple_app.service

[Unit]
Description-Simple Flask App

[Service]
ExecStart=/usr/bin/python3 /opt/web/run.py
ExecStartPre=/opt/web/migrate_database.sh
ExecStartPost=/opt/web/sent_notifications.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

> etc/systemd/system

### Unit-файл службы Docker

```bash
/lib/systemd/system/docker.service

[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service containerd.service
Wants=network-online.target.
Requires=docker.socket
Wants=containerd.service

[Service]
Type=notify
ExecStart=/usr/bin/dockerd -H fd://--containerd=/run/containerd/containerd.sock
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutSec=0
RestartSec=2
Restart=always
StartLimitBurst=3
StartLimitInterval=60s
LimitNOFILE=Infinity
LimitNPROC=infinity
LimitCORE=infinity
TasksMax=infinity
Delegate=yes

[Install]
WantedBy=multi-user.target
```

По директивам видно, что после установки служба docker.service работает как
фоновый процесс, который ожидает свои docker-команды.

Сам файл для запуска демона docker находится в размещении `/usr/bin/dockerd`.
`D` на конце значит, что это процесс демон. Демон означает, что эта программа предназначена для работы в фоновом режиме, без прямого взаимодействия с пользователем.

Наш python веб-сервер по сути тоже был демоном.

В этом unit-файле есть три секции: Unit, Service и Install.

Здесь в разделе Unit есть описание под Description. Также здесь есть ссылка на документацию к этому ПО и другие директивы.

В разделе Service в ExecStart прописана команда, которая запускает исполняемый
файл dockerd.

## Маршрутизация

![img1](/knowledge-base/img/switching.png)

### Отображение интерфейсов для хоста

#### СИСТЕМА1

```bash
~$ ip link

2:eth0 <BROADCAST, MULTICAST, UP, LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
```

#### СИСТЕМА2

```bash
ip link

2:eth0 <BROADCAST, MULTICAST, UP, LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
```

Для установления полноценной сети, подключим наши интерфейсы не к общему
проводу, а к специальному устройству-коммутатору или свитчу.

В этом случае мы подключили к свитчу интерфейсы с именем eth0 обоих наших
систем.

Предположим, это сеть с адресацией `192.168.1.0`.

### Назначение системам IP-адреса в этой сети.

#### СИСТЕМА1

```bash
~$ ip addr add  192.168.1.10/24 dev eth0
```

#### СИСТЕМА2

```bash
~$ ip addr add  192.168.1.11/24 dev eth0
```

### Проверка

#### СИСТЕМА1

```bash
ping 192.168.1.11

PING 192.168.1.11 (192.168.1.11) 56(84) bytes of data.
64 bytes from 192.168.1.11: icmp_seq=1 ttl=64 time=24.5 ms
64 bytes from 192.168.1.11: icmp_seq=2 ttl=64 time=13.1 ms
```

Допустим, у нас есть еще одна сеть, содержащая СИСТЕМА3 и СИСТЕМА4. Адресация
этой сети `192.168.2.0`.

Системы имеют IP-адреса `192.168.2.10` и `192.168.2.11` соответственно.

![img2](/knowledge-base/img/switching2.png)


Поскольку маршрутизатор или роутер подключается к двум отдельным сетям, ему назначают два IP-адреса. По одному в каждой сети.

В первой сети мы назначаем ему IP-адрес `192.168.1.1`, а во второй - IP `192.168.2.1`.

Теперь у нас есть маршрутизатор, подключенный к двум сетям, который может обеспечивать связь между ними.

```bash
~$ route

Kernel IP routing table
Destination     Gateway     Genmask     Flags   Metric      Ref     Use     Iface
```

```bash
~$ ip route add 192.168.2.0/24 via 192.168.1.1
```

```bash
~$ route

Kernel IP routing table
Destination  Gateway     Genmask        Flags   Metric      Ref     Use     Iface
192.168.2.0  192.168.1.1 255.255.255.0  UG      0           0       0       eth0
```

![img3](/knowledge-base/img/gateway.png)

Шлюз или gateway и маршрут или route.

Если бы сеть была комнатой, шлюз - это дверь во внешний мир, т.е. в другие сети или в Интернет.

Системам нужно знать, где эта дверь находится, чтобы пройти в нее.

Чтобы увидеть существующую конфигурацию маршрутизации в системе, запусти
команду route.

Она отображает таблицу маршрутизации ядра - kernel routing table, и в ней, как ты можешь видеть, в данный момент нет конфигураций маршрутизации.

Таким образом, в этом состоянии наша СИСТЕМА1 не сможет подключиться к СИСТЕМА3. Она сможет подключиться только к другим системам в той же сети в диапазоне `192.168.1.0`.

### Настройка шлюза в СИСТЕМА1

```bash
~$ ip route add 192.168.1.0/24 via 192.168.2.1
```

```bash
~$ route

Kernel IP routing table
Destination     Gateway         Genmask         Flags   Metric  Ref   Use  Iface
192.168.1.0     192.168.2.1     255.255.255.0   UG      0       0     0    eth0
```

Теперь предположим, что этим системам нужен доступ к интернету. Скажем, им нужен доступ к Google в сети `172.217.194.0` в интернете.

Таким образом, мы подключаем роутер к Интернету, а затем добавляем новый маршрут в свою routing table, чтобы направлять весь трафик в сеть `172.217.194` через маршрутизатор.

```bash
~$ ip route add 172.217.194.0/24 via 192.168.2.1
```

```bash
~$ route

Kernel IP routing table
Destination     Gateway         Genmask         Flags   Metric  Ref   Use  Iface
192.168.1.0     192.168.2.1     255.255.255.0   UG      0       0     0    eth0
172.217.194.0   192.168.2.1     255.255.255.0   UG      0       0     0    eth0
```

```bash
ip route add default via 192.168.2.1
```

```bash
~$ route

Kernel IP routing table
Destination     Gateway         Genmask         Flags   Metric  Ref   Use  Iface
default         192.168.2.1     255.255.255.0   UG      0       0     0    eth0
192.168.1.0     192.168.2.1     255.255.255.0   UG      0       0     0    eth0
172.217.194.0   192.168.2.1     255.255.255.0   UG      0       0     0    eth0
```

Существует очень много разных сайтов в разных сетях в интернете, и, вместо того, чтобы добавлять подобные записи в свою таблицу маршрутизации для каждой их этих сетей, мы можем просто сказать, что, если система не знает маршрута, то пользуйся этим маршрутизатором в качестве шлюза по умолчанию.

Таким образом, любой запрос к любой сети за пределами нашей существующей
поступит на этот конкретный маршрутизатор.

И здесь, при такой простой настройке, все, что нам нужно, это одна запись в таблице маршрутизации с шлюзом по умолчанию, установленным на IP-адрес маршрутизатора.

![img4](/knowledge-base/img/gateway.png)

Вместо слова `default` можно указать `0.0.0.0`. Это означает любой IP-адрес назначения. Обе эти строки означают одно и то же.

```bash
~$ ip route add 192.168.1.0/24 via 192.168.2.2
```

```bash
~$ route

Kernel IP routing table
Destination     Gateway         Genmask         Flags   Metric  Ref   Use  Iface
default         192.168.2.1     255.255.255.0   UG      0       0     0    eth0
192.168.1.0     192.168.2.2     255.255.255.0   UG      0       0     0    eth0
```

В случае СИСТЕМА4 для доступа к любым устройствам в сети
192.168.2.0 ей не нужен шлюз, потому что она находится в собственной сети.

Но предположим, что у нас в сети есть несколько маршрутизаторов: один для
интернета, другой для внутренней частной сети.

Тогда нам нужно будет иметь две отдельные записи для каждой сети.

Одна запись для внутренней приватной сети и другая запись со шлюзом по умолчанию для всех других сетей, включая общедоступные сети.

Поэтому, если у тебя возникают проблемы с выходом в интернет с твоих хостов,
проверка таблиц маршрутизации и конфигурации дефолтного шлюза - хорошее место
для начала траблшутинга.
