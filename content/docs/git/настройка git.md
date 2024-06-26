# Настройка git

Настройка git происходит через `git config`.

## Хранения настроек

Настройки могут храниться в трех местах:

1. `/etc/gitconfig`, если использовать `git config --system`;
2. `~/.gitconfig` если использовать `git config --global`;
3. `.git/config` если использовать `git config --local`.

Чтобы просмотреть все существующие настройки:

```bash
git config --list --show-origin
```

## Настройки

### user.name и user.email

Первое, что следует настроить эту имя и почту пользователя. Например:

```bash
git config --global user.name "John Doe"
git config --global user.email "qJkZK@example.com"
```

### core.editor

Git нам позволяет явно указать редактор, который стоит использовать для коммитов, разрешения конфликтов и так далее.
По-умолчанию значение считывается из переменной среды `$EDITOR`.

```bash
git config --global core.editor nvim
```

### commit.template

Позволяет задать шаблон для коммита.

```bash
git config --global commit.template ~/.gitmessage
```

### core.pager

Определяет какая программа будет использована для разбиения текста на страницы.

```bash
git config --global core.pager nvimpager
```

### merge.tool

Указывает на инструмент, который надо использовать для команды `mergetool`.

```bash
git config --global merge.tool nvimdiff
```

### diff.external

Указывает на инструмент, который надо использовать для сравнения.

```bash
git config --global diff.external nvimdiff
```

### init.defaultBranch

Ветка, с которой проект создается по-умолчанию.

```bash
git config --global init.defaultBranch main
```

### pull.rebase

Какое поведение git использовать при получении изменений.

```bash
git config --global pull.rebase false # способ по-умолчанию
git config --global pull.rebase true # с использованием перебазирования
```

### alias.*

Git позволяет создавать псевдонимы. Например:

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
```

Советуется добавить:

```bash
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!nvim -c Neogit' # Поменяйте на свой редактор
```

### core.excludesfile

Какие файлы стоит игнорировать в проектах.

```bash
git config --global core.excludesfile ~/.gitignore_global
```

### color.ui

Стоит ли подсвечивать большую часть вывода.

```bash
git config --global color.ui true
git config --global color.ui false
```

Можно отдельно для разных элементов: *color.branch*, *color.diff*, *color.interactive*, *color.status*.
