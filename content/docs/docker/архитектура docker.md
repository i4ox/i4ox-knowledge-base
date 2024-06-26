# Архитектура Docker

Docker состоит из следующих копмонентов:

- демон: ответственен за создание, запуск и контроль работы контейнера, за создание и хранения образов;
- клиент: используется для общения с демоном по протоколу http;
- реестр: используется для хранения и распространения образов.

## Драйвер выполнения

Docker использует "драйвер выполнения" для создания контейнеров.
По-умолчанию выбирается собственный `runc`, но также поддерживается `LXC`.

"Драйвер выполнения" тесно связан со следующими механизмами ядра:

1. cgroups - отвечает за управления ресурсами системы, используется также для `docker pause`;
2. namespaces - отвечает за управление процессами внутри контейнера;

## Система безопасности

Docker поддерживает SELinux и AppArmor для того, чтобы настроить более строгую среду безопасности.

## Union Filesystem

Ищите в отдельном пункте про Docker.

## Сопроваждающие плагины

Docker на текущий момент предоставляет следующие технологии(плагины):

1. Docker Swarm - решает задачу кластеризации в Docker. Deprecated в пользу k8s;
2. Docker Compose - инструмент для создания и выполнения приложения, созданных из нескольких контейнеров;
3. Docker Machine - позволяет устанавливать и конфигурировать Docker на локальных и удаленных ресурса. Deprecated;
4. Docker Desktop - инструмент для удобного управления контейнерами с GUI;
5. Docker Tristed Registry - локально устанавливаемая система для управления образами Docker.

Стоит отметить, что есть возможность замены компонентов сторонними наработками.
