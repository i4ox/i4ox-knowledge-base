# Создание образов из Dockerfile

## Пример простого Dockerfile

```dockerfile
FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y cowsay fortune
```

## Ключевые команды

1. FROM - определяет базовый ОС.
2. RUN - определяет команды, исполняемые в командной оболочке внутри образа.
3. ENTRYPOINT - определяет точку входа внутри образа. То что будет выполняться при запуске.
4. LABEL - определяет метки образа.
