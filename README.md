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

## 3. Содержимое папки lab04 было запушено в этот репозиторий (Также был исправлен файл print.hpp, чтобы компиляция на сайте происходила без ошибок)
```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git add .
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git commit
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git push origin master:main
Username for 'https://github.com': EighthBroom3439
Password for 'https://EighthBroom3439@github.com': 
        
Перечисление объектов: 4, готово.
Подсчет объектов: 100% (4/4), готово.
При сжатии изменений используется до 2 потоков
Сжатие объектов: 100% (2/2), готово.
Запись объектов: 100% (2/2), 323 байта | 323.00 КиБ/с, готово.
Total 2 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/EighthBroom3439/lab04.git
   7b140c3..a6e0271  master -> main
```

```bash
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04/include$ nano print.hpp 
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git add include/print.hpp
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git commit -m "fix print.hpp"
[master 04b95fa] fix print.hpp
 1 file changed, 1 insertion(+), 1 deletion(-)
vdeo@deo:~/EighthBroom3439/workspace/projects/lab04$ git push origin master:main
Username for 'https://github.com': EighthBroom3439
Password for 'https://EighthBroom3439@github.com': 
Перечисление объектов: 7, готово.
Подсчет объектов: 100% (7/7), готово.
При сжатии изменений используется до 2 потоков
Сжатие объектов: 100% (3/3), готово.
Запись объектов: 100% (4/4), 342 байта | 342.00 КиБ/с, готово.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/EighthBroom3439/lab04.git
   a6e0271..04b95fa  master -> main
```

## 4. В начало отчета был добавлен бейдж
