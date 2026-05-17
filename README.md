[![CMake CI](https://github.com/EighthBroom3439/lab5/actions/workflows/ci.yml/badge.svg)](https://github.com/EighthBroom3439/lab5/actions/workflows/ci.yml)
# Laboratory work V

Цель данной лабораторной работы заключалась в том, чтобы ознакомиться с фреймворками для тестирования на примере GTest.
B данной работе было выполнено следующее:

## 1. Был скопирован репозиторий из предыдущей лабораторной работы
```bash
vdeo@deo:~$ export GITHUB_USERNAME=EighthBroom3439
vdeo@deo:~$ cd ${GITHUB_USERNAME}/workspace/
vdeo@deo:~/EighthBroom3439/workspace$ git clone https://github.com/${GITHUB_USERNAME}/lab04 projects/lab05
Клонирование в «projects/lab05»...
remote: Enumerating objects: 94, done.
remote: Counting objects: 100% (94/94), done.
remote: Compressing objects: 100% (54/54), done.
remote: Total 94 (delta 33), reused 84 (delta 28), pack-reused 0 (from 0)
Получение объектов: 100% (94/94), 28.99 КиБ | 4.83 МиБ/с, готово.
Определение изменений: 100% (33/33), готово.
vdeo@deo:~/EighthBroom3439/workspace$ cd projects/lab05
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ git remote remove origin
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ git remote add origin https://git
```

## 2. Была установлена последняя доступная версия gtest
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ git submodule add https://github.com/google/googletest third-party/gtest
Клонирование в «/home/vdeo/EighthBroom3439/workspace/projects/lab05/third-party/gtest»...
remote: Enumerating objects: 28637, done.
remote: Counting objects: 100% (71/71), done.
remote: Compressing objects: 100% (51/51), done.
remote: Total 28637 (delta 36), reused 21 (delta 19), pack-reused 28566 (from 2)
Получение объектов: 100% (28637/28637), 13.75 МиБ | 6.59 МиБ/с, готово.
Определение изменений: 100% (21277/21277), готово.
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05/third-party/gtest$ git checkout v1.15.2
Примечание: переключение на «v1.15.2».

Вы сейчас в состоянии «отсоединённого указателя HEAD». Можете осмотреться,
внести экспериментальные изменения и зафиксировать их, также можете
отменить любые коммиты, созданные в этом состоянии, не затрагивая другие
ветки, переключившись обратно на любую ветку.

Если хотите создать новую ветку для сохранения созданных коммитов, можете
сделать это (сейчас или позже), используя команду switch с параметром -c.
Например:

  git switch -c <новая-ветка>

Или отмените эту операцию с помощью:

  git switch -

Отключите этот совет, установив переменную конфигурации
advice.detachedHead в значение false

HEAD сейчас на b514bdc8 Update version strings to 1.15.2 (#4583)
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05/third-party/gtest$ cd ../..
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ git add third-party/gtest
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ git commit -m "added gtest v1.15.2"
[main 2d03fe7] added gtest v1.15.2
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

## 3. Был добавлен кусок кода к CMake.txt
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ cat >> CMakeLists.txt <<'EOF'
> if(BUILD_TESTS)
>   enable_testing()
>   add_subdirectory(third-party/gtest)
>   file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
>   add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
>   target_link_libraries(check ${PROJECT_NAME} gtest_main)
>   add_test(NAME check COMMAND check)
> endif()
> EOF
```

## 4. Был написан тест
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ mkdir -p tests
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ cat > tests/test1.cpp <<'EOF'
> #include <print.hpp>
> #include <gtest/gtest.h>
> #include <fstream>
> #include <string>
> 
> TEST(Print, InFileStream)
> {
>   std::string filepath = "file.txt";
>   std::string text = "hello";
>   std::ofstream out{filepath};
>   print(text, out);
>   out.close();
>   std::string result;
>   std::ifstream in{filepath};
>   in >> result;
>   EXPECT_EQ(result, text);
> }
> EOF
```

## 5. Была произведена сборка с последующим запуском теста
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ cmake -H. -B_build -DBUILD_TESTS=ON
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
-- Configuring done (3.6s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab05/_build
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ cmake --build _build
[  8%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 16%] Linking CXX static library libprint.a
[ 16%] Built target print
[ 25%] Building CXX object third-party/gtest/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 33%] Linking CXX static library ../../../lib/libgtest.a
[ 33%] Built target gtest
[ 41%] Building CXX object third-party/gtest/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library ../../../lib/libgtest_main.a
[ 50%] Built target gtest_main
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
[ 75%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 83%] Linking CXX static library ../../../lib/libgmock.a
[ 83%] Built target gmock
[ 91%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ cmake --build _build --target test
Running tests...
Test project /home/vdeo/EighthBroom3439/workspace/projects/lab05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.04 sec
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ 
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ ./_build/check
Running main() from /home/vdeo/EighthBroom3439/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from Print
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (0 ms)
[----------] 1 test from Print (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.
```

## 6. К ci.yml был добавлен кусок кода
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab05$ cat >> .github/workflows/ci.yml <<'EOF'
> 
>     -name: Run tests
>      run: cmake --build _build --target test -- ARGS=--verbose
> EOF

```
