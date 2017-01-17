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

Однако мой опыт установки связан с тем, что т.к мне были нужны не самые свеживе версии, а по версии 3.8.
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


Источники информации:
	- puppet.com
    - Tutorial By Alessandro Franceschi (11-07-2015)
