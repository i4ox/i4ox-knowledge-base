# Extra Packages For Enterprise Linux и RPMFusion

1. Установка EPEL

```bash
sudo dnf config-manager --set-enabled crb
sudo dnf install epel-release
sudo /usr/bin/crb enable 
```

2. Установка RPMFusion

```bash
sudo dnf install --nogpgcheck https://dl.fedoraproject.org/pub/epel/epel-release-latest-$(rpm -E %rhel).noarch.rpm
sudo dnf install --nogpgcheck https://mirrors.rpmfusion.org/free/el/rpmfusion-free-release-$(rpm -E %rhel).noarch.rpm https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm
```