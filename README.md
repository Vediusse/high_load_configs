# Конфигурация corporate-catering

Git-репозиторий настроек Spring Cloud Config для [high_load](https://github.com/Vediusse/high_load), лабораторная 3.

- `application.yml` — общие настройки всех сервисов.
- `<spring.application.name>/application.yml` — настройки конкретного сервиса.
- `<spring.application.name>/application-<profile>.yml` — переопределения профиля, если понадобятся.
- Ветка `main` — версия конфигурации по умолчанию (`label`).

Config Server использует Git backend и `search-paths: '{application}'`, как в [примере преподавателя](https://github.com/leomag/configs-repo).
Например, `GET /order-service/default/main` объединяет общие настройки с `order-service/application.yml`.
`GET /order-service/dev/main` дополнительно применяет `application-dev.yml` в корне и папке сервиса, если они существуют.

Пароли и ключи не хранятся здесь: `${ORDER_DB_PASSWORD}`, `${CATALOG_DB_PASSWORD}` и остальные секреты подставляются из окружения клиентского сервиса.

После изменения настроек сделайте commit/push в этот репозиторий и перезапустите нужные клиентские сервисы.
Пересобирать JAR или Docker-образ Config Server не требуется. Автоматический refresh уже работающих клиентов не настроен.

Документация: [Git backend](https://docs.spring.io/spring-cloud-config/reference/4.3/server/environment-repository/git-backend.html), [Config Client](https://docs.spring.io/spring-cloud-config/reference/4.3/client.html).
