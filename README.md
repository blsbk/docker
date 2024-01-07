# docker REBRAIN

## DKR 01: Basics. Знакомство с Docker

> Устанавливаем докер согласно оффициальной документации.
> Дальше по задании:

1. Запускаем контейнер из официального образа nginx

```
user@server:~$ sudo docker run -d -p 127.0.0.1:28080:80 --name rbm-dkr-01 nginx:stable
```

2. Проверям успешно ли запустился контейнер

```
user@server:~$ sudo curl http://127.0.0.1:28080
```

3. Останавливаем контейнер

```
user@server:~$ sudo docker stop rbm-dkr-01
```

## DKR 02: Basics. Флаги запуска

1. Скачиваем [конфигурационный файл](https://gitlab.rebrainme.com/docker-course-students/dkr-nginx-conf-1/blob/master/nginx.conf)

2. Создаем и запускаем докер контейнер

```
user@server:~$ docker run -d -p 127.0.0.128889:80 -v /home/user/nginx.conf:/etc/nginx/nginx/conf --name rbm-dkr-02 nginx:stable
```

3. Подсчитываем хэш файла

```
user@server:~$ docker exec -ti rbm-dkr-02 md5sum /etc/nginx/nginx.conf
```

4. Отправляем на проверку не останавливая контейнер.

##  DKR 03: Basics. Запуск команд внутри контейнеров 

1. Настраиваем конфигурационный файл и копируем содержание в окружение
```
user@server:~$ nano nginx.conf
```

2. Создаем и запускаем докер контейнер
```
user@server:~$ docker run -d -p 127.0.0.1:8889:80 --name rbm-dkr-03 -v /home/user/nginx.conf:/etc/nginx/nginx.conf nginx:stable
```

3. Делаем curl запрос на порт
```
user@server:~$ curl 127.0.0.1:8889
Welcome to the training program RebrainMe: Docker!
``` 

4. Подсчитываем хэш файла
```bash
user@server:~$ docker exec -ti rbm-dkr-03 md5sum /etc/nginx/nginx.conf
```

5. Настраиваем новый конфигурационный файл
```bash
user@server:~$ docker exec -it rbm-dkr-03 bash
root@980ae194a434:/ apt update
root@980ae194a434:/ apt-get install sudo
root@980ae194a434:/ sudo apt-get install nano
root@980ae194a434:/ nano /etc/nginx/nginx.conf
```
> Удаляем старое содержимое и вставляем содержимое нового файла и закрываем

6. Повторное чтение конфига приложения без рестарта
```bash
user@server:~$ docker exec rbm-dkr-03 nginx -s reload
```

7. Подсчитываем хэш заново и он должен отличаться от предыдущего
```bash
user@server:~$ docker exec -ti rbm-dkr-03 md5sum /etc/nginx/nginx.conf
87283d198ce47de77dfc7f9de0653485  /etc/nginx/nginx.conf
```

8. Заново отправляем curl запрос
```
user@server:~$ curl 127.0.0.1:8890
Welcome to the training program RebrainMe: Docker! Again!
```

9. Отправляем на проверку не останавливая контейнер.

## DKR 04: Basics. Внешнее хранилище

1. Настраиваем конфигурационный файл и копируем содержание в окружение
```bash
user@server:~$ nano nginx.conf
```

2. Создаем внешнее хранилище
```bash
user@server:~$ docker volume create rbm-dkr-04-volume
user@server:~$ docker volume ls
DRIVER    VOLUME NAME
local     rbm-dkr-04-volume
```

3. Создаем и запускаем докер контейнер c пробросом конфиг файла и монтиованием директория
```bash
user@server:~$ docker run -d -p 127.0.0.1:8891:80 --name rbm-dkr-04 -v /home/user/nginx.conf:/etc/nginx/nginx.conf --mount source=rbm-dkr-04-volume,target=/var/log/nginx/external nginx:stable
```

4. Делаем curl запрос на порт
```bash
user@server:~$ curl 127.0.0.1:8889
Welcome to the training program RebrainMe: Docker!
``` 

5. Выводим содержимое volume на хостовой системе
```bash
user@server:~$ docker volume inspect rbm-dkr-04-volume 
[
    {
        "CreatedAt": "2024-01-02T19:29:29Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/rbm-dkr-04-volume/_data",
        "Name": "rbm-dkr-04-volume",
        "Options": {},
        "Scope": "local"
    }
]

```

6. Отправляем на проверку не останавливая контейнер.

##  DKR 05: Basics. Остановка, удаление контейнеров 

1. Создаем и запускаем 2 контейнера c одинаковыми параметрами
```bash 
user@server:~$ docker run -d --name rbm-dkr-05-run-$(cat /dev/urandom | tr -cd 'a-f0-9' | head -c 10) nginx:stable
```

2. Создаем и запускаем еще один контейнер
```bash 
user@server:~$ docker run -d --name rbm-dkr-05-stop nginx:stable
```

3. Выводим логи всех запущенных контейнеров в файл
```bash
user@server:~$ docker ps | tee /home/user/ps.txt
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
2f6743d46e3a   nginx:stable   "/docker-entrypoint.…"   23 seconds ago   Up 22 seconds   80/tcp    rbm-dkr-05-stop
d5a592ff3297   nginx:stable   "/docker-entrypoint.…"   51 seconds ago   Up 50 seconds   80/tcp    rbm-dkr-05-run-2a0fbf71e1
d30a9f2eb009   nginx:stable   "/docker-entrypoint.…"   54 seconds ago   Up 53 seconds   80/tcp    rbm-dkr-05-run-51e2635da8
```

4. Остановливаем контейнер rbm-dkr-05-stop
```bash
user@server:~$ docker stop rbm-dkr-05-stop
rbm-dkr-05-stop
```
5. Выводим список всех контейнеров
```bash
user@server:~$ docker ps -q
2f6743d46e3a
d5a592ff3297
d30a9f2eb009

user@server:~$ docker ps -aq
d5a592ff3297
d30a9f2eb009
```
6. Одной командой остановим все запущенные контейнеры
```bash
user@server:~$ docker stop $(docker ps -aq)
2f6743d46e3a
d5a592ff3297
d30a9f2eb009
```
7. Выводим список всех контейнеров
```bash
user@server:~$ docker ps -q
2f6743d46e3a
d5a592ff3297
d30a9f2eb009

user@server:~$ docker ps -aq
user@server:~$ 
```
8. Одной командой удаляем все контейнеры
```bash
user@server:~$ docker rm $(docker ps -aq)
2f6743d46e3a
d5a592ff3297
d30a9f2eb009
user@server:~$ docker ps -aq
user@server:~$ 
```
9. Отправляем на проверку

## DKR 06: Basics. Логирование 
1. Запускаем контейнер
```bash
user@server:~$ docker run -d --name rbm-dkr-06-local -p 127.0.0.1:8892:80 --log-driver local --log-opt max-size=10mb nginx:stable
```

2. Выводим список запущенных контейнеров
```bash
user@server:~$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                    NAMES
c71286ec1cc2   nginx:stable   "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds   127.0.0.1:8892->80/tcp   rbm-dkr-06-local
```

3. Обращаемся к запущенному nginx, чтобы были записаны логи
```bash
user@server:~$ curl --silent http://127.0.0.1:8892 > /dev/null
```

4. Выполняем вывод содержимого файла на хостовой системе, в который записаны логи контейнера
```bash
user@server:~$ docker logs --tail 10 rbm-dkr-06-local 

172.17.0.1 - - [02/Jan/2024:20:18:41 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.68.0" "-"
user@server:
```

5. Настраиваем глобальное сохранение логов 
```bash
user@server:~$ sudo nano /etc/docker/daemon.json
```
```json
{
  "log-driver": "local",
  "log-opts": {
    "max-size": "10m"
  }
}
```

6. Запускаем второй контейнер
```bash
user@server:~$ docker run -d -p 127.0.0.1:8893:80 --name rbm-dkr-06-global nginx:stable
```

7. Выводим список запущенных контейнеров
```bash
user@server:~$ docker ps -aq
882f6854474b
c71286ec1cc2
```

8. Обращаемся к запущенному nginx, чтобы были записаны логи
```bash
user@server:~$ curl --silent http://127.0.0.1:8893 > /dev/null
```

9. Выполняем вывод содержимого файла на хостовой системе, в который записаны логи контейнера
```bash
user@server:~$ docker logs --tail 10 rbm-dkr-06-global

172.17.0.1 - - [02/Jan/2024:20:32:37 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.68.0" "-"

```

10. Отправляем на проверку

##  DKR 07: Images. Введение в Docker-образы

1. Выполняем загрузку образа nginx:stable-alpine на свой локальный хост
```bash
user@server:~$ docker pull nginx:stable-alpine
```

2. Добавляем к загруженному образу новый тег rbm-dkr-07
```bash
user@server:~$ docker tag nginx:stable-alpine nginx:rbm-dkr-07
```

3. Выводим список образов на хосте
```bash
user@server:~$ docker images

REPOSITORY   TAG                        IMAGE ID       CREATED        SIZE
nginx        rbm-dkr-07                 0cd127114627   8 months ago   41.1MB
nginx        stable-alpine              0cd127114627   8 months ago   41.1MB
```
    
4. Запускаем контейнер
```bash
user@server:~$ docker run -d nginx:rbm-dkr-07 
d06f07fe9f53f4e0a233c313ccf7c1e8e5423faeea2172732ba9c3d1b96a86c9
```

5. Выводим список запущенных контейнеров
```bash
user@server:~$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED          STATUS          PORTS     NAMES
d06f07fe9f53   nginx:rbm-dkr-07   "/docker-entrypoint.…"   15 seconds ago   Up 13 seconds   80/tcp    fervent_agnesi
user@server:~$ 
```

6. Отправляем на проверку

## DKR 08: Images. Введение в Dockerfile 

1. Скачиваем [конфигурационный файл nginx](https://gitlab.rebrainme.com/docker-course-students/dkr-nginx-conf-1/blob/master/nginx.conf)

2. Пишем Dockerfile
```bash
user@server:~$ nano Dockerfile
```

```Dockerfile
FROM nginx:stable
COPY nginx.conf /etc/nginx/nginx.conf
```

3. Соберираем этот образ с именем nginx и тегом rbm-dkr-08
```bash
user@server:~$ docker build -t nginx:rbm-dkr-08 .
```

4. Выводим список образов на хосте
```bash
user@server:~$ docker images

REPOSITORY   TAG          IMAGE ID       CREATED          SIZE
nginx        rbm-dkr-08   e5adc2e7c7f6   25 seconds ago   142MB
nginx        stable       4c2c5b8bf99f   8 months ago     142MB
```

5. Запускаем контейнер
```bash
user@server:~$ docker run -d -p 127.0.0.1:8900:80 nginx:rbm-dkr-08
e380aa3243edda6bb77471c631a7ddb747ffb9cbea14624a07f8180d699ea20d
```

6. Выводим список запущенных контейнеров
```bash
user@server:~$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS         PORTS                    NAMES
e380aa3243ed   nginx:rbm-dkr-08   "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   127.0.0.1:8900->80/tcp   gallant_merkle
```

7. Проверяем обратившись к 127.0.0.1:8900
```bash
user@server:~$ curl 127.0.0.1:8900
Welcome to the training program RebrainMe: Docker!
```

8. Отправляем на проверку

##  DKR 09: Images. Основные директивы Dockerfile 

1. Пишем Dockerfile следующей конфигурации:
```Dockerfile
FROM ubuntu:18.04
RUN apt-get update && apt-get install -y nginx
COPY nginx.conf /etc/nginx/nginx.conf
ENTRYPOINT ["nginx", "-g", "daemon off;"]
CMD ["-g", "daemon off;"]
WORKDIR /etc/nginx/
VOLUME /var/lib/nginx
```

2. Собираем образ

```bash
user@server:~$ docker build -t nginx:rbm-dkr-09 .
```

3. Выводим список образов на хосте
```bash
user@server:~$ docker images
REPOSITORY   TAG          IMAGE ID       CREATED         SIZE
nginx        rbm-dkr-09   b1f4d99482bf   9 seconds ago   169MB
ubuntu       18.04        f9a80a55f492   7 months ago    63.2MB
```

4. Запускаем контейнер
```bash
user@server:~$ docker run -d -p 127.0.0.1:8901:80 nginx:rbm-dkr-09
```

5. Выводим список запущенных контейнеров - контейнер должен быть запущен.

```bash
user@server:~$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS         PORTS                    NAMES
f7358795d903   nginx:rbm-dkr-09   "nginx -g 'daemon of…"   9 seconds ago   Up 7 seconds   127.0.0.1:8901->80/tcp   elated_roentgen
```

6. Проверьте работу, обратившись к 127.0.0.1:8901

```bash
user@server:~$ curl 127.0.0.1:8901
Welcome to the training program RebrainMe: Docker!
```

7. Отправляем на проверку

## DKR 10: Images. Параметризация Dockerfile

1. Создаем файл /home/user/Dockerfile
```Dockerfile
ARG NG_VERSION
FROM nginx:$NG_VERSION
ENV NG_VERSION=$NG_VERSION
ARG ARG_FILE
RUN touch /opt/$ARG_FILE
```

2. Соберираем образ из данного Dockerfile, передаю в качестве аргумента версию nginx stable и название файла newfile

```bash
user@server:~$ docker build --build-arg NG_VERSION=stable --build-arg ARG_FILE=newfile -t nginx:rbm-dkr-10 /home/user/
```

3. Выводим список образов на хосте
```bash
user@server:~$ docker images
REPOSITORY   TAG          IMAGE ID       CREATED          SIZE
nginx        rbm-dkr-10   83898cc0b615   27 seconds ago   142MB
nginx        stable       4c2c5b8bf99f   9 months ago     142MB
```

4. Запускаем контейнер из этого образа
```bash
user@server:~$ docker run -d --name rbm-dkr-10 --env REBRAINME=DKR10 nginx:rbm-dkr-10
```

5. Выводим список запущенных контейнеров
```bash
user@server:~$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS         PORTS     NAMES
8681c6276240   nginx:rbm-dkr-10   "/docker-entrypoint.…"   7 seconds ago   Up 5 seconds   80/tcp    rbm-dkr-10
```

6. Выводим в контейнере список переменных окружения 
```bash
user@server:~$ docker exec -it rbm-dkr-10 env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=8681c6276240
TERM=xterm
REBRAINME=DKR10
NGINX_VERSION=1.24.0
NJS_VERSION=0.7.12
PKG_RELEASE=1~bullseye
NG_VERSION=
HOME=/root
```

> как видим, переменная окружения NG_VERSION пуста так как ARG не доступна после FROM.

7. Выводим в контейнере список файлов в директории /opt/
```bash
user@server:~$ docker exec -it rbm-dkr-10 ls /opt/
newfile
```

8. Отправляем на проверку

## DKR 11: Images. Введение в понятие «слои» 


