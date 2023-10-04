# Containerization_2
## Контейнеризация (семинары)
## Урок 2. Механизмы контрольных групп
### Задание 1:
1) запустить контейнер с ubuntu, используя механизм LXC
2) ограничить контейнер 256 Мб ОЗУ и проверить, что ограничение работает
3) добавить автозапуск контейнеру, перезагрузить ОС и убедиться, что контейнер действительно запустился самостоятельно
4) при создании указать файл, куда записывать логи
5) после перезагрузки проанализировать логи

### Задание 2*: 
настроить автоматическую маршрутизацию между контейнерами. 
Адреса можно взять: 10.0.12.0/24 и 10.0.13.0/24.


### Задание 1:

### Установка ЛКС
sudo apt update

apt-get install lxc debootstrap bridge-utils lxc-templates

apt-get install lxd-installer

lxd init(Здесь просто нажимаем на Enter что уствновились значения по умолчанию)


### Проверяем

lxc storage list

lxc storage list

### 1) запустить контейнер с ubuntu, используя механизм LXC

Создаем контейнер

lxc-create -n cont1 -t ubuntu

Проверяем:

lxc-ls -f

NAME  STATE   AUTOSTART GROUPS IPV4 IPV6 UNPRIVILEGED

cont1 STOPPED 0         -      -    -    false


lxc-start -n cont1 -- запускаем

lxc-attach -n cont1 -- войдем в него


free -m —проверяем пямаять


               total        used        free      shared  buff/cache   available
               
Память:       4657          28        4625           0           4        4629

Подкачка:          0           0           0


exit -- выходим

lxc-stop -n cont1 - -закрываем


### 2) ограничить контейнер 256 Мб ОЗУ и проверить, что ограничение работает

Переходим в библиотеки lxc

cd /var/lib/lxc/

итого 12

drwx------  3 root root 4096 окт  4 12:09 ./

drwxr-xr-x 70 root root 4096 окт  4 12:11 ../

drwxrwx---  3 root root 4096 окт  4 12:10 cont1/

Переходим в папку контейнера

cd cont1/

итого 16

drwxrwx---  3 root root 4096 окт  4 12:10 ./

drwx------  3 root root 4096 окт  4 12:09 ../

-rw-r-----  1 root root  673 окт  4 12:10 config

drwxr-xr-x 17 root root 4096 окт  4 12:25 rootfs/


Открываем файл config


#Template used to create this container: /usr/share/lxc/templates/lxc-ubuntu

#Parameters passed to the template:

#For additional config options, please look at lxc.container.conf(5)


#Uncomment the following line to support nesting containers:

#lxc.include = /usr/share/lxc/config/nesting.conf

#(Be aware this has security implications)



#Common configuration

lxc.include = /usr/share/lxc/config/ubuntu.common.conf


#Container specific configuration

lxc.rootfs.path = dir:/var/lib/lxc/cont1/rootfs

lxc.uts.name = cont1

lxc.arch = amd64


#Network configuration

lxc.net.0.type = veth

lxc.net.0.link = lxcbr0

lxc.net.0.flags = up

lxc.net.0.hwaddr = 00:16:3e:8e:99:81


## Добавляем:

#additional

lxc.cgroup2.memory.max = 256M


Сохраняем Ctrl + O

Выходим Ctrl + Х


lxc-start -n cont1 -- запускаем

lxc-attach -n cont1 -- войдем в него


free -m —проверяем пямаять


               total        used        free      shared  buff/cache   available
               
Память:        256          27         227           0           0         228

Подкачка:          0           0           0


exit -- выходим


Выходим из контейнера: 

exit


### 3) добавить автозапуск контейнеру, перезагрузить ОС и убедиться, что контейнер действительно запустился самостоятельно


### Открываем файл config:

nano /var/lib/lxc/cont1/config


#Template used to create this container: /usr/share/lxc/templates/lxc-ubuntu

#Parameters passed to the template:

#For additional config options, please look at lxc.container.conf(5)


#Uncomment the following line to support nesting containers:

#lxc.include = /usr/share/lxc/config/nesting.conf

#(Be aware this has security implications)



#Common configuration

lxc.include = /usr/share/lxc/config/ubuntu.common.conf


#Container specific configuration

lxc.rootfs.path = dir:/var/lib/lxc/cont1/rootfs

lxc.uts.name = cont1

lxc.arch = amd64


#Network configuration

lxc.net.0.type = veth

lxc.net.0.link = lxcbr0

lxc.net.0.flags = up

lxc.net.0.hwaddr = 00:16:3e:8e:99:81


#additional

lxc.cgroup2.memory.max = 256M


## Добавляем:

lxc.start.auto = 1


Сохраняем Ctrl + O

Выходим Ctrl + Х


Перезагружаем:

reboot


Проверяем:

lxc-ls -f

NAME  STATE   AUTOSTART GROUPS IPV4      IPV6 UNPRIVILEGED

cont1 RUNNING 1         -      10.0.3.63 -    false


### 4) при создании указать файл, куда записывать логи


Так  как при создании файл для логов указан не был, добавим его в файл конфигурации:


### Открываем файл config:

nano /var/lib/lxc/cont1/config


#Template used to create this container: /usr/share/lxc/templates/lxc-ubuntu

#Parameters passed to the template:

#For additional config options, please look at lxc.container.conf(5)


#Uncomment the following line to support nesting containers:

#lxc.include = /usr/share/lxc/config/nesting.conf

#(Be aware this has security implications)



#Common configuration

lxc.include = /usr/share/lxc/config/ubuntu.common.conf


#Container specific configuration

lxc.rootfs.path = dir:/var/lib/lxc/cont1/rootfs

lxc.uts.name = cont1

lxc.arch = amd64


#Network configuration

lxc.net.0.type = veth

lxc.net.0.link = lxcbr0

lxc.net.0.flags = up

lxc.net.0.hwaddr = 00:16:3e:8e:99:81


#additional

lxc.cgroup2.memory.max = 256M

lxc.start.auto = 1


## Добавляем:


#logging

lxc.log.file = /home/HomeWork02/cont1.log

lxc.log.level = 1


Сохраняем Ctrl + O

Выходим Ctrl + Х


Перезагружаем:

reboot


### 5) после перезагрузки проанализировать логи:


cd /home/HomeWork02# 

cat cont1.log



lxc-attach cont1 20231004101109.755 DEBUG    commands - commands.c:lxc_cmd_rsp_recv_fds:140 - Received less file descriptors 1 < 20 for command "get_cgroup_ctx"

lxc-attach cont1 20231004101109.798 WARN     attach - attach.c:lxc_attach:1603 - Could not change directory to "/home/andrey"

lxc-attach cont1 20231004101109.799 NOTICE   utils - utils.c:lxc_drop_groups:1368 - Dropped supplimentary groups

lxc-attach cont1 20231004101109.799 INFO     apparmor - lsm/apparmor.c:apparmor_process_label_set_at:1186 - Set AppArmor label to "lxc-container-default-cgns"

lxc-attach cont1 20231004101109.800 NOTICE   utils - utils.c:lxc_switch_uid_gid:1344 - Switched to gid 0

lxc-attach cont1 20231004101109.800 NOTICE   utils - utils.c:lxc_switch_uid_gid:1353 - Switched to uid 0

lxc-attach cont1 20231004101312.827 INFO     terminal - terminal.c:lxc_terminal_ptx_io_handler:390 - Terminal client on fd 7 has exited



### Задание 2*: настроить автоматическую маршрутизацию между контейнерами. Адреса можно взять: 10.0.12.0/24 и 10.0.13.0/24.


Создаем контейнер

lxc-create -n cont2 -t ubuntu --logfile=HomeWork02/cont2.lo


### Открываем файл config:


nano /var/lib/lxc/cont2/config



#Template used to create this container: /usr/share/lxc/templates/lxc-ubuntu

#Parameters passed to the template:

#For additional config options, please look at lxc.container.conf(5)


#Uncomment the following line to support nesting containers:

#lxc.include = /usr/share/lxc/config/nesting.conf

#(Be aware this has security implications)



#Common configuration

lxc.include = /usr/share/lxc/config/ubuntu.common.conf


#Container specific configuration

lxc.rootfs.path = dir:/var/lib/lxc/cont1/rootfs

lxc.uts.name = cont1

lxc.arch = amd64


#Network configuration

lxc.net.0.type = veth

lxc.net.0.link = lxcbr0

lxc.net.0.flags = up

lxc.net.0.hwaddr = 00:16:3e:8e:99:81


## Добавляем:


#additional

lxc.cgroup2.memory.max = 256M

lxc.start.auto = 1


#logging

lxc.log.file = /home/HomeWork02/cont2.log

lxc.log.level = 1


Сохраняем Ctrl + O

Выходим Ctrl + Х


Проверяем:

lxc-ls -f


NAME  STATE   AUTOSTART GROUPS IPV4      IPV6 UNPRIVILEGED

cont1 RUNNING 1         -      10.0.3.63 -    false

cont2 STOPPED 1         -      -         -    false


Перезагружаем:

reboot


Проверяем:

lxc-ls -f


NAME  STATE   AUTOSTART GROUPS IPV4      IPV6 UNPRIVILEGED

cont1 RUNNING 1         -      10.0.12.0 -    false

cont2 RUNNING 1         -      10.0.13.0 -    false


lxc-attach -n cont1

ping 10.0.12.0


PING 10.0.12.0 (10.0.12.0) 56(84) bytes of data.


64 bytes from 10.0.12.0: icmp_seq=10 ttl=64 time=0.061 ms

64 bytes from 10.0.12.0: icmp_seq=11 ttl=64 time=0.078 ms

64 bytes from 10.0.12.0: icmp_seq=12 ttl=64 time=0.061 ms

^C

--- 10.0.12.0 ping statistics ---

23 packets transmitted, 23 received, 0% packet loss, time 23138ms

rtt min/avg/max/mdev = 0.034/0.062/0.105/0.011 ms

 
exit


lxc-attach -n cont2

ping 10.0.13.0


PING 10.0.13.0 (10.0.13.0) 56(84) bytes of data.

64 bytes from 10.0.13.0: icmp_seq=6 ttl=64 time=0.060 ms

64 bytes from 10.0.13.0: icmp_seq=7 ttl=64 time=0.059 ms

64 bytes from 10.0.13.0: icmp_seq=8 ttl=64 time=0.057 ms

^C

--- 10.0.13.0 ping statistics ---

8 packets transmitted, 8 received, 0% packet loss, time 7148ms

rtt min/avg/max/mdev = 0.050/0.058/0.063/0.003 ms











