# Sequence: авторизация пользователя

Пример Sequence-диаграммы процесса авторизации пользователя с проверкой учетных данных, обработкой ошибки авторизации и выдачей access/refresh token.

![Sequence: авторизация пользователя](course-marketplace-auth-sequence.png)

## PlantUML

```plantuml
@startuml
actor client
participant FE
participant BE
database DB

== Ввод пароля / авторизация ==
client -> FE ++ : Вводит логин/пароль
FE -> FE : Проверяет поля
FE -> BE --++ : POST /api/v1/login
BE -> BE : Валидирует запрос
BE -> DB ++ : Найти пользователя

== Поиск по базе / верификация пароля ==
alt пользователь не найден
DB --> BE  : пользователь не найден
BE --> FE ++ : 401 Unauthorized \nНеверный логин или пароль
FE --> client -- : Отдает уведомление
else пользователь найден
DB --> BE -- : данные пользователя ($hash) + RBAC
BE -> BE : Верификация пароля по $hash
alt пароли не совпадают
BE --> FE ++ : 401 Unauthorized \nНеверный логин или пароль
FE --> client -- : Отдает уведомление
else пароли совпадают
BE -> BE : Генерация A1 + R1
BE -> DB ++ : Сохранить hash R1 для user_id
DB --> BE -- : OK
BE --> FE --++ : 200 OK \nBody: A1 \nSet-Cookie: R1; HttpOnly; Secure
FE -> FE : Сохранить A1 в памяти \nБраузер сохраняет R1 в cookie
FE --> client -- : Редирект на страницу пользователя
end
end

== Запрос к защищенному ресурсу ==
client -> FE ++ : Запрос
FE -> BE ++ : Запрос\nAuthorization: Bearer A1
BE -> BE : Проверить подпись и срок действия A1

@enduml
```
