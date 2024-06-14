# Установка Qemu в Debian

## Проверяем поддержку виртуализации

Если команду ниже выводит число отличное от 0, то все в порядке.

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

## Установка необходимых пакетов

```bash
sudo apt-get install -y qemu-kvm qemu-system qemu-utils libvirt-clients libvirt-daemon-system bridge-utils virtinst libvirt-daemon virt-manager
```

Проверяем работу демона после установки:

```bash
sudo systemctl status libvirtd
```

## Настраиваем сеть по-умолчанию

```bash
sudo virsh net-start default
sudo virsh net-autostart default
sudo virsh net-list --all
```

## Добавляем пользователя в необходимые группы для работы с libvirtd

```bash
sudo usermod -aG libvirt $USER
sudo usermod -aG libvirt-qemu $USER
sudo usermod -aG kvm $USER
sudo usermod -aG input $USER
sudo usermod -aG disk $USER
```

## Перезапуск 

Последний шаг - перезапусе системы.
