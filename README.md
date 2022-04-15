Как запустить проект:

Клонировать репозиторий и перейти в него в командной строке:

git clone git@github.com:Ka1las/api_final_yatube.git

cd api_final_yatube

Cоздать и активировать виртуальное окружение:

python3 -m venv env

source env/bin/activate

python3 -m pip install --upgrade pip

Установить зависимости из файла requirements.txt:

pip install -r requirements.txt Выполнить миграции:

python3 manage.py migrate

Запустить проект:

python3 manage.py runserver

Описание проекта

API доступен только аутентифицированным пользователям. В проекте аутентификация осуществляется по токену TokenAuthentication (Реализована аутентификация по JWT-токену).

Когда вы запустите проект, по адресу http://127.0.0.1:8000/redoc/ будет доступна документация для API Yatube. В документации описано, как как работает API. Документация представлена в формате Redoc.

Аутентифицированный пользователь авторизован на изменение и удаление своего контента; в остальных случаях доступ предоставляется только для чтения. При попытке изменить чужие данные должен возвращаться код ответа 403 Forbidden.

Эндпоинты Для взаимодействия с ресурсами:

api/v1/api-token-auth/ (POST): передаём логин и пароль, получаем токен.

api/v1/posts/ (GET, POST): получаем список всех постов или создаём новый пост.

api/v1/posts/{post_id}/ (GET, PUT, PATCH, DELETE): получаем, редактируем или удаляем пост по id.

api/v1/groups/ (GET): получаем список всех групп.

api/v1/groups/{group_id}/ (GET): получаем информацию о группе по id.

api/v1/posts/{post_id}/comments/ (GET, POST): получаем список всех комментариев поста с id=post_id или создаём новый, указав id поста, который хотим прокомментировать. api/v1/posts/{post_id}/comments/{comment_id}/ (GET, PUT, PATCH, DELETE): получаем, редактируем или удаляем комментарий по id у поста с id=post_id.

В ответ на запросы POST, PUT и PATCH ваш API возвращает объект, который был добавлен или изменён.

Работа с моделью Post осуществляется через через ModelViewSet.

В проекте описана модель Follow, в ней два поля — user (кто подписан) и following (на кого подписан). Для этой модели в документации уже описан эндпоинт /follow/ и два метода:

GET — возвращает все подписки пользователя, сделавшего запрос. Возможен поиск по подпискам по параметру search

POST — подписать пользователя, сделавшего запрос на пользователя, переданного в теле запроса. При попытке подписаться на самого себя, пользователь получит информативное сообщение об ошибке. Проверка должна осуществляться на уровне API.

Анонимный пользователь на запросы к этому эндпоинту получает ответ с кодом 401 Unauthorized.

При запросе на изменение или удаление данных осуществляется проверка прав доступа.

Примеры запросов

Пример POST-запроса с токеном Антона Чехова: добавление нового поста. POST .../api/v1/posts/

{ "text": "Вечером собрались в редакции «Русской мысли», чтобы поговорить о народном театре. Проект Шехтеля всем нравится." }

Пример ответа:

{ "id": 14, "text": "Вечером собрались в редакции «Русской мысли», чтобы поговорить о народном театре. Проект Шехтеля всем нравится.", "author": "anton", "image": null, "group": 1, "pub_date": "2021-06-01T08:47:11.084589Z" }

Пример POST-запроса с токеном Антона Чехова: отправляем новый комментарий к посту с id=14. POST .../api/v1/posts/14/comments/

{ "text": "тест тест", }

Пример ответа:

{ "id": 4, "author": "anton", "post": 14, "text": "тест тест", "created": "2021-06-01T10:14:51.388932Z" }

Пример GET-запроса с токеном Антона Чехова: получаем информацию о группе. GET .../api/v1/groups/2/

Пример ответа:

{ "id": 2, "title": "Математика", "slug": "math", "description": "Посты на тему математики" }