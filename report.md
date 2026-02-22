# Отчет об отладке приложения Русаковича Сергея Анатольевича

## 1. Ошибка переменной окружения
### Опечатка validation_alias="**DATABSE_URL**"
```
database_url: str = Field(
        "postgresql+asyncpg://postgres:postgres@db:5432/postgres_typo",
    validation_alias="DATABSE_URL",
)
```

### Исправлено на validation_alias="**DATABASE_URL**"
```code
database_url: str = Field(
    "postgresql+asyncpg://postgres:postgres@db:5432/postgres_typo",
    validation_alias="DATABASE_URL",
)
```
___

## 2. Ошибка заполнения форма вакансии 
### При значении города `None` падает ошибка `AttributeError: 'NoneType' object has no attribute name
`
```
parsed_payloads.append(
    {
        "external_id": item.id,
        "title": item.title,
        "timetable_mode_name": item.timetable_mode.name,
        "tag_name": item.tag.name,
        "city_name": item.city.name.strip(),
        "published_at": item.published_at,
        "is_remote_available": item.is_remote_available,
        "is_hot": item.is_hot,
    }
)

```

### Добавена вариация получения значения `None`
```
parsed_payloads.append(
    {
        "external_id": item.id,
        "title": item.title,
        "timetable_mode_name": item.timetable_mode.name,
        "tag_name": item.tag.name,
        "city_name": getattr(item.city, "name", None),
        "published_at": item.published_at,
        "is_remote_available": item.is_remote_available,
        "is_hot": item.is_hot,
    }
)
```
___
## 3. Неточность в README.md

### Лишняя команда `source .env`

```
## Быстрый старт

1. Клонируйте репозиторий (или распакуйте проект из архива):
   `git clone --branch with-bugs https://github.com/selectel/be-test.git`
2. Создайте `.env` на основе примера:
   `cp .env.example .env`
3. Примените переменные окружения `.env`:
   `source .env`
4. Запуск через Docker Desktop:
   `docker compose up --build`
5. Проверка работоспособности:
   откройте `http://localhost:8000/docs`
6. Остановка и очистка:
   `docker-compose down -v`

```

### Убрана команда применения переменных окружения. При запуске через docker compose, контейнер сам подтягивает переменные 
```
## Быстрый старт

1. Клонируйте репозиторий (или распакуйте проект из архива):
   `git clone --branch with-bugs https://github.com/selectel/be-test.git`
2. Создайте `.env` на основе примера:
   `cp .env.example .env`
3. Запуск через Docker Desktop:
   `docker compose up --build`
4. Проверка работоспособности:
   откройте `http://localhost:8000/docs`
5. Остановка и очистка:
   `docker-compose down -v`
```
___
## 4. Ошибка в создании scheduler
### При создании передавались секунды `seconds=...`

```
scheduler.add_job(
        job,
        trigger="interval",
        seconds=settings.parse_schedule_minutes,
        coalesce=True,
        max_instances=1,
    )
```

### Изменено на правильную переменную `minutes=...`
```
scheduler.add_job(
        job,
        trigger="interval",
        minutes=settings.parse_schedule_minutes,
        coalesce=True,
        max_instances=1,
    )
```
___
## 5. Отсутствие rollback при ошибках
### В случае ошибки в базу могу попасть "битые" данные

```
async def get_session() -> AsyncSession:
    async with async_session_maker() as session:
        yield session
```

### При возникновении любого исключения выполнится `rollback()`
```
async def get_session() -> AsyncSession:
    async with async_session_maker() as session:
        try:
            yield session
        except:
            await session.rollback() 
            raise 
```
___
## 6. 
### 

```
```

### 
```

```