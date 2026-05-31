[![CMake CI](https://github.com/EighthBroom3439/lab7/actions/workflows/ci.yml/badge.svg)](https://github.com/EighthBroom3439/lab7/actions/workflows/ci.yml)
# Laboratory work VII

Цель данной лабораторной работы заключалась в том, чтобы ознакомиться с системами управления пакетами на примере Hunter, однако для удобства и из-за того, что Hunter устарел, было принято решение использовать FetchContent.
B данной работе было выполнено следующее:

## 1. Был скопирован репозиторий из предыдущей лабораторной работы
```bash
vdeo@deo:~$ export GITHUB_USERNAME=EighthBroom3439
vdeo@deo:~$ cd ${GITHUB_USERNAME}/workspace/
vdeo@deo:~/EighthBroom3439/workspace$ git clone https://github.com/${GITHUB_USERNAME}/lab6 projects/lab07
Клонирование в «projects/lab07»...
remote: Enumerating objects: 120, done.
remote: Counting objects: 100% (120/120), done.
remote: Compressing objects: 100% (61/61), done.
remote: Total 120 (delta 42), reused 119 (delta 41), pack-reused 0 (from 0)
Получение объектов: 100% (120/120), 36.78 КиБ | 523.00 КиБ/с, готово.
Определение изменений: 100% (42/42), готово.
vdeo@deo:~/EighthBroom3439/workspace$ cd projects/lab07
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ git remote remove origin
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab7
```

## 2. Был отредактирован файл CMakeLists.txt
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cat CMakeLists.txt 
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)
option(BUILD_TESTS "Build tests" ON)

project(print)

set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION_TWEAK 0)
set(PRINT_VERSION
  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        v1.15.2
)
FetchContent_MakeAvailable(googletest)

if(BUILD_TESTS)
  enable_testing()
  file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check ${PROJECT_NAME} GTest::gtest_main)
  add_test(NAME check COMMAND check)
endif()

include(CPackConfig.cmake)
```

## 3. Была произведена ручная сборка и запущен тест
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cmake -H. -B_builds -DBUILD_TESTS=ON
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- The C compiler identification is GNU 14.2.0
-- The CXX compiler identification is GNU 14.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (9.4s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cmake --build _builds
[  8%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 16%] Linking CXX static library libprint.a
[ 16%] Built target print
[ 25%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 33%] Linking CXX static library ../../../lib/libgtest.a
[ 33%] Built target gtest
[ 41%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library ../../../lib/libgtest_main.a
[ 50%] Built target gtest_main
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
[ 75%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 83%] Linking CXX static library ../../../lib/libgmock.a
[ 83%] Built target gmock
[ 91%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cmake --build _builds --target test
Running tests...
Test project /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.04 sec
```
## 4. Также было проверено, что FetchContent успешно скачал и собрал GTest

```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ ls -la _builds/_deps/
итого 20
drwxrwxr-x 5 vdeo vdeo 4096 мая 31 15:02 .
drwxrwxr-x 7 vdeo vdeo 4096 мая 31 15:03 ..
drwxrwxr-x 5 vdeo vdeo 4096 мая 31 15:02 googletest-build
drwxrwxr-x 8 vdeo vdeo 4096 мая 31 15:02 googletest-src
drwxrwxr-x 4 vdeo vdeo 4096 мая 31 15:02 googletest-subbuild
```

## 5. Был написан demo
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ mkdir -p demo
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cat > demo/main.cpp << 'EOF'
> #include <print.hpp>
> #include <cstdlib>
> #include <iostream>
> #include <fstream>
> 
> int main(int argc, char* argv[])
> {
>     const char* log_path = std::getenv("LOG_PATH");
>     if (log_path == nullptr)
>     {
>         std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
>         return 1;
>     }
>     std::string text;
>     while (std::cin >> text)
>     {
>         std::ofstream out(log_path, std::ios_base::app);
>         print(text, out);
>         out << std::endl;
>     }
>     return 0;
> }
> EOF
```

## 6. Был отредактирован CMakeLists.txt
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cat >> CMakeLists.txt << 'EOF'
> 
> add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
> target_link_libraries(demo print)
> install(TARGETS demo RUNTIME DESTINATION bin)
> EOF
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ tail -n 5 CMakeLists.txt 
include(CPackConfig.cmake)

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
```

## 7. Проект был пересобран для включения demo в себя
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ rm -rf _builds
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cmake -H. -B_builds -DBUILD_TESTS=ON
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- The C compiler identification is GNU 14.2.0
-- The CXX compiler identification is GNU 14.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (8.2s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ cmake --build _builds
[  7%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 14%] Linking CXX static library libprint.a
[ 14%] Built target print
[ 21%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 28%] Linking CXX static library ../../../lib/libgtest.a
[ 28%] Built target gtest
[ 35%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 42%] Linking CXX static library ../../../lib/libgtest_main.a
[ 42%] Built target gtest_main
[ 50%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 57%] Linking CXX executable check
[ 57%] Built target check
[ 64%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[ 71%] Linking CXX executable demo
[ 71%] Built target demo
[ 78%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 85%] Linking CXX static library ../../../lib/libgmock.a
[ 85%] Built target gmock
[ 92%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
```

## 8. Был скачан Polly и проведено тестирование проекта через него
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ mkdir -p tools
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ git submodule add https://github.com/ruslo/polly tools/polly
Клонирование в «/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly»...
remote: Enumerating objects: 6578, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 6578 (delta 21), reused 17 (delta 17), pack-reused 6549 (from 2)
Получение объектов: 100% (6578/6578), 1.68 МиБ | 2.18 МиБ/с, готово.
Определение изменений: 100% (4551/4551), готово.
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ tools/polly/bin/polly.py --test
Python version: 3.13
Build dir: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.31.6

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default`
  `-DCMAKE_TOOLCHAIN_FILE=/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly/default.cmake`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "-H." "-B/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly/default.cmake"

CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 14.2.0
-- The CXX compiler identification is GNU 14.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (15.2s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default
Execute command: [
  `cmake`
  `--build`
  `/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default`
  `--`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "--build" "/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default" "--"

[  7%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 14%] Linking CXX static library libprint.a
[ 14%] Built target print
[ 21%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 28%] Linking CXX static library ../../../lib/libgtest.a
[ 28%] Built target gtest
[ 35%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 42%] Linking CXX static library ../../../lib/libgtest_main.a
[ 42%] Built target gtest_main
[ 50%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 57%] Linking CXX executable check
[ 57%] Built target check
[ 64%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[ 71%] Linking CXX executable demo
[ 71%] Built target demo
[ 78%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 85%] Linking CXX static library ../../../lib/libgmock.a
[ 85%] Built target gmock
[ 92%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
Run tests
Execute command: [
  `ctest`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default]> "ctest"

Test project /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec
-
Log saved: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:16.259036s
Build: 0:00:11.514217s
Test: 0:00:00.019347s
-
Total: 0:00:27.792975s
-
SUCCESS
```

## 9. Проект был устновлен посредством Polly
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ tools/polly/bin/polly.py --install --reconfig
Python version: 3.13
Build dir: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.31.6

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default`
  `-DCMAKE_TOOLCHAIN_FILE=/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly/default.cmake`
  `-DCMAKE_INSTALL_PREFIX=/home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "-H." "-B/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly/default.cmake" "-DCMAKE_INSTALL_PREFIX=/home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default"

CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
-- [polly] Used toolchain: Default
  to work with policies introduced by <max> or earlier.


-- Configuring done (0.4s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default
Execute command: [
  `cmake`
  `--build`
  `/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default`
  `--target`
  `install`
  `--`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "--build" "/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/default" "--target" "install" "--"

[ 14%] Built target print
[ 28%] Built target gtest
[ 42%] Built target gtest_main
[ 57%] Built target check
[ 71%] Built target demo
[ 85%] Built target gmock
[100%] Built target gmock_main
Install the project...
-- Install configuration: ""
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/libprint.a
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/print.hpp
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/cmake/print-config.cmake
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/cmake/print-config-noconfig.cmake
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/bin/demo
-- Up-to-date: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-actions.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/gmock-internal-utils.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/gmock-pp.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/custom
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/custom/gmock-generated-actions.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/custom/README.md
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/custom/gmock-matchers.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/custom/gmock-port.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/internal/gmock-port.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-cardinalities.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-nice-strict.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-function-mocker.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-matchers.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-spec-builders.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-more-actions.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gmock/gmock-more-matchers.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/libgmock.a
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/libgmock_main.a
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/pkgconfig/gmock.pc
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/pkgconfig/gmock_main.pc
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/cmake/GTest/GTestTargets.cmake
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/cmake/GTest/GTestTargets-noconfig.cmake
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/cmake/GTest/GTestConfigVersion.cmake
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/cmake/GTest/GTestConfig.cmake
-- Up-to-date: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-death-test.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-message.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest_prod.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-type-util.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-param-util.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-port-arch.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-death-test-internal.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-port.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-filepath.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-internal.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/gtest-string.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/custom
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/custom/gtest.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/custom/gtest-port.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/custom/gtest-printers.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/internal/custom/README.md
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-assertion-result.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-typed-test.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-test-part.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-printers.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest_pred_impl.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-matchers.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-param-test.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/include/gtest/gtest-spi.h
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/libgtest.a
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/libgtest_main.a
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/pkgconfig/gtest.pc
-- Installing: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_install/default/lib/pkgconfig/gtest_main.pc
-
Log saved: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:01.466855s
Build: 0:00:01.336833s
-
Total: 0:00:02.803837s
-
SUCCESS
```

Отдельная структура установленных файлов
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ tree _install/default
_install/default
├── bin
│   └── demo
├── cmake
│   ├── print-config.cmake
│   └── print-config-noconfig.cmake
├── include
│   ├── gmock
│   │   ├── gmock-actions.h
│   │   ├── gmock-cardinalities.h
│   │   ├── gmock-function-mocker.h
│   │   ├── gmock.h
│   │   ├── gmock-matchers.h
│   │   ├── gmock-more-actions.h
│   │   ├── gmock-more-matchers.h
│   │   ├── gmock-nice-strict.h
│   │   ├── gmock-spec-builders.h
│   │   └── internal
│   │       ├── custom
│   │       │   ├── gmock-generated-actions.h
│   │       │   ├── gmock-matchers.h
│   │       │   ├── gmock-port.h
│   │       │   └── README.md
│   │       ├── gmock-internal-utils.h
│   │       ├── gmock-port.h
│   │       └── gmock-pp.h
│   ├── gtest
│   │   ├── gtest-assertion-result.h
│   │   ├── gtest-death-test.h
│   │   ├── gtest.h
│   │   ├── gtest-matchers.h
│   │   ├── gtest-message.h
│   │   ├── gtest-param-test.h
│   │   ├── gtest_pred_impl.h
│   │   ├── gtest-printers.h
│   │   ├── gtest_prod.h
│   │   ├── gtest-spi.h
│   │   ├── gtest-test-part.h
│   │   ├── gtest-typed-test.h
│   │   └── internal
│   │       ├── custom
│   │       │   ├── gtest.h
│   │       │   ├── gtest-port.h
│   │       │   ├── gtest-printers.h
│   │       │   └── README.md
│   │       ├── gtest-death-test-internal.h
│   │       ├── gtest-filepath.h
│   │       ├── gtest-internal.h
│   │       ├── gtest-param-util.h
│   │       ├── gtest-port-arch.h
│   │       ├── gtest-port.h
│   │       ├── gtest-string.h
│   │       └── gtest-type-util.h
│   └── print.hpp
└── lib
    ├── cmake
    │   └── GTest
    │       ├── GTestConfig.cmake
    │       ├── GTestConfigVersion.cmake
    │       ├── GTestTargets.cmake
    │       └── GTestTargets-noconfig.cmake
    ├── libgmock.a
    ├── libgmock_main.a
    ├── libgtest.a
    ├── libgtest_main.a
    ├── libprint.a
    └── pkgconfig
        ├── gmock_main.pc
        ├── gmock.pc
        ├── gtest_main.pc
        └── gtest.pc

14 directories, 57 files
```

## 10. Был установлен clang
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ sudo apt update
Пол:1 http://deb.debian.org/debian trixie InRelease [140 kB]
Пол:2 http://security.debian.org/debian-security trixie-security InRelease [43,4 kB]
Пол:3 http://deb.debian.org/debian trixie-updates InRelease [47,3 kB]          
Пол:4 http://security.debian.org/debian-security trixie-security/main Sources [170 kB]
Пол:5 http://deb.debian.org/debian trixie/main Sources [10,5 MB]               
Сущ:6 https://download.sublimetext.com apt/stable/ InRelease                   
Пол:7 http://security.debian.org/debian-security trixie-security/main amd64 Packages [199 kB]
Пол:8 http://security.debian.org/debian-security trixie-security/main Translation-en [120 kB]
Пол:9 http://deb.debian.org/debian trixie/main amd64 Packages [9 671 kB]       
Пол:10 http://deb.debian.org/debian trixie/main Translation-en [6 485 kB]
Получено 27,4 MB за 7с (4 096 kB/s)                                            
Может быть обновлено 224 пакета. Запустите «apt list --upgradable» для их показа.
Уведомление: Репозиторий «http://deb.debian.org/debian trixie InRelease» изменил значение поля «Version» с «13.4» на «13.5»
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ sudo apt install clang --fix-missing
Обновление:                                                       
  libc-bin  libc-dev-bin  libc-l10n  libc6  libc6-dev  locales

Установка:
  clang

Установка зависимостей:
  clang-19                libclang1-19    libz3-dev
  clang-tools-19          libffi-dev      llvm-19
  lib32gcc-s1             libgc1          llvm-19-dev
  lib32stdc++6            libncurses-dev  llvm-19-linker-tools
  libc6-i386              libobjc-14-dev  llvm-19-runtime
  libclang-common-19-dev  libobjc4        llvm-19-tools
  libclang-cpp19          libpfm4         python3-pygments
  libclang-rt-19-dev      libxml2-dev     python3-yaml

Предлагаемые пакеты:
  clang-19-doc  ncurses-doc  llvm-19-doc          ttf-bitstream-vera
  wasi-libc     pkg-config   python-pygments-doc

Сводка:
  Обновление: 6, Установка: 25, Удаление: 0, Пропуск обновления: 218
  Объём загрузки: 12,7 MB / 113 MB
  Требуемое пространство: 689 MB / 21,9 GB доступно

Продолжить? [Д/н] Д
Пол:1 http://deb.debian.org/debian trixie/main amd64 libc6-dev amd64 2.41-12+deb13u3 [1 992 kB]
Пол:2 http://deb.debian.org/debian trixie/main amd64 libc-dev-bin amd64 2.41-12+deb13u3 [59,8 kB]
Пол:3 http://deb.debian.org/debian trixie/main amd64 libc6 amd64 2.41-12+deb13u3 [2 850 kB]
Пол:4 http://deb.debian.org/debian trixie/main amd64 libc-bin amd64 2.41-12+deb13u3 [638 kB]
Пол:5 http://deb.debian.org/debian trixie/main amd64 libc-l10n all 2.41-12+deb13u3 [740 kB]
Пол:6 http://deb.debian.org/debian trixie/main amd64 locales all 2.41-12+deb13u3 [3 925 kB]
Пол:7 http://deb.debian.org/debian trixie/main amd64 libc6-i386 amd64 2.41-12+deb13u3 [2 544 kB]
Получено 12,7 MB за 2с (5 147 kB/s)        
Чтение журналов изменений... Выполнено
Извлекаются шаблоны из пакетов: 100%
Предварительная настройка пакетов …
(Чтение базы данных … на данный момент установлено 141412 файлов и каталогов.)
Подготовка к распаковке …/libc6-dev_2.41-12+deb13u3_amd64.deb …
Распаковывается libc6-dev:amd64 (2.41-12+deb13u3) на замену (2.41-12+deb13u2) …
Подготовка к распаковке …/libc-dev-bin_2.41-12+deb13u3_amd64.deb …
Распаковывается libc-dev-bin (2.41-12+deb13u3) на замену (2.41-12+deb13u2) …
Подготовка к распаковке …/libc6_2.41-12+deb13u3_amd64.deb …
Распаковывается libc6:amd64 (2.41-12+deb13u3) на замену (2.41-12+deb13u2) …
Настраивается пакет libc6:amd64 (2.41-12+deb13u3) …
(Чтение базы данных … на данный момент установлено 141412 файлов и каталогов.)
Подготовка к распаковке …/libc-bin_2.41-12+deb13u3_amd64.deb …
Распаковывается libc-bin (2.41-12+deb13u3) на замену (2.41-12+deb13u2) …
Настраивается пакет libc-bin (2.41-12+deb13u3) …
(Чтение базы данных … на данный момент установлено 141412 файлов и каталогов.)
Подготовка к распаковке …/00-libc-l10n_2.41-12+deb13u3_all.deb …
Распаковывается libc-l10n (2.41-12+deb13u3) на замену (2.41-12+deb13u2) …
Подготовка к распаковке …/01-locales_2.41-12+deb13u3_all.deb …
Распаковывается locales (2.41-12+deb13u3) на замену (2.41-12+deb13u2) …
Выбор ранее не выбранного пакета libclang-cpp19.
Подготовка к распаковке …/02-libclang-cpp19_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается libclang-cpp19 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета libgc1:amd64.
Подготовка к распаковке …/03-libgc1_1%3a8.2.8-1_amd64.deb …
Распаковывается libgc1:amd64 (1:8.2.8-1) …
Выбор ранее не выбранного пакета libobjc4:amd64.
Подготовка к распаковке …/04-libobjc4_14.2.0-19_amd64.deb …
Распаковывается libobjc4:amd64 (14.2.0-19) …
Выбор ранее не выбранного пакета libobjc-14-dev:amd64.
Подготовка к распаковке …/05-libobjc-14-dev_14.2.0-19_amd64.deb …
Распаковывается libobjc-14-dev:amd64 (14.2.0-19) …
Выбор ранее не выбранного пакета libclang-common-19-dev:amd64.
Подготовка к распаковке …/06-libclang-common-19-dev_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается libclang-common-19-dev:amd64 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета llvm-19-linker-tools.
Подготовка к распаковке …/07-llvm-19-linker-tools_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается llvm-19-linker-tools (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета libclang1-19.
Подготовка к распаковке …/08-libclang1-19_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается libclang1-19 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета clang-19.
Подготовка к распаковке …/09-clang-19_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается clang-19 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета clang.
Подготовка к распаковке …/10-clang_1%3a19.0-63_amd64.deb …
Распаковывается clang (1:19.0-63) …
Выбор ранее не выбранного пакета clang-tools-19.
Подготовка к распаковке …/11-clang-tools-19_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается clang-tools-19 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета libc6-i386.
Подготовка к распаковке …/12-libc6-i386_2.41-12+deb13u3_amd64.deb …
Распаковывается libc6-i386 (2.41-12+deb13u3) …
Выбор ранее не выбранного пакета lib32gcc-s1.
Подготовка к распаковке …/13-lib32gcc-s1_14.2.0-19_amd64.deb …
Распаковывается lib32gcc-s1 (14.2.0-19) …
Выбор ранее не выбранного пакета lib32stdc++6.
Подготовка к распаковке …/14-lib32stdc++6_14.2.0-19_amd64.deb …
Распаковывается lib32stdc++6 (14.2.0-19) …
Выбор ранее не выбранного пакета libclang-rt-19-dev:amd64.
Подготовка к распаковке …/15-libclang-rt-19-dev_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается libclang-rt-19-dev:amd64 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета libffi-dev:amd64.
Подготовка к распаковке …/16-libffi-dev_3.4.8-2_amd64.deb …
Распаковывается libffi-dev:amd64 (3.4.8-2) …
Выбор ранее не выбранного пакета libncurses-dev:amd64.
Подготовка к распаковке …/17-libncurses-dev_6.5+20250216-2_amd64.deb …
Распаковывается libncurses-dev:amd64 (6.5+20250216-2) …
Выбор ранее не выбранного пакета libpfm4:amd64.
Подготовка к распаковке …/18-libpfm4_4.13.0+git99-gc5587f9-1_amd64.deb …
Распаковывается libpfm4:amd64 (4.13.0+git99-gc5587f9-1) …
Выбор ранее не выбранного пакета libxml2-dev:amd64.
Подготовка к распаковке …/19-libxml2-dev_2.12.7+dfsg+really2.9.14-2.1+deb13u2_am
d64.deb …
Распаковывается libxml2-dev:amd64 (2.12.7+dfsg+really2.9.14-2.1+deb13u2) …
Выбор ранее не выбранного пакета libz3-dev:amd64.
Подготовка к распаковке …/20-libz3-dev_4.13.3-1_amd64.deb …
Распаковывается libz3-dev:amd64 (4.13.3-1) …
Выбор ранее не выбранного пакета llvm-19-runtime.
Подготовка к распаковке …/21-llvm-19-runtime_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается llvm-19-runtime (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета llvm-19.
Подготовка к распаковке …/22-llvm-19_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается llvm-19 (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета python3-pygments.
Подготовка к распаковке …/23-python3-pygments_2.18.0+dfsg-2_all.deb …
Распаковывается python3-pygments (2.18.0+dfsg-2) …
Выбор ранее не выбранного пакета python3-yaml.
Подготовка к распаковке …/24-python3-yaml_6.0.2-1+b2_amd64.deb …
Распаковывается python3-yaml (6.0.2-1+b2) …
Выбор ранее не выбранного пакета llvm-19-tools.
Подготовка к распаковке …/25-llvm-19-tools_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается llvm-19-tools (1:19.1.7-3+b1) …
Выбор ранее не выбранного пакета llvm-19-dev.
Подготовка к распаковке …/26-llvm-19-dev_1%3a19.1.7-3+b1_amd64.deb …
Распаковывается llvm-19-dev (1:19.1.7-3+b1) …
Настраивается пакет libz3-dev:amd64 (4.13.3-1) …
Настраивается пакет libc-l10n (2.41-12+deb13u3) …
Настраивается пакет libclang1-19 (1:19.1.7-3+b1) …
Настраивается пакет libclang-common-19-dev:amd64 (1:19.1.7-3+b1) …
Настраивается пакет python3-yaml (6.0.2-1+b2) …
Настраивается пакет libffi-dev:amd64 (3.4.8-2) …
Настраивается пакет locales (2.41-12+deb13u3) …
Generating locales (this might take a while)...
  ru_RU.UTF-8... done
Generation complete.
Настраивается пакет python3-pygments (2.18.0+dfsg-2) …
Настраивается пакет libxml2-dev:amd64 (2.12.7+dfsg+really2.9.14-2.1+deb13u2) …
Настраивается пакет libpfm4:amd64 (4.13.0+git99-gc5587f9-1) …
Настраивается пакет libgc1:amd64 (1:8.2.8-1) …
Настраивается пакет libc6-i386 (2.41-12+deb13u3) …
Настраивается пакет llvm-19-linker-tools (1:19.1.7-3+b1) …
Настраивается пакет llvm-19-runtime (1:19.1.7-3+b1) …
Настраивается пакет libc-dev-bin (2.41-12+deb13u3) …
Настраивается пакет llvm-19-tools (1:19.1.7-3+b1) …
Настраивается пакет libclang-cpp19 (1:19.1.7-3+b1) …
Настраивается пакет libobjc4:amd64 (14.2.0-19) …
Настраивается пакет libobjc-14-dev:amd64 (14.2.0-19) …
Настраивается пакет lib32gcc-s1 (14.2.0-19) …
Настраивается пакет lib32stdc++6 (14.2.0-19) …
Настраивается пакет libclang-rt-19-dev:amd64 (1:19.1.7-3+b1) …
Настраивается пакет libc6-dev:amd64 (2.41-12+deb13u3) …
Настраивается пакет llvm-19 (1:19.1.7-3+b1) …
Настраивается пакет libncurses-dev:amd64 (6.5+20250216-2) …
Настраивается пакет clang-19 (1:19.1.7-3+b1) …
Настраивается пакет clang-tools-19 (1:19.1.7-3+b1) …
Настраивается пакет clang (1:19.0-63) …
Настраивается пакет llvm-19-dev (1:19.1.7-3+b1) …
Обрабатываются триггеры для libc-bin (2.41-12+deb13u3) …
Обрабатываются триггеры для systemd (257.9-1~deb13u1) …
Обрабатываются триггеры для man-db (2.13.1-1) …
Обрабатываются триггеры для base-files (13.8+deb13u2) …
```
## 11. Также было проведено тестирование через clang
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab07$ tools/polly/bin/polly.py --toolchain clang-cxx14 --test
Python version: 3.13
Build dir: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14
Execute command: [
  `which`
  `cmake`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.31.6

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14`
  `-GUnix Makefiles`
  `-DCMAKE_TOOLCHAIN_FILE=/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly/clang-cxx14.cmake`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "-H." "-B/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14" "-GUnix Makefiles" "-DCMAKE_TOOLCHAIN_FILE=/home/vdeo/EighthBroom3439/workspace/projects/lab07/tools/polly/clang-cxx14.cmake"

CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- [polly] Used toolchain: clang / c++14 support
-- The C compiler identification is Clang 19.1.7
-- The CXX compiler identification is Clang 19.1.7
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/clang - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/clang++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Configuring done (51.5s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14
Execute command: [
  `cmake`
  `--build`
  `/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14`
  `--`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07]> "cmake" "--build" "/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14" "--"

[  7%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 14%] Linking CXX static library libprint.a
[ 14%] Built target print
[ 21%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 28%] Linking CXX static library ../../../lib/libgtest.a
[ 28%] Built target gtest
[ 35%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 42%] Linking CXX static library ../../../lib/libgtest_main.a
[ 42%] Built target gtest_main
[ 50%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 57%] Linking CXX executable check
[ 57%] Built target check
[ 64%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[ 71%] Linking CXX executable demo
[ 71%] Built target demo
[ 78%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 85%] Linking CXX static library ../../../lib/libgmock.a
[ 85%] Built target gmock
[ 92%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
Run tests
Execute command: [
  `ctest`
]

[/home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14]> "ctest"

Test project /home/vdeo/EighthBroom3439/workspace/projects/lab07/_builds/clang-cxx14
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
-
Log saved: /home/vdeo/EighthBroom3439/workspace/projects/lab07/_logs/polly/clang-cxx14/log.txt
-
Generate: 0:00:52.568131s
Build: 0:00:14.760845s
Test: 0:00:00.024728s
-
Total: 0:01:07.353971s
-
SUCCESS
```
