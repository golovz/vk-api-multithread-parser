# vk-api-multithread-parser
Multithread parser of vk.com(Russian social network) using api.vk.com

## О vk-multithread-parser
Программа парсит все user_id каждого пользователя кто поставил лайки в заданной группе (используя api.vk.com) и подсчитывает сколько лайков сделал каждый пользователь в группе.Результаты записываются в SQLite3 базу данных в следующем виде: 
для каждой группы создаётся своя таблица с названием "group_(...id группы...)" со столбцами содержащими id пользователя (user_id) и колличеством поставленных лайков в группе (likes_count)
в порядке убывания.

Основной проблемой множественных запросов к API Вконтакте является время их обработки сервером (например ,получение информации о лайках для групп с огромным числом подписчиков в синхронном режимe при стабильном интернет подключении может занять больше часа)
Для решения данной проблемы в программе был разработан небольшой task-based пул потоков позволяющий отправлять запросы асинхронно, при этом составление запросов   и обработка ответов работают в синхронном режиме

В качаестве https клиетна был использован ранее мной написанный https://github.com/danglvz/https_client ,позволяющий обходить ограничение в 100 запросов (после 100-ого запроса API ВК будет выдавать пустой результат)

## Зависимости
- **OpenSSL v1.1 or higher**
- **SQLite3**
## Сборка и запуск
```
git clone https://github.com/danglvz/vk-api-multithread-parser.git --recursive
cd vk-api-multithread-parser
mkdir build
cmake ..
make vk_parser_main
cd src
./vk_parser_main <args>
```
## Параметры запуска
Для запуска необходимо, как минимум, задать access_token https://vk.com/dev/access_token и id группы
```
--group_id -g       [id группы без "-"]
--access_token -a   [access token]
--file -f           [файл с группами (каждый id записан на новой строке)]
--coun_of_posts -c  [количество постов в группе с которых будут собираться лайки, по-умолчанию 10000 постов]
--output_db -o      [SQLite3 база данных для записи, по-умолчанию "output.db"]
```
