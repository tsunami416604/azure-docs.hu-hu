---
title: LEMP üzembe helyezése Linux rendszerű virtuális gépen az Azure-ban | Microsoft Docs
description: Oktatóanyag – A LEMP-verem telepítése Linux rendszerű virtuális gépen az Azure-ban
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: f907b468a409135d4b45e76297fc7cd86eeead78
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>LEMP-webkiszolgáló telepítése Azure-beli virtuális gépen
Ez a cikk ismerteti, hogyan helyezhet üzembe NGINX-webkiszolgálót, MySQL-t és PHP-t (a LEMP-vermet) Ubuntu rendszerű virtuális gépen az Azure-ban. A LEMP-verem a népszerű [LAMP-verem](tutorial-lamp-stack.md) alternatívája, amelyet az Azure-ban is telepíthet. Ha szeretné működés közben megtekinteni a LEMP-kiszolgálót, telepíthet és konfigurálhat egy WordPress-webhelyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása (a LEMP-veremben az „L” betű)
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az NGINX, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LEMP-kiszolgálón


Ez a telepítés gyors teszteléshez és megvalósíthatósági vizsgálatokhoz használható.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Az NGINX, a MySQL és a PHP telepítése

Futtassa az alábbi parancsot az Ubuntu-csomag forrásainak frissítéséhez és az NGINX, a MySQL és a PHP telepítéséhez. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

A rendszer felszólítja a csomagok és más függőségek telepítésére. Amikor a rendszer arra kéri, állítson be egy rendszergazdai jelszót a MySQL-hez, majd nyomja le az [Enter] billentyűt a folytatáshoz. Kövesse a lépéseket. Ezzel a folyamat telepíti a PHP MySQL-lel való használatához minimálisan szükséges PHP-bővítményeket. 

![A MySQL rendszergazdai jelszavának oldala][1]

## <a name="verify-installation-and-configuration"></a>A telepítés és a konfigurálás ellenőrzése


### <a name="nginx"></a>NGINX

Ellenőrizze az NGINX verzióját a következő paranccsal:
```bash
nginx -v
```

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Az NGINX kezdőlapjának megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Használja azt a nyilvános IP-címet, amellyel SSH-kapcsolatot létesített a virtuális géppel:

![Az NGINX alapértelmezett oldala][3]


### <a name="mysql"></a>MySQL

Ellenőrizze a MySQL verzióját a következő paranccsal (ügyeljen a nagybetűs `V` paraméterre):

```bash
mysql -V
```

A MySQL telepítésének biztosításához futtassa a `mysql_secure_installation` szkriptet. Ha csak egy ideiglenes kiszolgálót állít be, kihagyhatja ezt a lépést. 

```bash
mysql_secure_installation
```

Adjon meg egy rendszergazdai jelszót a MySQL számára, és konfigurálja a környezet biztonsági beállításait.

Ha ki szeretné próbálni a MySQL funkcióit (MySQL-adatbázis létrehozása, felhasználók hozzáadása vagy a konfigurációs beállítások módosítása), jelentkezzen be a MySQL-be. Ez a lépés nem kötelező az oktatóanyag elvégzéséhez. 


```bash
mysql -u root -p
```

Amikor végzett, a `\q` parancs beírásával lépjen ki a mysql parancssorból.

### <a name="php"></a>PHP

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

Konfigurálja az NGINX-et a PHP FastCGI Process Manager (PHP-FPM) használatára. Futtassa az alábbi parancsokat az eredeti NGINX-kiszolgálóblokk konfigurációs fájljának biztonsági mentéséhez, majd szerkessze az eredeti fájlt egy tetszőleges szerkesztőben:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

A szerkesztőben cserélje le az `/etc/nginx/sites-available/default` tartalmát az alábbira. A megjegyzésekben találja a beállítások magyarázatát. Helyettesítse be a virtuális gép nyilvános IP-címét a *yourPublicIPAddress* elem helyére, és hagyja változatlanul a többi beállítást. Ezután mentse a fájlt.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

Ellenőrizze, hogy vannak-e szintaxishibák az NGINX konfigurációjában:

```bash
sudo nginx -t
```

Ha a szintaxis helyes, indítsa újra az NGINX-et a következő paranccsal:

```bash
sudo service nginx restart
```

Ha további tesztelést szeretne végezni, hozzon létre egy rövid PHP-információs oldalt. amely a böngészőben tekinthető meg. A következő paranccsal hozhatja létre a PHP-információs oldalt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Most ellenőrizheti a létrehozott PHP-információs oldalt. Nyissa meg a böngészőt, és lépjen a `http://yourPublicIPAddress/info.php` webhelyre. Helyettesítse be a virtuális gép nyilvános IP-címét. Ennek a képen láthatóhoz hasonlóan kell kinéznie.

![PHP-információs oldal][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy LEMP-kiszolgálót helyezett üzembe az Azure-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az NGINX, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LEMP-vermen

Folytassa a következő oktatóanyaggal, amelyből megismerheti, hogyan tehetők biztonságossá a webkiszolgálók SSL-tanúsítványok használatával.

> [!div class="nextstepaction"]
> [Webkiszolgáló biztonságossá tétele SSL használatával](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
