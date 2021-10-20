### *PROJECT ID: postgres2021-220685*
### *VM: instance-1 (35.204.207.80)*
#### *Homework 3*
---

0. Новую VM не создавал, заиспользовал старую, на которой уже был 13й постгрес. Соответственно, т.к. мы будем поднимать контейнер и пробрасывать в нём порт 5432 наружу - то возникнет конфликт. Поэтому в конфиге текущего простгреса меняю порт 5432 --> 5433 и рестартую кластер. Теперь к контейнерам.

1. Создал директорию куда будет монтироваться постгрес-дата из будущего контейнера (mkdir  /var/lib/postgres).

2. Заранал контейнер сервер с сетью и пробросом порта наружу:

>sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:13
3. Заранал контенер клиент в той же сети pg-net в котором стазу стартует psql с подключением к pg-server контейнеру:
> sudo docker run -it --rm --network pg-net --name pg-client postgres:13 psql -h pg-server -U postgres

4. Т.е. сейчас я подключен к pg-server по psql. Создаю базу данных doc_server_db, в ней табличку artist, в ней инсёрчу несколько кортэжей.

5. Выхожу из контейнера, стопаю и удалаю первый контейнер.

6. Ранаю новый контейнер-сервер (как в пункте 2).

7. Подключаюсь к нему через psql через новый контейнер-клиент (как в пункте 3).

8. Смотрю список баз (\l), вижу свою старую базу doc_server_db, делаю "select * from artisat" данные на месте.

PS: если дропнуть вcе контейнеры - директория (/var/lib/postgres) из пункта 1 со своими файлами никуда не девается.