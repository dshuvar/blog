---
title: Введение в puppet
date: {}
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
	- [Facter](https://docs.puppet.com/facter/) - Утилита для извлечения данных системы
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

Источники информации:
	- puppet.com
    - Tutorial By Alessandro Franceschi (11-07-2015)
