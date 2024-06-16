# Блокировка пользователя root

**P.S**: У вас должен быть пользователь с включенным sudo. Иначе вы не сможете настраивать сервер в дальнейшем.

### Отключаем логин через su -

```bash
sudo nano /etc/passwd
# Меняем root:x:0:0:root:/root:/bin/bash на root:x:0:0:root:/root:/sbin/nologin
```

### Отключаем вход в root через ssh

Используйте `PermitRootLogin no` в `/etc/ssh/sshd_config`.
