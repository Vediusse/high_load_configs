# Конфигурация corporate-catering

Git-репозиторий настроек Spring Cloud Config для [high_load](https://github.com/Vediusse/high_load), лабораторная 4.

- `application.yml` — общие настройки всех сервисов.
- `<spring.application.name>/application.yml` — настройки конкретного сервиса.
- `<spring.application.name>/application-<profile>.yml` — переопределения профиля, если понадобятся.
- Ветка `main` сохраняет конфигурацию lab3; ветка `lab4` используется лабораторной 4.

Config Server использует Git backend и `search-paths: '{application}'`, как в [примере преподавателя](https://github.com/leomag/configs-repo).
Например, `GET /order-service/default/lab4` объединяет общие настройки с `order-service/application.yml`.
`GET /order-service/dev/lab4` дополнительно применяет `application-dev.yml` в корне и папке сервиса, если они существуют.

Пароли и ключи не хранятся здесь: `${ORDER_DB_PASSWORD}`, `${CATALOG_DB_PASSWORD}` и остальные секреты подставляются из окружения клиентского сервиса.

Файлы хранятся в локальном S3 Silo, PostgreSQL — метаданные. Endpoint и ключи
задаются окружением File Service; содержимое файлов в этот репозиторий не попадает.

После commit/push в `lab4` выполните в checkout приложения
`python3 scripts/refresh-config.py --destination 'file-service:**'`.
Config Server передаст сигнал через Spring Cloud Bus Kafka в `runtime-config-events`
(3 partitions/RF3/minISR2), клиент перечитает Git. Значения не пересылаются в Kafka.
File Service применяет `files.max-bytes` и `files.allowed-mime-types` без перезапуска;
ошибочная политика сохраняет предыдущие значения. S3 connection settings требуют
перезапуска приложения. Активные значения: Swagger → File → `/api/v1/files/settings`
для SUPERVISOR. Управление refresh защищено отдельным локальным Config operator.

Сценарий демонстрации: [инструкция приложения](https://github.com/Vediusse/high_load/blob/feature/lab4/docs/lab4-s3-runtime.md).

Документация: [Git backend](https://docs.spring.io/spring-cloud-config/reference/4.3/server/environment-repository/git-backend.html), [Config Client](https://docs.spring.io/spring-cloud-config/reference/4.3/client.html).
