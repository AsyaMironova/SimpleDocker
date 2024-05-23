## Part 1. Готовый докер

##### Возьми официальный докер-образ с **nginx** и выкачай его при помощи `docker pull`.
![docker pull](png/ping1.1.png)

##### Проверь наличие докер-образа через `docker images`.
Проверяем наличие докер образа
![docker images](png/ping1.2.png)

##### Запусти докер-образ через `docker run -d [image_id|repository]`.
Запускаем докер командой docker run -d nginx
![docker run -d nginx](png/ping1.3.png)

##### Проверь, что образ запустился через `docker ps`.
![docker ps](png/ping1.4.png)

##### Посмотри информацию о контейнере через `docker inspect [container_id|container_name]`.
Иформация контейнера
![docker inspect 2030710e](png/ping1.5.png)

##### По выводу команды определи и помести в отчёт размер контейнера, список замапленных портов и ip контейнера.
Размер контейнера
![ShmSize](png/ping1.6.png)
Список замапленных портов
![Ports](png/ping1.7.png)
IP контейнера
![IP](png/ping1.8.png)

##### Останови докер образ через `docker stop [container_id|container_name]`.
Остановка докер-образа
![docker stop](png/ping1.9.png)

##### Проверь, что образ остановился через `docker ps`.
![docker ps](png/ping1.10.png)

Тк контейнеры по-умолчанию запускаются с портом 80, то нужно изменить в его конфиге, на любой другой порт (я изменила на порт 90). После чего запускаем докер-образ.
![docker run -d -p 80:80 -p 443:443 nginx](png/ping1.11.png)

##### Проверь, что в браузере по адресу *localhost:80* доступна стартовая страница **nginx**.
Для начала проверим, что докер-образ запустился командой docker ps. Все ок, у нас он запущен. Далее проверим стартовую страницу nginx
![docker ps](png/ping1.12.png)
![страница localhost:80](png/ping1.13.png)

##### Перезапусти докер контейнер через `docker restart [container_id|container_name]`.
![docker restart](png/ping1.14.png)

##### Проверь любым способом, что контейнер запустился.
![проверка](png/ping1.15.png)


## Part 2. Операции с контейнером

##### Прочитай конфигурационный файл *nginx.conf* внутри докер контейнера через команду *exec*.
![docker exec 52c cat /etc/nginx/nginx.conf](png/ping2.1.png)

##### Создай на локальной машине файл *nginx.conf*.
Создаем файл nginx.conf и прописываем в нем порт 80, также location = /status {stub_status on}; для того, чтобы появилась страница по адресу localhost/status с отображением статуса
##### Настрой в нем по пути */status* отдачу страницы статуса сервера **nginx**.

![location = /status](png/ping2.2.png)

##### Скопируй созданный файл *nginx.conf* внутрь докер-образа через команду `docker cp`.
![docker cp nginx.conf fossowan:/etc/nginx/nginx.conf](png/ping2.3.png)

##### Перезапусти **nginx** внутри докер-образа через команду *exec*.
![docker exec fossowan nginx -s reload](png/ping2.4.png)

##### Проверь, что по адресу *localhost:80/status* отдается страничка со статусом сервера **nginx**.
![localhost:80/status](png/ping2.5.png)

##### Экспортируй контейнер в файл *container.tar* через команду *export*.
![container.tar](png/ping2.6.png)

##### Останови контейнер.
![Останови контейнер](png/ping2.7.png)

##### Удали образ через `docker rmi [image_id|repository]`, не удаляя перед этим контейнеры. И удали остановленный контейнер.
![docker rmi -f nginx](png/ping2.8.png)

##### Импортируй контейнер обратно через команду *import*.
 ![docker import container.tar nginx_restored](png/ping2.9.png)

##### Запусти импортированный контейнер.
![docker run -d -p 80:80 -p 443:443 nginx_restored](png/ping2.10.png)

##### Проверь, что по адресу *localhost:80/status* отдается страничка со статусом сервера **nginx**.
![localhost:80/status](png/ping2.11.png)


## Part 3. Мини веб-сервер

##### Напиши мини-сервер на **C** и **FastCgi**, который будет возвращать простейшую страничку с надписью `Hello World!`.
![Мини-сервер](png/ping3.1.png)

##### Напиши свой *nginx.conf*, который будет проксировать все запросы с 81 порта на *127.0.0.1:8080*.
![spawn-fcgi](png/ping3.2.png)
![подготовка](png/ping3.3.png)

##### Запусти написанный мини-сервер через *spawn-fcgi* на порту 8080.
![spawn-fcgi](png/ping3.4.png)

##### Проверь, что в браузере по *localhost:81* отдается написанная тобой страничка.
![localhost:81](png/ping3.5.png)

##### Положи файл *nginx.conf* по пути *./nginx/nginx.conf* (это понадобится позже).

## Part 4. Свой докер

##### Напишем свой docker-образ, который собирает исходники 3-й части, запускает на порту 80, после копирует внутрь написанный нами nginx.conf и, наконец, запускает nginx (ниже приведены файлы run.sh и Dockerfile, файлы nginx.conf и server.c остаются с 3-й части)
![Dockerfile](png/ping4.1.png)
![run.sh](png/ping4.2.png)

#####  Соберем написанный docker-образ через команду docker build, при этом указав имя и тэг нашего контейнера
![sudo docker build -t nifty_swanson:1.0 .](png/ping4.3.png)

##### Проверь через `docker images`, что все собралось корректно.
![docker images](png/ping4.4.png)

##### Запусти собранный докер-образ с маппингом 81 порта на 80 на локальной машине и маппингом папки *./nginx* внутрь контейнера по адресу, где лежат конфигурационные файлы **nginx**'а
![маппингом 81 порта на 80](png/ping4.5.png)

##### Проверь, что по localhost:80 доступна страничка написанного мини сервера.
![localhost:80](png/ping4.6.png)

##### Допиши в *./nginx/nginx.conf* проксирование странички */status*, по которой надо отдавать статус сервера **nginx**.
![допиши](png/ping4.7.png)

##### Перезапусти докер-образ.
![перезапуск](png/ping4.8.png)
##### Проверь, что теперь по *localhost:80/status* отдается страничка со статусом **nginx**
![](png/ping4.9.png)

## Part 5. **Dockle**

##### Просканируй образ из предыдущего задания через `dockle [image_id|repository]`.
![dockle [image_id|repository]](png/ping5.1.png)

##### Исправь образ так, чтобы при проверке через **dockle** не было ошибок и предупреждений.
![dockle](png/ping5.2.png)

## Part 6. Базовый **Docker Compose**


##### Напиши файл *docker-compose.yml*, с помощью которого:
![docker-compose.yml](png/ping6.1.png)

##### Замапь 8080 порт второго контейнера на 80 порт локальной машины.

##### Собери и запусти проект с помощью команд `docker-compose build` и `docker-compose up`.
![docker-compose build](png/ping6.2.png)
![docker-compose up](png/ping6.3.png)

##### Проверь, что в браузере по *localhost:80* отдается написанная тобой страничка, как и ранее.
![localhost:80](png/ping6.4.png)
![Статус](png/ping6.5.png)