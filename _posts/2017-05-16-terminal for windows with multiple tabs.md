---
published: false
categories: work
tags: 'enviroment, terminal'
date: '2017-03-30 16:43:00 +0300'
---
## FaTTY is new tabbed terminal for cygwin based on mintty for ex-unix users  

FaTTY ([link to github](https://github.com/juho-p/fatty))новая разновидность unix-like эмулятора терминала для [cygwin](http://www.cygwin.com/) на основе [mintty](https://mintty.github.io/) предназначенный для пользователей windows os, который поможет пережить переезд на новую ОС и уменьшить страдания.

Настройка терминала описана буквально парой строк в readme:

```
To install, run cygwin setup*.exe and have at least following packages marked for install:

    gcc-g++
    make
    w32api-headers
    git

Then, in Cygwin terminal run following commands:

git clone https://github.com/juho-p/fatty.git
cd fatty
make
cp src/fatty.exe /bin

You can then try running it by typing fatty

Then you probably want to create shortcut to your Windows desktop to run fatty. After that, you have decent terminal with tabs for your Cygwin!
```

