---
title: "Настройка Git сервера с нуля на Debian 12"
sidebar:
 exclude: true
---

<!-- # Настройка Git сервера с нуля на Debian 12 -->

В этом руководстве мы рассмотрим, как настроить собственный Git сервер на базе Debian 12.
Мы пройдем через установку и настройку необходимых компонентов, включая:

- SSH;
- создание пользователей;
- инициализация проектов;
- настройка веб-интерфейса для просмотра репозиториев.

**Цель**: создать гибкую и безопасную систему управления версиями без использования сторонних сервисов.

## Настройка SSH на сервере

Первым шагом является установка и настройка SSH сервера, который обеспечит безопасный доступ к нашему серверу.

### Установка openssh-server

Для установка `openssh-server` выполните следующие команды:

```bash
apt update
apt install openssh-server
```

### Настройка openssh-server

Для начала надо открыть файл конфигурации SSH:

```bash
nano /etc/ssh/sshd_config
```

Измените следующие параметры для повышения безопасности:

- *Port*: измените стандартный порт на другой для уменьшения числа автоматических атак;
- *PermitRootLogin*: отключите вход по пользователю root после настройки отдельного пользователя для работы с сервером;
- *PasswordAuthentification*: отключите вход по паролю после настройки ключей;
- *PermitEmptyPasswords*: отключите пустые пароли(можно проигнорировать, если стоит `PasswordAuthentification: no`);
- *PubkeyAuthentification*: включите аутентификацию по ключам.

После внесения изменений перезапустите SSH службу:

```bash
systemctl restart sshd
```

## Создание пользователя *admin*

Для управления сервером создайте пользователя `admin` с правами `sudo`:

```bash
adduser admin
apt install sudo
usermod -aG sudo admin
su admin
cd
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys # Добавить ключ системного_администратора
```

После незабудьте установить с конфигурации SSH `PermitRootLogin no` и отключите доступ к пользователю *root*:

```bash
sudo nano /etc/passwd
# Меняем root:x:0:0:root:/root:/bin/bash на root:x:0:0:root:/root:/sbin/nologin
```

Если попробовать зайти в *root*, то выведется следующие сообщение: ***This account is currently not available.***

## Создание пользователя *git*

Для работы с Git создадим отдельного пользователя *git*:

```bash
adduser git
su git
cd 
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
```

## Генерация ключа на клиенте

Создайте SSH ключ на клиенте:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/key_for_git -C "Developer name"
ssh-keygen -t ed25519 -f ~/.ssh/key_for_admin -C "Developer name"
```

Настройте клиент SSH для работы с новым ключом:

```gitconfig
Host server_git
  HostName <hostname>
  StrictHostKeyChecking no
  User git
  Port <port>
  ForwardAgent yes
  IdentityFile /home/i4ox/.ssh/key_for_git
  IdentitiesOnly yes
  UserKnownHostsFile=/dev/null
  AddKeysToAgent yes
  ServerAliveInterval 60
  ServerAliveCountMax 1200

Host server_admin
  HostName <hostname>
  StrictHostKeyChecking no
  User admin
  Port <port>
  ForwardAgent yes
  IdentityFile /home/i4ox/.ssh/key_for_admin
  IdentitiesOnly yes
  UserKnownHostsFile=/dev/null
  AddKeysToAgent yes
  ServerAliveInterval 60
  ServerAliveCountMax 1200
```

Добавьте публичный ключ `server_git` в `/home/git/.ssh/authorized_keys`, также и для `server_admin`, но в `/home/admin/.ssh/authorized_keys`.
Отключите вход по паролю в SSH, если соединение через SSH проходит успешно.

## Инициализация проекта

После настройки пользователей и SSH, следующий шаг - это инициализация проекта на сервере.
В этом разделе рассмотрим как создать "голый" репозиторий, сделать первый коммит и настроить удаленный репозиторий для работы.


### Создание "голого" репозитория

"Голый" репозиторий(*--bare*) используется на сервере, чтобы хранить центральное хранилище кода.
Он не содержит рабочей копии файлов и предназначем исключительно для обмена данными между разработчиками.

**P.S**: подразумевается, что все команды выполняются от пользователя *admin*.

1. Установите Git, если он еще не установлен:

```bash
sudo apt install git
```

2. Создайте директорию для хранения репозиториев и настройте права доступа:

```bash
sudo mkdir -p /srv/git
sudo chown git:git /srv/git/
```

3. Создайте "голый" репозиторий:

```bash
sudo -u git -i
cd /srv/git
mkdir my_project.git
cd my_project.git
git init --bare
exit
```

### Настройка локального репозитория и первый коммит

Теперь настроим локальный репозиторий, сделаем первый коммит и отправим его на сервер.

1. На вашей локальной машине инициализируйте новый репозиторий:

```bash
mkdir my_project
cd my_project
git init
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"
git remote add origin git@hostname:/srv/git/my_project.git
git push -u origin main
```

## Настройка демона Git

Git-демон позволяет предоставлять доступ к репозиториям через протокол `git://`, что обеспечивает быструю и легкую настройку для чтения.

### Создание службы для systemd.

1. Создайте файл службы для Git-демона:

```bash
sudo nano /etc/systemd/system/git-daemon.service
```

2. Вставьте следующее содержимое:

```ini
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

3. Запустите и активируйте службу.

```bash
systemctl enable git-daemon
systemctl start git-daemon
systemctl status git-daemon
```

## Настройка GitWeb

GitWeb предоставляет веб-интерфейс для просмотра репозиториев Git. Рассмотрим два способы настройки:

- с использованием встроенного в Git *git instaweb*;
- через веб-сервер `nginx`.

### Использование git instaweb

*git instaweb* позволяет быстро развернуть веб-интерфейс для каждого репозитория.

1. Установите **lighttpd**, если он еще не установлен:

```bash
sudo apt install lighttpd
```

2. Перейдите в директорию проекта и запустите *git instaweb*:

```bash
cd /srv/git/my_project.git
git instaweb
```

3. Откройте браузер и перейдите по адресу ***http://hostname:1234***, чтобы увидеть интерфейс GitWeb:

![gitweb with libhttpd](./assets/gitweb_with_lighttpd.png)

4. Остановите сервер:

```bash
git instaweb --stop
```

### Настройка GitWeb через Nginx

1. Установите необходимые пакеты:

```bash
apt install nginx gitweb fcgiwrap
```

2. Настройте nginx для работы с GitWeb. Создайте конфигурационный файл для сайта:

```bash
sudo nano /etc/nginx/sites-available/gitweb
```

3. Вставьте следующее содержимое:

```conf
server {
    listen 80;
    server_name example.com;

    location /index.cgi {
        root /usr/share/gitweb/;
        include fastcgi_params;
        gzip off;
        fastcgi_param SCRIPT_NAME $uri;
        fastcgi_param GITWEB_CONFIG /etc/gitweb.conf;
        fastcgi_pass unix:/var/run/fcgiwrap.socket;
    }

    location / {
        root /usr/share/gitweb/;
        index index.cgi;
    }
}
```

4. Активируйте конфигурацию:

```bash
sudo ln -s /etc/nginx/sites-available/gitweb /etc/nginx/sites-enabled/gitweb
sudo rm /etc/nginx/sites-enabled/default
sudo systemctl restart nginx
```

5. Откройте браузер и перейдите по адресу вашего сервера, чтобы увидеть интерфейс GitWeb:

![nginx with gitweb](./assets/nginx_with_gitweb.png)

6. Обновите конфигурацию GitWeb для правильного отображения проектов. Откройте файл конфигурации:

```bash
sudo nano /etc/gitweb.conf
```

7. Измените путь к проектам:

```perl
our $projectroot = '/srv/git';
```

Теперь наш Git сервер настроен, и у нас есть доступ к репозиториям через SSH, Git-демон и веб-интерфейс GitWeb.
Это конфигурация обеспечивает как безопасность, так и удобство использования, а также поддержку анонимного и аутентифицированного доступа к репозиториям.

![nginx gitweb final](./assets/nginx_final_gitweb.png)


## Дополнительные аспекты безопасности и оптимизации

Скоро...

## ИДЕИ

- [ ] Создание резервных копий через rsync.
- [ ] Автоматическое обновление пакетов на сервере. Необходимо для получение последних фиксов безопасности.
- [ ] Настройка брандмауэра.
- [ ] Защита от brute-force атак.
- [ ] Мониторинг через Prometheus и Grafana.
- [ ] CI/CD через Jenkins или Gitlab Runner.


<!-- ### Ограничение доступа -->
<!---->
<!-- 1. Включение git-shell. -->
<!---->
<!-- `git-shell` - это специальная командная оболочка, где есть только команды git и ничего более. -->
<!---->
<!-- ```bash -->
<!-- nano /etc/shells # Добавить git-shell. -->
<!-- mkdir /home/git/git-shell-commands -->
<!-- chsh -s $(command -v git-shell) git -->
<!-- ``` -->
<!---->
<!-- В этой оболочке не будет абсолютно никаких комманд. Для того, чтобы они появились(если они нужны) надо создавать bash-сценарии в каталоге `git-shell-commands`. -->
<!---->
<!-- 2. Отключаем всю возможную переадресацию по SSH. -->
<!---->
<!-- Для этого добавляем `no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty` ко всем ключам в `.ssh/authorized_keys` -->
<!---->

