
# Requirements

Задача: с помощью одного docker-compose файла поднять стек состоящий из трех сервисов:

1. PostgreSQL - база данных. Должна быть возможность подключения к этой базе из хост системы. Прописать именованный volume для хранения данных. Добавить user, password, database в docker-compose файл.
2. Написать Dockerfile для [node.js приложения](https://nodejs.org/en/docs/guides/getting-started-guide/). Требования:
    - Необходимо копировать все файлы из текущего каталога в собираемый докер образ, за исключением папки node_modules и её содержимого
    - Для установки всех зависимостей необходимо выполнить команду `npm install`
    - Приложение должно стартовать при старте контейнера. Команда для старта приложения `npm start`
3. Третий сервис - Nginx. Nodejs приложение слушает на 3000 порту. Третим сервисом необходимо поднять контейнер с nginx, который будет проксировать соединения с 80 порта в хост системе на 3000 порт приложения NodeJS

# How to run

Let's start from building a node.js app:

```docker build . -t node-web-app --no-cache```

Then, we'll move to ```docker-compose``` command as follows:

```docker-compose up --build```

That should be sufficient to start playing with this project. A proof of concept:

```
Dmytros-MacBook-Pro:docker-test dmytroh$ docker-compose up --build
nginx-proxy is up-to-date
postgresql is up-to-date
nodejs_app is up-to-date
Attaching to nginx-proxy, postgresql, nodejs_app
nodejs_app     | 
nodejs_app     | > docker_web_app@1.0.0 start
nodejs_app     | > node app.js
nodejs_app     | 
nodejs_app     | Running on http://0.0.0.0:3000
postgresql     | 
postgresql     | PostgreSQL Database directory appears to contain a database; Skipping initialization
postgresql     | 
postgresql     | 2021-11-10 22:42:59.233 UTC [1] LOG:  starting PostgreSQL 14.0 (Debian 14.0-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
postgresql     | 2021-11-10 22:42:59.235 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
postgresql     | 2021-11-10 22:42:59.245 UTC [1] LOG:  listening on IPv6 address "::", port 5432
postgresql     | 2021-11-10 22:42:59.251 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
postgresql     | 2021-11-10 22:42:59.268 UTC [27] LOG:  database system was interrupted; last known up at 2021-11-10 22:41:05 UTC
postgresql     | 2021-11-10 22:42:59.643 UTC [27] LOG:  database system was not properly shut down; automatic recovery in progress
postgresql     | 2021-11-10 22:42:59.650 UTC [27] LOG:  invalid record length at 0/16FBC60: wanted 24, got 0
postgresql     | 2021-11-10 22:42:59.651 UTC [27] LOG:  redo is not required
postgresql     | 2021-11-10 22:42:59.700 UTC [1] LOG:  database system is ready to accept connections
nginx-proxy    | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
nginx-proxy    | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
nginx-proxy    | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
nginx-proxy    | 10-listen-on-ipv6-by-default.sh: info: can not modify /etc/nginx/conf.d/default.conf (read-only file system?)
nginx-proxy    | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
nginx-proxy    | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
nginx-proxy    | /docker-entrypoint.sh: Configuration complete; ready for start up
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: using the "epoll" event method
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: nginx/1.21.4
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: OS: Linux 5.10.47-linuxkit
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: start worker processes
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: start worker process 25
nginx-proxy    | 2021/11/10 22:42:59 [notice] 1#1: start worker process 26
nginx-proxy    | 172.23.0.1 - - [10/Nov/2021:22:43:13 +0000] "GET / HTTP/1.1" 200 11 "-" "curl/7.77.0" "-"
```

Now you can query both localhost:80 and localhost:3000 addresses, and the result should be the same:

```
Dmytros-MacBook-Pro:~ dmytroh$ curl http://localhost:80
Hello WorldDmytros-MacBook-Pro:~ dmytroh$ 
...
nginx-proxy    | 172.23.0.1 - - [10/Nov/2021:22:56:03 +0000] "GET / HTTP/1.1" 200 11 "-" "curl/7.77.0" "-"
...
```
```
Dmytros-MacBook-Pro:junior-devops-task dmytroh$ curl http://localhost:3000
Hello WorldDmytros-MacBook-Pro:~ dmytroh$
```

That's it. Thank you for your attention.
