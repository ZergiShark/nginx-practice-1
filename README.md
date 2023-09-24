# Задача: Построить балансировщик нагрузки веб-приложения на базе Nginx

Создадим несколько нод:

![screenshot](/screenshots/node-nginx.jpg)

Создадим папку nginx, которую подключим в качестве volume к контейнеру по пути /etc/nginx
```
docker run --name nginx -d nginx
docker cp $(docker ps --filter "ancestor=nginx" --format "{{.Names}}"):/etc/nginx .
docker stop $(docker ps -aqf "name=nginx") && docker rm $(docker ps -aqf "name=nginx")
```
Далее запустим doker с nginx, пробросим порт 80:80 и volume nginx
```
docker run -d -p 80:80 -v $(pwd)/nginx:/etc/nginx/ nginx
```

#  Полезные ссылки:
```
https://labs.play-with-docker.com/
```
