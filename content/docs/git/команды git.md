# Команды git

## git help

Выводит справочную информацию о той или иной команде.

```bash
git help add
```

## git config

Позволяет взаимодействовать с конфигурацией git.

```bash
git config --global user.name "John Doe"
git config --global user.email "qJkZK@example.com"
git config --list --show-origin
```

## git init

Инициализирует новый проект.

```bash
git init
```

## git add

Индексирует указанные файлы.

```bash
git add --all # Все файлы
git add *.md # С определенным расширением
git add . # Все файлы в корне(кроме скрытых)
git add README.md # Определенный файл/директория
```

## git commit

Делает коммит с проиндексированными файлами.

```bash
git commit
git commit -m "commit message" # Добавляет сообщение к комиту
git commit -a # Заставляет проиндексировать файлы ранее проиндексированные в предыдущих коммитах
git commit --amend # Отменяет предыдущий коммит, переопределяет его
```

## git clone

Клонирует существующий репозиторий.

```bash
git clone https://github.com/i4ox/dotfiles.git # По https
git clone git@github.com:i4ox/dotfiles.git # По ssh
git clone git@github.com:i4ox/dotfiles.git ~/Projects/dotfiles # Клонирование в определенное место
```

## git status

Позволяет просматривать состояние файлов(Untracked, Unmodified, Modified, Staged).

```bash
git status
git status -s # Сокращенный вывод
git status content/ # Проверить статус определенного файла или файлов в директории
```

## git diff

Позволяет просматривать отдельные файлы на изменение содержимого.

```bash
git diff
git diff --staged #(--cached) Только по отношению к файлам с состоянием Staged
git diff README.md # Для конкретного файла или директории    
```

## git rm

Удаление файла из индексации репозитория.

```bash
git rm README.md
git rm -f README.md # Удалить принудительно
```

## git mv

Команда чтобы переименовать файл.

```bash
git mv README.md README
```

Является alias для команд ниже:

```bash
mv README.md README
git rm README.md
git add README
```

## git log

Позволяет просматривать историю коммитов.

```bash
git log
git log --graph --decorate --oneline # Чаще всего используется
git log --stat # Краткая статистика
```

## git restore / git reset 

`git restore` это современная замена `git reset`.

Отменяет индексацию файла.

```bash
git add README.md
git restore --staged README.md
```

## git remote

Позволяет взаимодействовать с удаленными репозиториями.

```bash
git remote # выведет названия доступных удаленных репозиториев
git remote -v # выведет адреса привязанные к репозиторию
git remote add origin https://github.com/i4ox/dotfiles.git # Позволяет добавить удаленный репозиторий под именем origin
git remote show origin # позволяет просматривать удаленный репозиторий
git remote rename origin i4ox # Позволяет переименовать удаленный репозиторий
git remote remove i4ox # Позволяет удалить удаленный репозиторий
```

## git fetch

Позволяет подгрузить изменение с определенного удаленного репозитория.

```bash
git fetch origin
git remote add i4ox https://github.com/i4ox/dotfiles.git
git fetch i4ox
```

## git pull

Тот же `git fetch`, но для основного сервера, как правило, и более прост в использовании.

```bash
git pull
```

## git push

Отправка изменений в удаленный репозиторий.

```bash
git push origin master # Сначала указывается репозиторий, потом ветка
git push origin v1.1 # Отправляем тег
git push origin --tags # Отправляем все теги
git push origin --follow-tags # Отправляет только обычные теги(аннотированные)
git push origin :refs/tags/v1.1 # Удаляем тег. Способ №1
git push origin --delete v1.1 # Удаляем тег. Способ №2
git push origin --delete old_branch # Удаляет старую ветку
```

## git tag

Позволяет задавать теги.

```bash
git tag v1.1-lw # создает легковестный тег, без дополнительной информации
git tag -a v1.1 # создает обычный(аннотированный) тег
git tag -a v1.1 -m "v1.1" # создает обычный тег с сообщением
git tag -a v1.0 9fceb02 # создаем отложенный тег, тег заданный уже созданному коммиту
git tag -d v1.1 # Удаление тега
```

Теги нужно отдельно отправлять в удаленный репозиторий. Смотрите команду `git push`.

Также обратите внимание на удаление тега на удаленном репозитории, также через команду `git push`.

## git show

Позволяет просматривать информацию о файлах, деревьях, тегах и коммитах.

```bash
git show v1.1
```

## git checkout

Позволяет переключаться между ветками.

```bash
git checkout v1.1 # Переключение на тег
git checkout -b new_version v1.1 # Создает новую ветку, которая сместится, если создать коммит, условоно это будет v1.1.1
git checkout --track i4ox/v1.1 # Будет отслежить i4ox/v1.1 вместо origin/v1.1
```

## git branch

Позволяет создать новую ветку.

```bash
git branch testing # Создает новую ветку с названием testing
git branch --all # Выводит все ветки
git branch -d testing # Удаляет ветку
git branch -v # Позволяет просмотреть последний коммит на каждой из веток
git branch --merged # Показывает ветки, которые уже слиты с текущей
git branch --no-merged # Показывает ветки, которые не слиты с текущей
git branch --move old_name new_name # Позволяет переименовать ветку(локально)
git branch -u i4ox/testing # Позволяет создать ветку и подключить её к удаленному репозиторию
```

Новую ветку, созданную командой `git branch --move`, надо отправить в удаленный репозиторий.

## git merge

Позволяет провести слияние веток.

```bash
git merge testing # Делает слияние текущей ветки с веткой testing
```

Иногда Git не может сам решить конфликты, и оставляет маркеры для того, чтобы их можно было разрешить вручную.

Пример:

```html
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

В этом примере произошел конфликт текущей ветки с веткой iss53 в файле index.html.

## git mergetool

Открывает инструмент для разрешения конфликтов.

```bash
git mergetool
```

## git ls-remote

Позволяет просмотреть все удаленный ссылки в удаленном репозитории.

```bash
git ls-remove git@github.com:i4ox/dotfiles
```

## git rebase

Накладывает все коммиты одной ветки поверх коммитов другой ветки.

```bash
git rebase master server
git rebase --onto master server client # Берем изменения из client, которых  нету в server и применяем их к мастер
```
