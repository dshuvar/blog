---
title: Введение в puppet
date: {}
categories: work
tags: 
  - puppet
  - Memory Allocation
published: true
---
##Что такое puppet?
[Puppet](https://puppet.com/) - система управления конфигурациями, состоящая из:
    - набора утилит для управления конфигураций
    - фраймворка для систем автоматизации
    - предметно-ориентированного языка (dsl)
Система разрабатывается компанией [puppet labs](http://puppetlabs.com/) на языке программирования [ruby](ruby-lang.org).

Программное обеспечение связанное с puppet:
	- [Facter](https://docs.puppet.com/facter/) - Утилита для извлечения данных о системе
    - [MCollective](https://docs.puppet.com/mcollective/) - Фреймворк для оркестрации
    - [Hiera](https://docs.puppet.com/hiera/1/) - Утилита где puppet хранит данные в виде ключей и значений
    - [PuppetDB](https://docs.puppet.com/puppetdb/1/) - Хранилище данных puppet'a
    
##Установка

В идеале установка должна выглядеть так:

Для Debian, Ubuntu

```
apt-get install puppet       # На клиенте (nodes)
apt-get install puppetmaster # На сервере (master)
```

Для RedHat, Centos, Fedora
Нужно добавить EPEL репозиторий.

```
yum install puppet        # На клиенте (nodes)
yum install puppet-server # На сервере (master)
```

На момент написания поста самой новой и стабильной версией является 4.8, а  для версия 3.8 не поддерживается с конца 2016 года.
Ниже прилагаю листинг действий для установки puppet'a на мастере(главный сервер):

```
wget https://apt.puppetlabs.com/puppetlabs-release-pc1-xenial.deb
sudo dpkg -i puppetlabs-release-pc1-xenial.deb
apt-g update
apt-get install puppetserver

```

вот что я в итоге получил:

```
root@puppet:~# dpkg -l | grep puppet
ii  puppet-agent                       1.8.3-1xenial                   amd64        The Puppet Agent package contains all of the elements needed to run puppet, including ruby, facter, hiera and mcollective.
ii  puppetlabs-release-pc1             1.1.0-2xenial                   all          Release packages for the Puppet Labs PC1 repository
ii  puppetserver                       2.7.2-1puppetlabs1              all          Puppet Labs puppetserver
```

Для установки клиенской части(puppet-agent) на отдельном хосте серверная часть(puppetserver) не требуется. В целом установка быстра и легка.

##Ошибки с которыми я столкнулся при запуске

Первый запуск произошел весьма болезненно и завершился ничем. В логах как и ожидалось - ошибка, в которой говорится о том, что для запуска JVM недостаточно памяти.

```
OpenJDK 64-Bit Server VM warning: ignoring option MaxPermSize=256m; support was removed in 8.0
```

```
2016-09-04 15:57:00,968 ERROR [async-dispatch-2] [p.t.internal] Error during service init!!!

java.lang.Error: Not enough available RAM (1,024MB) to safely accommodate the configured JVM heap size of {1}MB.  Puppet Server requires at least {2}MB of available RAM given this heap size, computed as 1.1 * max heap (-Xmx).  Either increase available memory or decrease the configured heap size by reducing the -Xms and -Xmx values in JAVA_ARGS in /etc/sysconfig/puppetserver on EL systems or /etc/default/puppetserver on Debian systems.
```

Такое возможно решить двумя способами:

1.Добавить памяти на сервер

2.Или указать параметр `JAVA_ARGS="-Xms1g -Xmx1g"`  в конфиге `/etc/default/puppetserver`

Согласно документации для запуска сервиса необходимо минимум 2гб озу.

По умолчанию параметр указан как `JAVA_ARGS="-Xms2g -Xmx2g -XX:MaxPermSize=256m"`

Второй запуск прошел без ошибок, что подтверждает статус init скрипта:

```
systemctl status puppetserver.service | grep active
   Active: active (running) since Fri 2017-01-27 21:07:06 UTC; 22min ago
```

##Минимальные настройки мастер-сервера.

###Синзронизация времени
Возможно указать  временную зону при помощи конфига `/etc/timezone` выбрав необходимую из [списка](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).
Либо установить пакет tzdata, если тот еще не установлен в вашей ос.

###Изменение конфига puppet
Отредактировал /etc/puppet/puppet.conf добавив туда информации в блок `agent`.

```
# This file can be used to override the default puppet settings.
# See the following links for more details on what settings are available:
# - https://docs.puppetlabs.com/puppet/latest/reference/config_important_settings.html
# - https://docs.puppetlabs.com/puppet/latest/reference/config_about_settings.html
# - https://docs.puppetlabs.com/puppet/latest/reference/config_file_main.html
# - https://docs.puppetlabs.com/puppet/latest/reference/configuration.html
[master]
vardir = /opt/puppetlabs/server/data/puppetserver
logdir = /var/log/puppetlabs/puppetserver
rundir = /var/run/puppetlabs/puppetserver
pidfile = /var/run/puppetlabs/puppetserver/puppetserver.pid
codedir = /etc/puppetlabs/code

[agent]
enviroment = production
server = puppet.altwhy.ru
```

###Указание связи имени хоста с адресом мастера
Отредактировал /etc/hosts

###Открытие 8140 порта на файерволе
```
iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 8140 -j ACCEPT
```

###Подписывание сертификата

Необходимо подписать сертификат на мастер-сервере.

```
               +------------------------+
               |                        |
               |  Root self-signed CA   |
               |                        |
               +------+----------+------+
                      |          |
           +----------+          +------------+
           |                                  |
           v                                  v
  +-----------------+                +----------------+
  |                 |                |                |
  | Master SSL Cert |                | Agent SSL Cert |
  |                 |                |                |
  +-----------------+                +----------------+
  ```

Подписывается сертифкат командой:

`puppet cert sign puppet.domain.com`

Проверить подпись можно командой:

`puppet cert list -a`

В случае если что то пошло не так, или например вы забыли указать в конфигурации альтернативные имена для сервера(как это сделал я), можете удалить сертификаты и переподписать по новой:

```
find /var/lib/puppet -type f -print0 |xargs -0r rm
puppet agent -t
puppet cert list
puppet cert sign puppet.tw1.su
```

```
puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppet.tw1.su
Info: Applying configuration version '1477261916'
Notice: Finished catalog run in 0.02 seconds
```




Источники информации:
	- puppet.com
    - Tutorial By Alessandro Franceschi (11-07-2015)
