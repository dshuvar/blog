---
title: Введение в puppet
date: 2017-01-16T00:00:00.000Z
categories: work
tags: puppet
published: false
---
##Что такое puppet?
[Puppet](https://puppet.com/) - система управления конфигурациями, состоящая из:
    - набора утилит для управления конфигураций
    - фраймворка для систем автоматизации
    - предметно-ориентированного языка
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

Однако мой опыт установки связан с тем, что т.к мне были нужны не самые свеживе версии, а програмное обеспечения версии 3.8.
Замечу что на момент написания поста самой новой и стабильной версией является 4.8.
Основная сложность заключается, что репозитории предлагаемом доментации эта версия отсутсвует, а на днях и вовсе была прекращена ее поддержка. Версия 3.8 была выбрана т.к эта версия все еще используется у нас на работе и много где еще, и в последствии иметь опыт обновления. Ниже прилагаю листинг действий для установки puppet'a на мастере(главный сервер):
```
wget https://apt.puppetlabs.com/puppetlabs-release-precise.deb
wget https://apt.puppetlabs.com/puppetlabs-release-pc1-precise.deb
dpkg -i puppetlabs-release-precise.deb
dpkg -i puppetlabs-release--pc1precise.deb
apt-get update 
apt-get install puppet-agent
apt-get purge puppetlabs-release-pc1
apt-get install puppet 
apt-get install puppetserver 
```
вот что я в итоге получил:
```
dpkg -l | grep puppet
ii  facter                          2.4.6-1puppetlabs1                  Ruby module for collecting simple facts about a host operating system
ii  hiera                           1.3.4-1puppetlabs1                  A simple pluggable Hierarchical Database.
ii  puppet                          3.8.7-1puppetlabs1                  Centralized configuration management - agent startup and compatibility scripts
rc  puppet-agent                    1.7.0-1precise                      The Puppet Agent package contains all of the elements needed to run puppet, including ruby, facter, hiera and mcollective.
ii  puppet-common                   3.8.7-1puppetlabs1                  Centralized configuration management
ii  puppetlabs-release              1.1-1                               "Package to install Puppet Labs gpg key and apt repo"
ii  puppetserver                    1.2.0-1puppetlabs1                  Puppet Labs puppetserver
```
В случае если получили сообщение об ошибки вида:
```
~#apt-get install puppetserver
Reading package lists... Done
Building dependency tree     
Reading state information... Done
    
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.

The following information may help to resolve the situation:
The following packages have unmet dependencies:

puppetserver : Depends: puppet-common (>= 3.7.3-1puppetlabs1)
               Depends: puppet-common (<= 4.0.0-1puppetlabs1)
               Depends: puppet (>= 3.7.3-1puppetlabs1)
               Depends: puppet (<= 4.0.0-1puppetlabs1)
               
E: Unable to correct problems, you have held broken packages.
```
То следует начать с установки puppet, а уж потом установите puppetserver, это починит необходимые зависимости.

Стоит заметить что puppetserver не тянет с собой в зависимостях клиентскую часть puppet-agent. Этот пакет как раз был установлен отдельно из репозитория который был впоследствиии удален.

Для установки клиенской части на отдельном хосте серверная часть не требуется. В целом установка быстра и легка.

##Ошибки с которыми я столкнулся при запуске

Первый запуск произошел весьма болезненно и завершился ничем. В логах как и ожидалось - ошибка, в которой говорится о том, что для запуска JVM недостаточно памяти.

По умолчанию параметр указан как
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
/etc/init.d/puppetserver status

 * puppetserver is running
```







Источники информации:
	- puppet.com
    - Tutorial By Alessandro Franceschi (11-07-2015)
