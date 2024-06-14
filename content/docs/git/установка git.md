# Установка Git

Git можно установить либо из официального репозитория:

```bash
sudo apt-get install -y git
```

Либо из исходников:

```bash
sudo apt-get install -y install-info dh-autoreconf libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev sciidoc xmlto docbook2x
sudo ln -s /usr/bin/db2x_docbook2texi /usr/bin/docbook2x-texi
tar -zxf git-2.39.2.tar.gz
cd git-2.39.2
make configure
./configure --prefix=/usr
make all doc info
sudo make install install-doc install-html install-info
```
