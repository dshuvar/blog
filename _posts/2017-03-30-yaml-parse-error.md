---
published: false
---
## Post about yaml parse error

 Ошибка `can not read a block mapping entry; a multiline key may not be an implicit key`
означает, что параметр в конфигурационном файле не будет прочитан из-за того, что ключ является многострочным и может быть не очевидным. Данная ошибка носит синтаксический характер.
Например в блоке кода параметр `init_connect` многострочный и в него необходимо добавить пробел:

Было:
```
    init_connect:'SET NAMES utf8;set autocommit=1;'
    innodb_additional_mem_pool_size: '200M'
    innodb_buffer_pool_size: '60000M'
```

Стало:
```
    init_connect: 'SET NAMES utf8;set autocommit=1;'
    innodb_additional_mem_pool_size: '200M'
    innodb_buffer_pool_size: '60000M'
```

Для поиска ошибок в yaml файлах я использовал [linter-js-yaml](https://github.com/AtomLinter/linter-js-yaml)