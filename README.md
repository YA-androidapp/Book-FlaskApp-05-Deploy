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
~$ sudo apt install apache2-dev
```

### Python3 のインストール

```bash
~$ sudo apt install python3.5
```

### Flask と mod_wsgi のインストール

```bash
~$ cd /opt
/opt$ sudo mkdir flaskroot
/opt$ sudo chown -R y:y flaskroot
/opt$ cd flaskroot/
/opt/flaskroot$ python3 -m venv flaskenv
/opt/flaskroot$ . flaskenv/bin/activate
/opt/flaskroot$ pip install -U pip
/opt/flaskroot$ pip install mod_wsgi
/opt/flaskroot$ pip freeze | grep wsgi
mod-wsgi==4.7.1
/opt/flaskroot$ pip install flask
```

`mod_wsgi-py35.cpython-35m-x86_64-linux-gnu.so` が存在するかチェックします。

```bash
/opt/flaskroot$ find -name "mod_wsgi-py*.cpython-*m-x86_64-linux-gnu.so"
./flaskenv/lib/python3.5/site-packages/mod_wsgi/server/mod_wsgi-py35.cpython-35m-x86_64-linux-gnu.so
/opt/flaskroot$ ls /opt/flaskroot/flaskenv/lib/python3.5/site-packages/mod_wsgi/server/mod_wsgi-py35.cpython-35m-x86_64-linux-gnu.so
/opt/flaskroot/flaskenv/lib/python3.5/site-packages/mod_wsgi/server/mod_wsgi-py35.cpython-35m-x86_64-linux-gnu.so
```

- /opt/flaskroot\$ sudo nano /etc/apache2/mods-available/wsgi_flask.conf

```
WSGIPythonHome /opt/flaskroot/flaskenv
WSGISocketPrefix /var/run/wsgi
```

- /etc/apache2/mods-available/wsgi_flask.load

```
LoadModule wsgi_module /opt/flaskroot/flaskenv/lib/python3.5/site-packages/mod_wsgi/server/mod_wsgi-py35.cpython-35m-x86_64-linux-gnu.so
```

```bash
/opt/flaskroot$ sudo a2enmod wsgi_flask
Enabling module wsgi_flask.
To activate the new configuration, you need to run:
  service apache2 restart
/opt/flaskroot$ sudo service apache2 restart
```

```bash
/opt/flaskroot$ cd /etc/apache2/sites-available
/etc/apache2/sites-available$ sudo cp 000-default.conf 000-default.conf.bak
```

- /etc/apache2/sites-available/000-default.conf

```
        ### <VirtualHost></VirtualHost>の内側
        WSGIDaemonProcess flaskapp user=www-data group=www-data threads=5 python-path=/opt/flaskroot/flaskenv/lib/python3.5/site-packages
        WSGIScriptAlias /flaskapp /opt/flaskroot/flaskapp/wsgi.py
        WSGIScriptReloading On

        <Directory /opt/flaskroot/flaskapp>
                WSGIProcessGroup flaskapp
                WSGIApplicationGroup %{GLOBAL}
                Require all granted
        </Directory>
        ### <VirtualHost></VirtualHost>の内側
```

```bash
/etc/apache2/sites-available$ cd /opt/flaskroot
/opt/flaskroot$ mkdir flaskapp
/opt/flaskroot$ cd flaskapp
```

- app.py

```py
# -*- coding:utf-8 -*-
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'It works!'

if __name__ == '__main__':
    app.run()
```

- wsgi.py

```
import sys, os
sys.path.append('/opt/flaskroot/flaskapp')

from app import app as application # 'from app'のappは'app.py'のapp
```

```sh
sudo service apache2 reload
```

ブラウザで `http://<VPSのIPアドレス>/flaskapp` にアクセスして、 `It works!` と表示されることを確認します。

## Heroku を使用する場合

## Azure を使用する場合
