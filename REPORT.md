## Laboratory work VII

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```sh
$ open https://github.com/ruslo/hunter
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
# Устанавливаем значение переменной окружения GITHUB_USERNAME
$ export GITHUB_USERNAME=tishchka
$ alias gsed=sed
```

```sh
# Вход в директорию workspace 
$ cd ${GITHUB_USERNAME}/workspace
# Запоминаем текущий каталог в виртуальном стеке каталогов
$ pushd .
~/tishchka/workspace ~/tishchka/workspace

# Исполняем команду из файла scripts/activate
$ source scripts/activate
```

```sh
# Копируем файлы lab06 из удаленного репозитория github в папку projects/lab07
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07
Cloning into 'projects/lab07'...
remote: Enumerating objects: 43, done.
remote: Counting objects: 100% (43/43), done.
remote: Compressing objects: 100% (24/24), done.
remote: Total 43 (delta 11), reused 43 (delta 11), pack-reused 0
Unpacking objects: 100% (43/43), 6.24 KiB | 798.00 KiB/s, done.

# Переход в папку lab07 
$ cd projects/lab07
# Удаление ссылки на старый удаленный репозиторий
$ git remote remove origin
# Добавляем ссылку на новый репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```

```sh
# Создаем директорию cmake
$ mkdir -p cmake
# Получение архива с удалённого репозитория и схранение его в директорию cmake/HunterGate.cmake
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
--2021-04-22 08:47:12--  https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.111.133, 185.199.108.133, 185.199.109.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17057 (17K) [text/plain]
Saving to: ‘cmake/HunterGate.cmake’

cmake/HunterGate.cm 100%[===================>]  16.66K  --.-KB/s    in 0.001s  

2021-04-22 08:47:12 (26.0 MB/s) - ‘cmake/HunterGate.cmake’ saved [17057/17057]

# Ошибка. Подключаем Hunter в CMakeLists.txt
$ gsed -i '/cmake_minimum_required(VERSION 3.4)/a\

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```

```sh
# Удаляем директорию third-party/gtest
$ git rm -rf third-party/gtest
rm 'third-party/gtest'

# Ошибка. С помощью hunter добавляем GTest в наш проект
$ gsed -i '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt
$ gsed -i 's/add_subdirectory(third-party/gtest)/' CMakeLists.txt
$ gsed -i 's/gtest_main/GTest::gtest_main/' CMakeLists.txt
```

```sh
# Конфигурирование с включенными тестами
$ cmake -H. -B_builds -DBUILD_TESTS=ON
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/tishchka/.hunter
-- [hunter] [ Hunter-ID: 5659b15 | Toolchain-ID: 252be92 | Config-ID: 8a1641b ]
-- [hunter] GTEST_ROOT: /home/tishchka/.hunter/_Base/5659b15/252be92/8a1641b/Install (ver.: 1.10.0)
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/tishchka/workspace/projects/lab07/_builds

# Компиляция
$ cmake --build _builds
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target check
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check


 # Компиляция указанной цели test
$ cmake --build _builds --target test
Running tests...
Test project /home/tishchka/tishchka/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.02 sec

# Выводим содержимое данной директории
$ ls -la $HOME/.hunter
total 12
drwxrwxr-x  3 tishchka tishchka 4096 Feb 24 08:41 .
drwxr-xr-x 35 tishchka tishchka 4096 Apr 17 11:17 ..
drwxrwxr-x  7 tishchka tishchka 4096 Apr 22 08:29 _Base

```

```sh
# Копирование из удалённого репозитроия папки hunter в папку /projects/hunter
$ git clone https://github.com/cpp-pm/hunter $HOME/projects/hunter
Cloning into '/home/tishchka/projects/hunter'...
remote: Enumerating objects: 48198, done.
remote: Counting objects: 100% (206/206), done.
remote: Compressing objects: 100% (114/114), done.
remote: Total 48198 (delta 87), reused 164 (delta 62), pack-reused 47992
Receiving objects: 100% (48198/48198), 12.31 MiB | 3.88 MiB/s, done.
Resolving deltas: 100% (30507/30507), done.

# Создание переменной окружения HUNTER_ROOT c инофрмацией о пути к папке hunter
$ export HUNTER_ROOT=$HOME/projects/hunter
# Удаление папки _build
$ rm -rf _builds
# Конфигурирование с включенными тестами
$ cmake -H. -B_builds -DBUILD_TESTS=ON
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/tishchka/.hunter
-- [hunter] [ Hunter-ID: 5659b15 | Toolchain-ID: 252be92 | Config-ID: 8a1641b ]
-- [hunter] GTEST_ROOT: /home/tishchka/.hunter/_Base/5659b15/252be92/8a1641b/Install (ver.: 1.10.0)
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/tishchka/workspace/projects/lab07/_builds

$ cmake --build _builds
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target check
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check

$ cmake --build _builds --target test
Running tests...
Test project /home/tishchka/tishchka/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec

```

```sh
# Выводит содержимое default.cmake
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest
  hunter_default_version(GTest VERSION 1.7.0-hunter-6)
  hunter_default_version(GTest VERSION 1.10.0)

 # Выводит содержимое hunter.cmake
$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake
# Copyright (c) 2013, Ruslan Baratov
# All rights reserved.

# !!! DO NOT PLACE HEADER GUARDS HERE !!!

include(hunter_add_version)
include(hunter_cacheable)
include(hunter_download)
include(hunter_pick_scheme)
include(hunter_cmake_args)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter.tar.gz"
    SHA1
    1ed1c26d11fb592056c1cb912bd3c784afa96eaa
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-1"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-1.tar.gz"
    SHA1
    0cb1dcf75e144ad052d3f1e4923a7773bf9b494f
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-2"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-2.tar.gz"
    SHA1
    e62b2ef70308f63c32c560f7b6e252442eed4d57
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-3"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-3.tar.gz"
    SHA1
    fea7d3020e20f059255484c69755753ccadf6362
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-4"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-4.tar.gz"
    SHA1
    9b439c0c25437a083957b197ac6905662a5d901b
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-5"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-5.tar.gz"
    SHA1
    796804df3facb074087a4d8ba6f652e5ac69ad7f
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-6"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-6.tar.gz"
    SHA1
    64b93147abe287da8fe4e18cfd54ba9297dafb82
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-7"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-7.tar.gz"
    SHA1
    19b5c98747768bcd0622714f2ed40f17aee406b2
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-8"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-8.tar.gz"
    SHA1
    ac4d2215aa1b1d745a096e5e3b2dbe0c0f229ea5
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-9"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-9.tar.gz"
    SHA1
    8a47fe9c4e550f4ed0e2c05388dd291a059223d9
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-10"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-10.tar.gz"
    SHA1
    374e6dbe8619ab467c6b1a0b470a598407b172e9
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-11"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-11.tar.gz"
    SHA1
    c6ae948ca2bea1d734af01b1069491b00933ed31
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p2
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p2.tar.gz"
    SHA1
    93148cb8850abe78b76ed87158fdb6b9c48e38c4
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p5
    URL https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p5.tar.gz
    SHA1 3325aa4fc8b30e665c9f73a60f19387b7db36f85
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p6
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p6.tar.gz"
    SHA1
    f57096bd01c6f8cbef043b312d4d1e82f29648b6
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p7
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p7.tar.gz"
    SHA1
    4fe083a96d7597f7dce6f453dca01e1d94a1e45b
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p8
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p8.tar.gz"
    SHA1
    1cdd396b20c8d29f7ea08baaa49673b1c261f545
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p9
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p9.tar.gz"
    SHA1
    a345f16cb610e0b5dfa7778dc2852b784cfede5b
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p10
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p10.tar.gz"
    SHA1
    1d92c9f51af756410843b13f8c4e4df09e235394
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.8.0-hunter-p11"
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p11.tar.gz"
    SHA1
    76c6aec038f7d7258bf5c4f45c4817b34039d285
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.8.1"
    URL
    "https://github.com/google/googletest/archive/release-1.8.1.tar.gz"
    SHA1
    152b849610d91a9dfa1401293f43230c2e0c33f8
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.10.0"
    URL
    "https://github.com/google/googletest/archive/release-1.10.0.tar.gz"
    SHA1
    9c89be7df9c5e8cb0bc20b3c4b39bf7e82686770
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.10.0-p0"
    URL
    "https://github.com/hunter-packages/googletest/archive/v1.10.0-p0.tar.gz"
    SHA1
    f7c72be12120e018f53cda0e0daa26fab5da7dfc
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.10.0-p1"
    URL
    "https://github.com/hunter-packages/googletest/archive/v1.10.0-p1.tar.gz"
    SHA1
    06a1f667f200ff94d38b608e44c3c8061c7b8f2f
)

if(HUNTER_GTest_VERSION VERSION_LESS 1.8.0)
  set(_gtest_license "LICENSE")
else()
  set(_gtest_license "googletest/LICENSE")
endif()

# gtest_force_shared_crt prevents GoogleTest from modifying options
# rather than forcing it to use shared libraries
hunter_cmake_args(
    GTest
    CMAKE_ARGS
    HUNTER_INSTALL_LICENSE_FILES=${_gtest_license}
    gtest_force_shared_crt=TRUE
)

hunter_pick_scheme(DEFAULT url_sha1_cmake)
hunter_cacheable(GTest)
hunter_download(PACKAGE_NAME GTest PACKAGE_INTERNAL_DEPS_ID 1)

# Создание папки Hunter в папке cmake
$ mkdir cmake/Hunter
 # Создание файла config.cmake и редактирование его
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
# add LOCAL in HunterGate function
```

```sh
# Создаем директорию demo
$ mkdir demo
# Записываем в созданный там файл main.cpp слудующий код
$ cat > demo/main.cpp <<EOF
#include <print.hpp>

#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF

# Редактирование CMakeLists.txt
$ gsed -i '/endif()/a\

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
' CMakeLists.txt
```

```sh
# Создание директории tools
$ mkdir tools
 # Копирование подмодуля из github в папку tools/polly
$ git submodule add https://github.com/ruslo/polly tools/polly
Cloning into '/home/tishchka/tishchka/workspace/projects/lab07/tools/polly'...
remote: Enumerating objects: 6576, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 6576 (delta 21), reused 19 (delta 16), pack-reused 6546
Receiving objects: 100% (6576/6576), 1.68 MiB | 2.13 MiB/s, done.
Resolving deltas: 100% (4551/4551), done.

# Запуск теста
$ tools/polly/bin/polly.py --test
Python version: 3.8
Build dir: /home/tishchka/tishchka/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.16.3

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/default`
  `-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/default.cmake`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "-H." "-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/default.cmake"

-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/tishchka/projects/hunter
-- [hunter] [ Hunter-ID: xxxxxxx | Toolchain-ID: 252be92 | Config-ID: c62ef00 ]
-- [hunter] GTEST_ROOT: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Install (ver.: 1.10.0)
-- [hunter] Building GTest
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/cache.cmake
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/args.cmake
-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Build
Scanning dependencies of target GTest-Release
[  6%] Creating directories for 'GTest-Release'
[ 12%] Performing download step (download, verify and extract) for 'GTest-Release'
-- Downloading...
   dst='/home/tishchka/projects/hunter/_Base/Download/GTest/1.10.0/9c89be7/release-1.10.0.tar.gz'
   timeout='none'
-- Using src='https://github.com/google/googletest/archive/release-1.10.0.tar.gz'
-- [download 100% complete]
-- [download 1% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 11% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 16% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 99% complete]
-- [download 100% complete]
-- verifying file...
       file='/home/tishchka/projects/hunter/_Base/Download/GTest/1.10.0/9c89be7/release-1.10.0.tar.gz'
-- Downloading... done
-- extracting...
     src='/home/tishchka/projects/hunter/_Base/Download/GTest/1.10.0/9c89be7/release-1.10.0.tar.gz'
     dst='/home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 18%] No patch step for 'GTest-Release'
[ 25%] No update step for 'GTest-Release'
[ 31%] Performing configure step for 'GTest-Release'
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/cache.cmake
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/args.cmake
-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PythonInterp: /usr/bin/python3.8 (found version "3.8.5")
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Build/GTest-Release-prefix/src/GTest-Release-build
[ 37%] Performing build step for 'GTest-Release'
Scanning dependencies of target gtest
[ 12%] Building CXX object googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 25%] Linking CXX static library ../lib/libgtest.a
[ 25%] Built target gtest
Scanning dependencies of target gmock
Scanning dependencies of target gtest_main
[ 37%] Building CXX object googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 50%] Building CXX object googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 62%] Linking CXX static library ../lib/libgtest_main.a
[ 62%] Built target gtest_main
[ 75%] Linking CXX static library ../lib/libgmock.a
[ 75%] Built target gmock
Scanning dependencies of target gmock_main
[ 87%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../lib/libgmock_main.a
[100%] Built target gmock_main
[ 43%] Performing install step for 'GTest-Release'
[ 25%] Built target gtest
[ 50%] Built target gmock
[ 75%] Built target gmock_main
[100%] Built target gtest_main
Install the project...
-- Install configuration: "Release"
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-function-mockers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-nice-strict.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-function-mockers.h.pump
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-actions.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-function-mocker.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-cardinalities.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-more-matchers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-matchers.h.pump
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/gmock-pp.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/gmock-internal-utils.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/gmock-port.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-port.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/README.md
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-generated-actions.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-generated-actions.h.pump
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-matchers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-more-actions.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-actions.h.pump
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-spec-builders.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-actions.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-matchers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-matchers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgmock.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgmock_main.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gmock.pc
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gmock_main.pc
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestTargets.cmake
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestTargets-release.cmake
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestConfigVersion.cmake
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestConfig.cmake
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-message.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-death-test.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest_pred_impl.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-matchers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest_prod.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-test-part.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-type-util.h.pump
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-port-arch.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-param-util.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-death-test-internal.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-port.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-type-util.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-internal.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-string.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/README.md
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/gtest-port.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/gtest-printers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/gtest.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-filepath.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-spi.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-typed-test.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-param-test.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-printers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgtest.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgtest_main.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gtest.pc
-- Installing: /home/gtishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gtest_main.pc
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/args.cmake
[ 50%] Completed 'GTest-Release'
[ 50%] Built target GTest-Release
Scanning dependencies of target GTest-Debug
[ 56%] Creating directories for 'GTest-Debug'
[ 62%] Performing download step (download, verify and extract) for 'GTest-Debug'
-- verifying file...
       file='/home/tishchka/projects/hunter/_Base/Download/GTest/1.10.0/9c89be7/release-1.10.0.tar.gz'
-- File already exists and hash match (skip download):
  file='/home/tishchka/projects/hunter/_Base/Download/GTest/1.10.0/9c89be7/release-1.10.0.tar.gz'
  SHA1='9c89be7df9c5e8cb0bc20b3c4b39bf7e82686770'
-- extracting...
     src='/home/tishchka/projects/hunter/_Base/Download/GTest/1.10.0/9c89be7/release-1.10.0.tar.gz'
     dst='/home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 68%] No patch step for 'GTest-Debug'
[ 75%] No update step for 'GTest-Debug'
[ 81%] Performing configure step for 'GTest-Debug'
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/cache.cmake
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/args.cmake
-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PythonInterp: /usr/bin/python3.8 (found version "3.8.5")
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Build/GTest-Debug-prefix/src/GTest-Debug-build
[ 87%] Performing build step for 'GTest-Debug'
Scanning dependencies of target gtest
[ 12%] Building CXX object googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 25%] Linking CXX static library ../lib/libgtestd.a
[ 25%] Built target gtest
Scanning dependencies of target gtest_main
Scanning dependencies of target gmock
[ 37%] Building CXX object googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Building CXX object googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 62%] Linking CXX static library ../lib/libgtest_maind.a
[ 62%] Built target gtest_main
[ 75%] Linking CXX static library ../lib/libgmockd.a
[ 75%] Built target gmock
Scanning dependencies of target gmock_main
[ 87%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../lib/libgmock_maind.a
[100%] Built target gmock_main
[ 93%] Performing install step for 'GTest-Debug'
[ 25%] Built target gtest
[ 50%] Built target gmock
[ 75%] Built target gmock_main
[100%] Built target gtest_main
Install the project...
-- Install configuration: "Debug"
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-function-mockers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-nice-strict.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-function-mockers.h.pump
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-actions.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-function-mocker.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-cardinalities.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-more-matchers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-matchers.h.pump
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/gmock-pp.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/gmock-internal-utils.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/gmock-port.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-port.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/README.md
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-generated-actions.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-generated-actions.h.pump
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/internal/custom/gmock-matchers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-more-actions.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-actions.h.pump
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-spec-builders.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-actions.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-generated-matchers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gmock/gmock-matchers.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgmockd.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgmock_maind.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gmock.pc
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gmock_main.pc
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestTargets.cmake
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestTargets-debug.cmake
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestConfigVersion.cmake
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/cmake/GTest/GTestConfig.cmake
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-message.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-death-test.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest_pred_impl.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-matchers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest_prod.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-test-part.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-type-util.h.pump
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-port-arch.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-param-util.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-death-test-internal.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-port.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-type-util.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-internal.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-string.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/README.md
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/gtest-port.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/gtest-printers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/custom/gtest.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/internal/gtest-filepath.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-spi.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-typed-test.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-param-test.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest-printers.h
-- Up-to-date: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/include/gtest/gtest.h
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgtestd.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/libgtest_maind.a
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gtest.pc
-- Installing: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/Install/lib/pkgconfig/gtest_main.pc
loading initial cache file /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest/args.cmake
[100%] Completed 'GTest-Debug'
[100%] Built target GTest-Debug
-- [hunter] Build step successful (dir: /home/tishchka/projects/hunter/_Base/xxxxxxx/252be92/c62ef00/Build/GTest)
-- [hunter] Cache saved: /home/tishchka/projects/hunter/_Base/Cache/raw/bfdd3f5abda49c9207f9752fc342a31521ca776b.tar.bz2
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/tishchka/workspace/projects/lab07/_builds/default
Execute command: [
  `cmake`
  `--build`
  `/home/tishchka/tishchka/workspace/projects/lab07/_builds/default`
  `--`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "--build" "/home/tishchka/tishchka/workspace/projects/lab07/_builds/default" "--"

Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target demo
[ 75%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Run tests
Execute command: [
  `ctest`
]

[/home/tishchka/tishchka/workspace/projects/lab07/_builds/default]> "ctest"

*********************************
No test configuration file found!
*********************************
Usage

  ctest [options]

-
Log saved: /home/tishchka/tishchka/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:01:30.062289s
Build: 0:00:03.120747s
Test: 0:00:00.075186s
-
Total: 0:01:33.259540s
-
SUCCESS

$ tools/polly/bin/polly.py --install
Python version: 3.8
Build dir: /home/tishchka/tishchka/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.16.3

CMake suite maintained and supported by Kitware (kitware.com/cmake).

== WARNING ==

Looks like cmake arguments changed. You have two options to fix it:
  * Remove build directory completely by adding '--clear' (works 100%)
  * Run configure again by adding '--reconfig' (you must understand how CMake cache variables works/updated)

- "cmake" "-H." "-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/default.cmake"
+ "cmake" "-H." "-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/default.cmake" "-DCMAKE_INSTALL_PREFIX=/home/tishchka/tishchka/workspace/projects/lab07/_install/default"
?                                                                                                                                                                                           +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

$ tools/polly/bin/polly.py --toolchain clang-cxx14
Python version: 3.8
Build dir: /home/gndavydov/GNDavydov/workspace/projects/lab07/_builds/clang-cxx14
Execute command: [
  `which`
  `cmake`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.16.3

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/clang-cxx14`
  `-GUnix Makefiles`
  `-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/clang-cxx14.cmake`
]

[/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "-H." "-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/clang-cxx14" "-GUnix Makefiles" "-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/clang-cxx14.cmake"

-- [polly] Used toolchain: clang / c++14 support

clang not found

CMake Error at tools/polly/utilities/polly_fatal_error.cmake:10 (message):
Call Stack (most recent call first):
  tools/polly/compiler/clang.cmake:23 (polly_fatal_error)
  tools/polly/clang-cxx14.cmake:20 (include)
  /usr/share/cmake-3.16/Modules/CMakeDetermineSystem.cmake:93 (include)
  CMakeLists.txt:15 (project)


CMake Error: CMake was unable to find a build program corresponding to "Unix Makefiles".  CMAKE_MAKE_PROGRAM is not set.  You probably need to select a different build tool.
-- Configuring incomplete, errors occurred!
Command exit with status "1": [/home/tishchka/tishchka/workspace/projects/lab07]> "cmake" "-H." "-B/home/tishchka/tishchka/workspace/projects/lab07/_builds/clang-cxx14" "-GUnix Makefiles" "-DCMAKE_TOOLCHAIN_FILE=/home/tishchka/tishchka/workspace/projects/lab07/tools/polly/clang-cxx14.cmake"

Log: /home/tishchka/tishchka/workspace/projects/lab07/_logs/polly/clang-cxx14/log.txt
*** FAILED ***

```

## Report

```sh
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

### Задание
1. Создайте cвой hunter-пакет.

## Links

- [Create Hunter package](https://docs.hunter.sh/en/latest/creating-new/create.html)
- [Custom Hunter config](https://github.com/ruslo/hunter/wiki/example.custom.config.id)
- [Polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2021 The ISC Authors
```
