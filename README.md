[![CMake CI](https://github.com/EighthBroom3439/lab04/actions/workflows/ci.yml/badge.svg)](https://github.com/EighthBroom3439/lab04/actions/workflows/ci.yml)
# Laboratory work IV

Цель данной лабораторной работы заключалась в том, чтобы ознакомиться с системой непрерывной интеграции на примере сервиса Travic CI, однако ввиду объективных причин был использован GitHub actions.
B данной работе было выполнено следующее:

## 1. Был скопирован репозиторий из предыдущей лабораторной работы
```bash
vdeo@deo:~$ export GITHUB_USERNAME=EighthBroom3439
vdeo@deo:~$ cd ${GITHUB_USERNAME}/workspace/
vdeo@deo:~/EighthBroom3439/workspace$ pushd .
~/EighthBroom3439/workspace ~/EighthBroom3439/workspace
vdeo@deo:~/EighthBroom3439/workspace$ source scripts/activate
vdeo@deo:~/EighthBroom3439/workspace$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
Клонирование в «projects/lab04»...
remote: Enumerating objects: 73, done.
remote: Counting objects: 100% (73/73), done.
remote: Compressing objects: 100% (41/41), done.
remote: Total 73 (delta 24), reused 73 (delta 24), pack-reused 0 (from 0)
Получение объектов: 100% (73/73), 23.10 КиБ | 5.78 МиБ/с, готово.
Определение изменений: 100% (24/24), готово.
vdeo@deo:~/EighthBroom3439/workspace$ cd projects/lab04
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git remote remove origin
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04.git
```

## 2. Был создан конфигурационный файл ci.yml
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ mkdir -p .github/workflows
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ cat > .github/workflows/ci.yml <<EOF
> name: CMake CI
> 
> on:
>   push:
>     branches: [ master, main ]
>   pull_request:
>     branches: [ master, main ]
> 
> jobs:
>   build:
>     runs-on: ubuntu-latest
> 
>     steps:
>     - name: Checkout code
>       uses: actions/checkout@v4
> 
>     - name: Install CMake
>       run: |
>         sudo apt-get update
>         sudo apt-get install -y cmake cmake-data
> 
>     - name: Configure CMake
>       run: cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
> 
>     - name: Build
>       run: cmake --build _build
> 
>     - name: Install
>       run: cmake --build _build --target install
> EOF
```
