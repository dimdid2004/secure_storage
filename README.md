# Система безопасного хранения данных

## Основные возможности
- загрузка файлов через веб-интерфейс;
- разделение данных между несколькими объектными хранилищами;
- восстановление по пороговому числу фрагментов;
- проверка целостности данных на этапе восстановления;
- авторизация;
- разграничение доступа.

## Используемые технологии и методы
- ЯП: Python
- Фреймворки: FastAPI, SQLAlchemy
- БД для хранения метаданных: SQLite
- Объектные хранилища: MinIO
- Аутентификация: Keycloak
- Авторизация: Open Policy Agent
- Контейнеризация: Docker 
- Шифрование: AES (в режиме CTR)
- Разделение секрета: схема Педерсена — Шамира с модификацией на эллиптической кривой (https://github.com/dimdid2004/crypto_sharing_secret)
- Систематический код Рида-Соломона на основе матрицы Коши

## Структура проекта
```text
secure_storage/
├── app/
│   ├── backend/
│   │   ├── api/
│   │   │   └── routes/
│   │   ├── core/
│   │   ├── db/
│   │   ├── policies/
│   │   └── services/
│   ├── static/
│   ├── templates/
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
└── docker-compose.yml
```

## Запуск
Для настройки приложения предварительно необходимо создать и настроить файл `.env`, расположенный в корне проекта.
Основеные переменное окружения
| Переменная | Пример значения | Назначение |
|---|---|---|
| `KEYCLOAK_INTERNAL_URL` | `http://keycloak:8080` | Адрес Keycloak в виртуальной Docker-сети. Используется для обращения к Keycloak по имени сервиса. |
| `KEYCLOAK_PUBLIC_URL` | `http://localhost:8080` | Используется для редиректов на адрес Keycloak при авторизации |
| `KEYCLOAK_EXPECTED_ISSUER` | `http://localhost:8080/realms/secure_storage` | Используется для проверки, что токен был выдан нужным realm в Keycloak. |
| `KEYCLOAK_REALM` | `secure_storage` | Название realm в Keycloak, в котором настроены пользователи, роли и клиент приложения. |
| `KEYCLOAK_CLIENT_ID` | `secure-storage-app` | Идентификатор клиента Keycloak, через который приложение выполняет авторизацию пользователей. |
| `KEYCLOAK_CLIENT_SECRET` | `s5JfwAqj9igLTrcL5O8WpbbfvpGUoE6f` | Секрет клиента Keycloak. Используется приложением при взаимодействии с Keycloak |
| `APP_BASE_URL` | `http://localhost:8000` | При локальном запуске оставить таким |
| `SESSION_SECRET_KEY` | `secret` | Секретный ключ для подписи сессий. |
| `KEYCLOAK_DB_NAME` | `keycloak` | Имя базы данных PostgreSQL, используемой сервисом Keycloak. |
| `KEYCLOAK_DB_USER` | `keycloak` | Имя пользователя PostgreSQL для подключения Keycloak к своей базе данных. |
| `KEYCLOAK_DB_PASSWORD` | `keycloak_password` | Пароль пользователя PostgreSQL для БД Keycloak |
| `KEYCLOAK_ADMIN` | `admin` | Имя администратора Keycloak, создаваемого при первом запуске контейнера. |
| `KEYCLOAK_ADMIN_PASSWORD` | `admin123` | Пароль администратора Keycloak. |
| `KEYCLOAK_HOSTNAME` | `localhost` | Hostname, который использует Keycloak  |
| `MINIO_ROOT_USER` | `admin` | Имя админа MinIO. Используется для доступа к объектным хранилищам. |
| `MINIO_ROOT_PASSWORD` | `password123` | Пароль админа MinIO.|


Запуск всех сервисов осуществляется одной командой:

```bash
docker compose up --build -d
```

После запуска доступны:

- приложение: http://localhost:8000
- Keycloak: http://localhost:8080
- OPA: http://localhost:8181
- управление объектными хранилищами:
  - http://localhost:9001
  - http://localhost:9002
  - http://localhost:9003
  - http://localhost:9004

## Скриншоты работы приложения
### Процесс аутентификации через Keycloak
<img width="624" height="304" alt="image" src="https://github.com/user-attachments/assets/57556b65-c8b3-4c42-80c7-3e596110a6c1" />

### Главная страница приложения
<img width="624" height="304" alt="image" src="https://github.com/user-attachments/assets/510a86bb-00ee-4518-9b3a-c4eb7fdab6c2" />

### Работа с документом
<img width="624" height="304" alt="image" src="https://github.com/user-attachments/assets/f5be8109-c339-438f-b1d5-34832894148d" />

### Ограничение доступа
<img width="624" height="304" alt="image" src="https://github.com/user-attachments/assets/cd4a7ad5-d381-4b7a-a3b4-12ef3df1e812" />
