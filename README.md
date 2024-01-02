# docker REBRAIN

## DKR 01: Basics. Знакомство с Docker

> Устанавливаем докер согласно оффициальной документации.
> Дальше по задании:

1. Запускаем контейнер из официального образа nginx

```
sudo docker run -d -p 127.0.0.1:28080:80 --name rbm-dkr-01 nginx:stable
```

2. Проверям успешно ли запустился контейнер

```
sudo curl http://127.0.0.1:28080
```

3. Останавливаем контейнер

```
sudo docker stop rbm-dkr-01
```

## DKR 02: Basics. Флаги запуска

1. Скачиваем конфигурационный файл: https://gitlab.rebrainme.com/docker-course-students/dkr-nginx-conf-1/blob/master/nginx.conf

2. Создаем и запускаем докер образ

```
docker run -d -p 127.0.0.128889:80 -v /home/user/nginx.conf:/etc/nginx/nginx/conf --name rbm-dkr-02 nginx:stable
```

3. Подсчитываем хэш файла

```
docker exec -ti rbm-dkr-02 md5sum /etc/nginx/nginx.conf
```

4. Отправляем на проверку не останавливая контейнер.
