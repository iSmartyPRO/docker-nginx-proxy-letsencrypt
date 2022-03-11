# Краткое описание
Это Docker веб прокси сервер на базе Nginx в связке c LetsEncrypt

# Разворачивание (разовая операция)

```
docker-compose up -d
```
появиться два контенера: nginx и certbot


# Генерация сертификата LetsEncrypt

Копируем шаблон первого шага:
```
cp ./letsencrypt/templates/1step.conf ./server/conf.d/1step.conf
vim ./server/conf.d/1step.conf
```
редактиурем файл, используя свое доменное имя.

Перезагружаем веб сервер:
```
docker exec nginx nginx -s reload
```

Запускаем генерацию сертификата:
```
docker-compose run --rm certbot certonly --webroot --webroot-path /var/www/certbot/ -d your.domain.name
```
используем свой имя домена

после генерации будет известен путь до сертификатов, их и надо использовать в новом конфигурационном файле.

удаялем старый конфиг и используем новый:

```
rm -rf ./server/conf.d/1step.conf
cp ./letsencrypt/templates/2step.conf ./server/conf.d/your.domain.name.conf
vim ./server/conf.d/your.domain.name.conf
```
редактируем файл используя свои значения.

так же в шаблоне имеется еще один пример прокси конфига: ./letsencrypt/templates/proxy-sample.conf

После редактирования перезагружаем веб сервер:
```
docker exec nginx nginx -s reload
```

Перед перезагрузкой веб сервера, можно проверить правильность настроек при помощи команды
```
docker exec nginx nginx -t
```


Обновление сертификата
```
docker-compose run --rm certbot renew
```