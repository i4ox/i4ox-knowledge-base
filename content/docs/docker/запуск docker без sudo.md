# Запуск Docker без sudo

Нужно просто добавить пользователя в нужную группу, а затем перезапустить docker.

```bash
sudo usermod -aG docker $USER
sudo systemctl restart docker
```
