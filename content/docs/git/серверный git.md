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
