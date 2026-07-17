# Sequence: авторизация пользователя

Пример Sequence-диаграммы процесса авторизации пользователя с проверкой учетных данных, обработкой ошибки авторизации и выдачей access/refresh token.

![Sequence: авторизация пользователя](course-marketplace-auth-sequence.png)

## PlantUML

```plantuml
@startuml
actor Client
participant FE
participant BE
database DB

Client -> FE ++: Запуск
FE --> Client --: Страница с авторизацией
Client -> FE ++: Ввод пароля
FE -> FE: Валидация введенных данных
FE -> BE --++: POST /api/v1/login
BE -> DB --++: Найти пользователя по login

alt Пользователь не найден
DB --> BE ++: Нет пользователя
BE --> FE --++: 401 Unauthorized
FE --> Client --: Уведомление
else Пользователь существует
DB --> BE ++: Данные пользователя + RBAC
BE -> BE: Верификация $hash
  alt Хэш не совпадает
  BE --> FE++: 401 Unauthorized
  FE --> Client--: Уведомление
  else Хэш совпадает
  BE -> BE: Генерация A1+R1
  BE -> DB: Сохранить Refresh_token (R1)
  deactivate DB
  BE --> FE --++: 200 OK A1+R1
  FE -> FE: Сохранить A1 кэш, R1- cookie с флагом httpOnly
  FE --> Client --: редирект в личный кабинет
  end
end
Client -> BE: Запрос с A1
@enduml
```
