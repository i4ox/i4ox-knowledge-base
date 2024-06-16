---
title: "Настройка Git сервера с нуля"
sidebar:
 exclude: true
---

**P.S**: в качестве сервера используется *Debian 12*.

### Настройка SSH на сервере

1. Установка `openssh-server`.

```bash
apt update
apt install openssh-server
```

2. Настройка `/etc/ssh/sshd_config`.

Нужно настроить следующие параметры:

- Port: поменять его с дефолтного
- PermitRootLogin: отключить
- PasswordAuthentification: отключить(после добавления ключей)
- PermitEmptyPasswords: отключить
- PubkeyAuthentification: включить

3. Перезапускаем службу

```bash
systemctl restart sshd
```

### Пользователь git

Для работы с git на нашем сервере, создадим отдельного пользователя:

1. Создадим нового пользователя git

```bash
adduser git
su git

```

2. Настроим SSH для нового пользователя

```bash
cd 
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
```

3. Создаем публичный ключ на клиенте и прописываем в `.ssh/authorized_keys`.

При необходимости можно настроить клиент ssh:

```config
Host 192.168.122.208
  StrictHostKeyChecking no
  User git
  Port 443
  ForwardAgent yes
  IdentityFile /home/i4ox/.ssh/git_server
  IdentitiesOnly yes
  UserKnownHostsFile=/dev/null
  AddKeysToAgent yes
  ServerAliveInterval 60
  ServerAliveCountMax 1200
```

### Инициализация проекта

1. Инициализируем "голый" проект на сервере

```bash
su -
apt install git
mkdir /srv/git
chown git:git /srv/git/
su git
cd /srv/git
mkdir <project_name>.git
cd <project_name>.git
git init --bare
```

![git bare structure](./assets/git_bare_structure.png)

2. Сделаем первый коммит

```bash
git init
git add --all
git commit -m "initial commit"
git remote add origin git@192.168.122.208:/srv/git/<project_name>.git
git push origin main
```

### Ограничение доступа

1. Включение git-shell.

`git-shell` - это специальная командная оболочка, где есть только команды git и ничего более.

```bash
nano /etc/shells # Добавить git-shell.
mkdir /home/git/git-shell-commands
chsh -s $(command -v git-shell) git
```

В этой оболочке не будет абсолютно никаких комманд. Для того, чтобы они появились(если они нужны) надо создавать bash-сценарии в каталоге `git-shell-commands`.

2. Отключаем всю возможную переадресацию по SSH.

Для этого добавляем `no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty` ко всем ключам в `.ssh/authorized_keys`

### Настройка демона Git

1. Создание службы для systemd.

```toml
# /etc/systemd/system/git-daemon.service
[Unit]
Description=Git daemon

[Service]
ExecStart=/usr/bin/git daemon --reuseaddr --export-all --base-path=/srv/git/ /srv/git/
Restart=always
RestartSec=500ms

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=git-daemon

User=git
Group=git

[Install]
WantedBy=multi-user.target
```

2. Запуск службы.

```bash
systemctl enable git-daemon
systemctl start git-daemon
systemctl status git-daemon
```

![git daemon status](./assets/git_daemon_status.png)

Итого у нас имеется:

- безопасная аутентификация через SSH;
- анонимный, неаутентифицированный доступ через `git://` для просмотра файлов.

### Настройка сервера и установка GitWeb

Скоро ...

### В планах/Реализовано

- [X] Настройка SSH
- [X] Инициализация проекта/Нескольких проектов
- [X] Подключение к ним по SSH/паролю/анонимно
    - [X] Подключение по SSH
    - [X] Отключение входа по паролю
    - [X] Анонимный вход за счет демона Git(используется для просмотра репозиториев)
- [X] Настройка ограничения доступа
- [X] Настройка демона Git и анонимного входа(просмотр репозитория)
- [X] Настройка сервера
- [ ] Настройка клинта GitWeb
- [ ] Итоги
