# Backend Sound Line

## Описание

Backend музыкального сервиса Sound Line написан на C# с использованием ASP.NET Core Minimal API.

Он отвечает за обработку запросов от frontend, работу с базой данных PostgreSQL, хранение аудиофайлов в MinIO, загрузку треков и передачу аудио для воспроизведения.

## Используемые технологии

- C#
- .NET 10
- ASP.NET Core Minimal API
- Entity Framework Core
- PostgreSQL
- Npgsql
- MinIO / S3
- OpenAPI
- Docker
- Docker Compose

## Архитектура
```
Frontend
   |
   | HTTP
   v
ASP.NET Core Backend
   |
   +------> PostgreSQL
   |        метаданные
   |
   +------> MinIO
            аудиофайлы
```

PostgreSQL используется для хранения информации о треках, исполнителях, пользователях, альбомах, плейлистах и других сущностях.

MinIO используется для хранения самих аудиофайлов.

## Основные API

### Получение списка треков

GET /api/tracks

Backend возвращает информацию о треках, включая:

- ID трека
- название
- ID исполнителя
- имя исполнителя
- длительность
- путь к аудиофайлу
- URL для воспроизведения

### Загрузка трека

```POST /api/tracks/upload```

Backend принимает:

- аудиофайл
- название трека
- имя исполнителя
- длительность

При загрузке:

1. Выполняется поиск исполнителя в PostgreSQL.
2. Если исполнителя нет, он создаётся автоматически.
3. Аудиофайл загружается в MinIO.
4. Информация о треке сохраняется в PostgreSQL.

Поддерживаемые форматы:

- MP3
- FLAC
- WAV
- M4A

### Воспроизведение трека

```GET /api/tracks/{id}/audio```

Backend находит трек в PostgreSQL, получает путь к аудиофайлу и загружает его из MinIO.

Аудиофайл передаётся frontend для воспроизведения.

Поддерживается HTTP Range Processing, необходимый для перемотки аудио.

## Docker

Backend, PostgreSQL и MinIO запускаются через Docker Compose.

Запускаются три контейнера:

music-backend
music-postgres
music-minio

Для запуска:

```
cd Data
docker compose up -d --build
```

Для проверки:

```
docker ps
```

