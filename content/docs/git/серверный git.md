# Git на сервере

## Протоколы

- Локальный протокол(file://);
- HTTP протокол(https://);
- Git протокол(git://);
- SSH протокол(git@username:);

## Настройка сервера

```bash
sudo adduser git
su git
cd
mkdir .ssh && chmod 700 .ssh
touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
# Далее нужно добавить public ключи разработчиков в `authorized_keys`
cd /srv/git
mkdir project.git
cd project.git
git init --bare
```

Далее кто-то из разработчиков должен добавить первую версию проекта.

## Ограничение доступа

### Оболочка git-shell

```bash
cat /etc/shells # Проверяем есть ли git-shell
which git-shell # Проверяем установлен ли git-shell
sudo -e /etc/shells # Добавляем git-shell
sudo chsh git -s $(which git-shell) # Делаем так, чтобы пользователь git имел доступ только к оболочке самого git'а
```

### Ограничение переадресации по SSH

Добавляем в файл `authorized_keys` для всех ключей, такие параметры: `no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty`.


## Запуск демона Git

Демон Git - используется по-большей части для доступа без аутентификации.
Он используется на публичных проектах или если к нему должно иметь доступ большая часть людей(но за сетевым экраном).

```bash
git daemon --reuseaddr --base-path=/srv/git /srv/git
```

Флаг `--reuseaddr` - позволит серверу перезапускаться без ожидания таймаута существующих подключений.

Естественно демона можно запускать как службу:

```toml
# /etc/systemd/system/git-daemon.service
[Unit]
Description=Git daemon

[Service]
ExecStart=/usr/bin/git daemon --reuseaddr --base-path=/srv/git /srv/git
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

После чего службу можно запустить:

```bash
systemctl enable git-daemon
```

Далее в необходимый проект надо добавить `git-daemon-export-ok`
