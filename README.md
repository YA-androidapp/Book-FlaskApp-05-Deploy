# Book-FlaskApp-05-Deploy

---

## VPS を使用する場合

以下の Ubuntu 環境を想定した手順を示します。

```bash
~$ grep -H "" /etc/*version ; grep -H "" /etc/*release
/etc/debian_version:stretch/sid
/etc/ec2_version:Ubuntu 16.04.3 LTS (Xenial Xerus)
/etc/lsb-release:DISTRIB_ID=Ubuntu
/etc/lsb-release:DISTRIB_RELEASE=16.04
/etc/lsb-release:DISTRIB_CODENAME=xenial
/etc/lsb-release:DISTRIB_DESCRIPTION="Ubuntu 16.04.6 LTS"
/etc/os-release:NAME="Ubuntu"
/etc/os-release:VERSION="16.04.6 LTS (Xenial Xerus)"
/etc/os-release:ID=ubuntu
/etc/os-release:ID_LIKE=debian
/etc/os-release:PRETTY_NAME="Ubuntu 16.04.6 LTS"
/etc/os-release:VERSION_ID="16.04"
/etc/os-release:HOME_URL="http://www.ubuntu.com/"
/etc/os-release:SUPPORT_URL="http://help.ubuntu.com/"
/etc/os-release:BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
/etc/os-release:VERSION_CODENAME=xenial
/etc/os-release:UBUNTU_CODENAME=xenial
```

### Apache のインストール

```bash
~$ sudo sed -i'~' -E "s@http://(..\.)?(archive|security)\.ubuntu\.com/ubuntu@http://ftp.jaist.ac.jp/pub/Linux/ubuntu@g" /etc/apt/sources.list
~$ sudo apt-get update -y && sudo apt-get upgrade -y
~$ sudo apt install apache2
~$ apache2 -v
Server version: Apache/2.4.18 (Ubuntu)
Server built:   2019-10-08T13:31:25
```

### Python3 のインストール

```bash
~$ sudo apt install python3.5
```

### Flask と mod_wsgi のインストール

```bash
~$ cd /opt
/opt$ sudo mkdir flaskroot
/opt$ cd flaskroot/
/opt/flaskroot$ sudo chown -R y:y
/opt/flaskroot$ python3 -m venv flaskenv
/opt/flaskroot$ . flaskenv/bin/activate
/opt/flaskroot$ pip install -U pip
/opt/flaskroot$ pip install mod_wsgi-httpd
/opt/flaskroot$ pip install mod_wsgi
/opt/flaskroot$ pip install flask
```

## Heroku を使用する場合

## Azure を使用する場合
