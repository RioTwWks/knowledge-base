# Команды Linux

### [Главная][1]

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
~$  -r /tmp/BMW
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










### [Главная][1]

[1]: /knowledge-base/
