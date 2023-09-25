# Задача: Построить балансировщик нагрузки веб-приложения на базе Nginx
На сайте-песочнице https://labs.play-with-docker.com/ несколько нод:

![screenshot](/screenshots/node-nginx.jpg)

Создадим папку nginx, которую подключим в качестве volume к контейнеру по пути /etc/nginx
```
docker run --name nginx -d nginx
docker cp $(docker ps --filter "ancestor=nginx" --format "{{.Names}}"):/etc/nginx . && docker cp $(docker ps --filter "ancestor=nginx" --format "{{.Names}}"):/usr/share/nginx/html .
docker stop $(docker ps -aqf "name=nginx") && docker rm $(docker ps -aqf "name=nginx")
```
Далее запустим docker с nginx, пробросим порт 80:80 и volume nginx
```
docker run -d -p 80:80 -v $(pwd)/nginx:/etc/nginx/ -v $(pwd)/html:/usr/share/nginx/html nginx
```
Для перезапуска конфигурации необходимо использовать:
```
docker exec $(docker ps --filter "ancestor=nginx" --format "{{.Names}}") /etc/init.d/nginx reload
```

Аналогично настраиваем на остальных нодах.
Ноды условно поделим на мастер ноду (та, которая проксирует) и на воркеры (те, на которые проксируют)

Отредактируем index.html - страницу на воркер нодах по пути ~/html/index.html:
```
<!DOCTYPE html>
<html>
<head>
    <title>It Works!</title>
    <style>
        body {
            background-color: green;
        }
        h1 {
            font-weight: bold;
        }
    </style>
</head>
<body>
    <h1>It works!</h1>
</body>
</html>
```

Необходимо вписать таким образом, чтобы nginx отдавал данную страницу на воркер нодах с изменением параметра на любой другой цвет:
```
body {
            background-color: green;
        }
```

Должно получиться вот так, 1-ая воркер нода по 80 порту:

![screenshot](/screenshots/work_node_1.png)

2-ая воркер нода по 80 порту:

![screenshot](/screenshots/work_node_2.png)

На местер ноде  делаем настройку nginx на балансировку нагрузки:
```
   upstream backend {
       server 192.168.0.26:80 weight=1;
       server 192.168.0.27:80 weight=2;
   }

   server {
       listen 80;

       location / {
           proxy_pass http://backend;
       }
   }
```
При подключении к мастер-ноде по 80 порту должны получить проксирование на страницы разных цветов.

# Заключение

Выяснили как настроить балансировку нагрузки http 

#  Полезные ссылки:
```
https://labs.play-with-docker.com/ - песочница для создания виртуальных машин
```
