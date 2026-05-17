[![CMake CI](https://github.com/EighthBroom3439/lab6/actions/workflows/ci.yml/badge.svg)](https://github.com/EighthBroom3439/lab6/actions/workflows/ci.yml)
# Laboratory work VI

Цель данной лабораторной работы заключалась в том, чтобы ознакомиться с средствами пакетирования на примере CPack.
B данной работе было выполнено следующее:

## 1. Был скопирован репозиторий из предыдущей лабораторной работы
```bash
vdeo@deo:~$ export GITHUB_USERNAME=EighthBroom3439
vdeo@deo:~$ git clone https://github.com/${GITHUB_USERNAME}/lab5 ${GITHUB_USERNAME}/workspace/projects/lab06
Клонирование в «EighthBroom3439/workspace/projects/lab06»...
remote: Enumerating objects: 108, done.
remote: Counting objects: 100% (108/108), done.
remote: Compressing objects: 100% (54/54), done.
remote: Total 108 (delta 37), reused 108 (delta 37), pack-reused 0 (from 0)
Получение объектов: 100% (108/108), 33.19 КиБ | 755.00 КиБ/с, готово.
Определение изменений: 100% (37/37), готово.
vdeo@deo:~$ cd ${GITHUB_USERNAME}/workspace/projects/lab06
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ git remote remove origin
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab6
```

## 2. Были внесены изменения в CMakeLists.txt
```bash
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION_TWEAK 0)
set(PRINT_VERSION
  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
```

## 3. Были созданы файлы DESCRIPTION и ChangeLog.md
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ echo "lab library" > DESCRIPTION
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cat > ChangeLog.md <<'EOF'
> * Sun May 17 2026 EighthBroom3439 <vladimir.deobald@gmail.com> 0.1.0.0
> - Initial RPM release
> EOF
```

## 4. Был создан файл CPackConfig.cmake
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cat > CPackConfig.cmake <<'EOF'
> include(InstallRequiredSystemLibraries)
> set(CPACK_PACKAGE_CONTACT vladimir.deobald@gmail.com)
> set(CPACK_PACKAGE_VERSION_MAJOR ${PRINT_VERSION_MAJOR})
> set(CPACK_PACKAGE_VERSION_MINOR ${PRINT_VERSION_MINOR})
> set(CPACK_PACKAGE_VERSION_PATCH ${PRINT_VERSION_PATCH})
> set(CPACK_PACKAGE_VERSION_TWEAK ${PRINT_VERSION_TWEAK})
> set(CPACK_PACKAGE_VERSION ${PRINT_VERSION})
> set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
> set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
> 
> set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
> set(CPACK_RESOURCE_FILE_README ${CMAKE_CURRENT_SOURCE_DIR}/README.md)
> 
> set(CPACK_RPM_PACKAGE_NAME "print-devel")
> set(CPACK_RPM_PACKAGE_LICENSE "MIT")
> set(CPACK_RPM_PACKAGE_GROUP "print")
> set(CPACK_RPM_CHANGELOG_FILE ${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
> set(CPACK_RPM_PACKAGE_RELEASE 1)
> 
> set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
> set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
> set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
> 
> include(CPack)
> EOF
```

## 5. Все изменения были закоммичены
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ git add .
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ git commit -m "added versions and CPack configuration"
[main 261b493] added versions and CPack configuration
 4 files changed, 37 insertions(+)
 create mode 100644 CPackConfig.cmake
 create mode 100644 ChangeLog.md
 create mode 100644 DESCRIPTION
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ git tag v0.1.0.0
```

## 6. Была произведена сборка CMake двумя способами
Первый способ:
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cmake -H. -B_build
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab06/_build
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cmake --build _build
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cd _build
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06/_build$ cpack -G "TGZ"
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /home/vdeo/EighthBroom3439/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
```

Второй способ:
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vdeo/EighthBroom3439/workspace/projects/lab06/_build
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ cmake --build _build --target package
[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /home/vdeo/EighthBroom3439/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
```
Оба способа сгенерировали архив print-0.1.0.0-Linux.tar.gz

## 7. Данный архив был перемещен в папку artifacts
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ mkdir -p artifacts
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ mv _build/*.tar.gz artifacts/
vdeo@deo:~/EighthBroom3439/workspace/projects/lab06$ tree artifacts
artifacts
└── print-0.1.0.0-Linux.tar.gz

1 directory, 1 file
```
