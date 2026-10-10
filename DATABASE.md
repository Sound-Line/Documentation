# Music Service — Database Documentation

Документация по схеме базы данных музыкального сервиса.

---

## 1. Обзор

| Параметр | Значение |
|----------|----------|
| **СУБД** | PostgreSQL 16+ |
| **Схема** | `music` |
| **Расширения** | `citext`, `pgcrypto`, `pg_trgm` |
| **Таблиц** | 12 |
| **Связей 1:N** | 9 |

### Назначение

БД хранит **метаданные** музыкального сервиса:

- пользователей;
- артистов;
- альбомы и треки;
- плейлисты;
- историю прослушиваний;
- лайки;
- подписки на артистов;
- устройства.

**Аудиофайлы хранятся не в БД**, а в MinIO (S3-совместимое хранилище). В БД — только **путь к объекту** (`tracks.audio_url`).

---

## 2. ER-диаграмма

```
users ──1:N──► artists (created_by_user_id)
  │
  ├──1:N──► playlists ──1:N──► playlist_tracks ◄──N:1── tracks
  │                                                          ▲
  ├──1:N──► plays ◄──────────────────────────────────────────┤
  ├──1:N──► likes ◄──────────────────────────────────────────┤
  ├──1:N──► listening_history ◄──────────────────────────────┤
  ├──1:N──► devices                                          │
  ├──1:N──► tracks (uploaded_by_user_id) ────────────────────┤
  ├──1:N──► artist_followers ──N:1──► artists ──1:N──► tracks│
  │                                       │                  │
  │                                       └──1:N──► albums ──┘
  │                                       (album_id — опционален)
  │
  └──1:N──► listening_rooms (host_user_id)
                 │
                 ├──1:N──► room_participants ◄──N:1── users
                 │
                 ├──1:N──► room_queue ────────N:1──► tracks
                 │
                 └──1:N──► room_play_history ─N:1──► tracks

genres ──1:N──► tracks
```
