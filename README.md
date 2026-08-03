# clickhouse_odbc_astra_linux
Работая с сервером на astra linux я столкнулся с проблемой установки на него odbc драйвера под click house.
Мне подошел только "Release 1.1.6.20200320", но при запросах с базе данных данный драйвер выдавал ошибку: "[HY090]Invalid string or buffer length"
Данную проблему я решал долго и вот какое решение нашел.

Необходимо самостоятельно сбилдить драйвер на сервере для этого нужно:
1. Используя команду скопировать репозиторий разработчиков со всеми сабмодулями:  
```
sudo apt install git
git clone --recursive git@github.com:ClickHouse/clickhouse-odbc.git
```
2. Далее в корневой папке ./clickhouse-odbc в файле "CMakeLists.txt" заменить "set (CMAKE_CXX_STANDARD 23)" на "set (CMAKE_CXX_STANDARD 20)"
3. Установить компилятор "clang-19" (можно и более новую версию) для этого нужно выполнить команду:
```
sudo apt install build-essential libpoco-dev libssl-dev libicu-dev unixodbc-dev
sudo apt-get install clang-19 clang++-19
```
4. Далее нужно использовать cmake, но если установить его через "apt-get", то установиться старая версия, которая не сможет сбилдить драйвер, поэтому нужно установить с официального сайта: "https://cmake.org/download/" актуальную версию cmake через распаковку пакета и использовать службу в папке bin, например, "~/cmake-X.X.X-linux-x86_64/bin/cmake", где
- X.X.X - версия cmake

После начал сбоку проекта для этого выполнил следующие команды
```
cd clickhouse-odbc
mkdir build
cd build

~/cmake-4.4.2-linux-x86_64/bin/cmake .. -DCMAKE_C_COMPILER=clang-19 -DCMAKE_CXX_COMPILER=clang++-19 -DCMAKE_CXX_STANDARD=20 -DCMAKE_CXX_STANDARD_REQUIRED=ON
```

После сбилдил драйверы командой:
```
~/cmake-4.4.2-linux-x86_64/bin/cmake --build . --config RelWithDebInfo
```
