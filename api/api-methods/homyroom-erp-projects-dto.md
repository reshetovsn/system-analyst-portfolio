# Модели данных API (DTO)

Фрагмент описания DTO для методов ресурса "Проекты". Включены только краткие модели, которые используются в ответе метода `GET /api/v1/projects/`.

## 5.3.1 EstimateShortDTO

Краткая информация о смете для списков.

```json
{
  "id": number,
  "name": "string",
  "status": "string (draft/work/archive)",
  "full_price_project": number
}
```

## 5.3.2 DictionaryShortDTO

Информация о справочниках в проекте.

```json
{
  "id": number,
  "name": "string",
  "work_type": "string (work/material)"
}
```
