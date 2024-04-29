## Answers Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**


## Homework

Представьте, что вы стажер в компании "Formatter Inc.".

### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

Создаем в дирректории formatter_lib файл CMakeLists.txt

Пишем в файл:
````
cmake_minimum_required(VERSION 3.4)
project(app)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(formatter_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)
````

Проверим что оно собирается:
````
cmake -B build
````

````
Output: -- Configuring done (0.1s)
    -- Generating done (0.0s)
    -- Build files have been written to: /Users/beamz/Documents/Институт/2 семестр/Технологии и методы програмирования/lab03/formatter_lib/build
````

````
cmake --build build
````

````
Output: [ 50%] Building CXX object CMakeFiles/formatter_lib.dir/formatter.cpp.o
        [100%] Linking CXX static library libformatter_lib.a
        [100%] Built target formatter_lib
````

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

Создаем в дирректории formatter_ex_lib файл CMakeLists.txt


````
cmake_minimum_required(VERSION 3.5)
project(formatter_ex_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib formatter_lib_dir)

add_library(formatter_ex_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)

target_include_directories(formatter_ex_lib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib)

target_link_libraries(formatter_ex_lib formatter_lib)
````

Проверим что оно собирается:
````
cmake -B build
````
````
Output: -- Configuring done (0.1s)
        -- Generating done (0.0s)
        -- Build files have been written to: /Users/beamz/Documents/Институт/2 семестр/Технологии и методы програмирования/lab03/formatter_ex_lib/build
````
````
cmake --build build
````
````
Output:  [ 50%] Built target formatter_lib
        [ 75%] Building CXX object CMakeFiles/formatter_ex_lib.dir/formatter_ex.cpp.o
        [100%] Linking CXX static library libformatter_ex_lib.a
        [100%] Built target formatter_ex_lib
````


### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

1. В дирректории hello_world_application создаем CMakeLists.txt:
````
cmake_minimum_required(VERSION 3.4)

project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib formatter_ex_lib_dir)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)

target_include_directories(hello_world PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib)

target_link_libraries(hello_world formatter_ex_lib)
````
Проверим что оно собирается:
````
cmake -B build
````
````
Output: -- Configuring done (0.1s)
        -- Generating done (0.1s)
        -- Build files have been written to: /Users/beamz/Documents/Институт/2 семестр/Технологии и методы програмирования/lab03/hello_world_application/build
````
````
cmake --build build
````
````
[ 16%] Building CXX object formatter_ex_lib_dir/formatter_lib_dir/CMakeFiles/formatter_lib.dir/formatter.cpp.o
[ 33%] Linking CXX static library libformatter_lib.a
[ 33%] Built target formatter_lib
[ 50%] Building CXX object formatter_ex_lib_dir/CMakeFiles/formatter_ex_lib.dir/formatter_ex.cpp.o
[ 66%] Linking CXX static library libformatter_ex_lib.a
[ 66%] Built target formatter_ex_lib
[ 83%] Building CXX object CMakeFiles/hello_world.dir/hello_world.cpp.o
[100%] Linking CXX executable hello_world
[100%] Built target hello_world
````

Запустим исполняемый файл hello_world в папке build

````
build/hello_world
````
````
Output: -------------------------
        hello, world!
        -------------------------
````
2. Собираем библиотеку solver_lib.

В дирректории solver_lib создаем CMakeLists.txt:
````
cmake_minimum_required(VERSION 3.5)
project(solver_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(solver_lib ${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)
````

В дирректории solver_application создаем CMakeLists.txt:
````
cmake_minimum_required(VERSION 3.5)
project(solver)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib formatter_ex_lib_dir)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib solver_lib_dir)
add_executable(solver ${CMAKE_CURRENT_SOURCE_DIR}/equation.cpp)
target_include_directories(formatter_ex_lib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib ${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib)
target_link_libraries(solver formatter_ex_lib solver_lib)
````
````
cmake -B build
````
````
Output: -- Configuring done (0.1s)
        -- Generating done (0.1s)
        -- Build files have been written to: /Users/beamz/Documents/Институт/2 семестр/Технологии и методы програмирования/lab03/solver_application/build
````
````
cmake --build build
````
````
[ 12%] Building CXX object solver_lib_dir/CMakeFiles/solver_lib.dir/solver.cpp.o
[ 25%] Linking CXX static library libsolver_lib.a
[ 25%] Built target solver_lib
[ 37%] Building CXX object formatter_ex_lib_dir/formatter_lib_dir/CMakeFiles/formatter_lib.dir/formatter.cpp.o
[ 50%] Linking CXX static library libformatter_lib.a
[ 50%] Built target formatter_lib
[ 62%] Building CXX object formatter_ex_lib_dir/CMakeFiles/formatter_ex_lib.dir/formatter_ex.cpp.o
[ 75%] Linking CXX static library libformatter_ex_lib.a
[ 75%] Built target formatter_ex_lib
[ 87%] Building CXX object CMakeFiles/solver.dir/equation.cpp.o
[100%] Linking CXX executable solver
[100%] Built target solver
````
Запустим исполняемый файл:
````
1 -2 1
-------------------------
x1 = 1.000000
-------------------------
-------------------------
x2 = 1.000000
-------------------------
````


```
Copyright (c) 2024 Lozanov Ilia IU8-25
```
