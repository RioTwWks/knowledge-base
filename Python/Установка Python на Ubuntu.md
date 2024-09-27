# Установка Python на Ubuntu

## Установка Python3 из DeadSnakes PPA

```cmd
sudo apt update
```

```cmd
sudo apt install build-essential software-properties-common -y
```

```cmd
sudo add-apt-repository ppa:deadsnakes/ppa
```

```cmd
sudo apt update
```

Прописываем нужную версию

```cmd
sudo apt install python3.12 -y
```

Проверка

```cmd
python3.12 --version
```

## Установка Python3 из исходного кода

```cmd
sudo apt update
```

Установка зависимостей, необходимых для сборки и компилирования кода Python.

```cmd
sudo apt install build-essential software-properties-common libssl-dev libffi-dev python3-dev libgdbm-dev libc6-dev libbz2-dev libsqlite3-dev tk-dev libffi-dev zlib1g-dev -y
```

Переходим на [страницу загрузки Python](https://www.python.org/downloads/release/python-3114/) и загрузите последнюю версию Python с помощью команды wget:

```cmd
wget https://www.python.org/ftp/python/3.11.4/Python-3.11.4.tgz
```

После загрузки проверьте целостность файла tarball, проверив его контрольную сумму MD5.

```cmd
md5dsum Python-3.11.4.tgz
```

Далее извлекаем tgz-файл.

```cmd
tar  -xvf Python-3.11.4.tgz
```

Далее переходим в разархивированную папку:

```cmd
cd Python-3.11.4/
```

Выполняем команду `configure`. Команда выполняет тщательный тест, чтобы проверить, соблюдены ли все зависимости для установки Python3.

```cmd
sudo ./configure --enable-optimizations
```

Далее запускаем процесс сборки.

```cmd
sudo make -j 2
```

В `-j` параметре указывается количество ядер процессора в вашей системе. Узнать можно командой `nproc`.

Наконец, установите двоичные файлы Python, как показано. Команда `altinstall` гарантирует, что библиотека Python по умолчанию в вашей системе не будет перезаписана.

```cmd
sudo make altinstall
```

## Установка pip

```cmd
sudo apt install -y python3-pip
```