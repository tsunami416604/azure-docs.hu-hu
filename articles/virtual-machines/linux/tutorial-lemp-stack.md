---
title: "LEMP telepítése egy Linux virtuális gép az Azure-ban |} Microsoft Docs"
description: "Az oktatóanyag - telepítés a LEMP verem a Linux virtuális gép az Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: c77cd0148a7e3e7b99e90e29bc1499dae8f95028
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>Egy Azure virtuális gépen LEMP webkiszolgáló telepítése
Ez a cikk végigvezeti egy NGINX webkiszolgálón, a MySQL és a PHP (a LEMP stack) az Azure-ban Ubuntu virtuális gép telepítése. A LEMP verem ahelyett, hogy a népszerű [LÁMPA verem](tutorial-lamp-stack.md), amely az Azure-ban is telepíthet. A művelet a LEMP kiszolgáló megtekintéséhez opcionálisan is telepítése és konfigurálása egy WordPress-webhely. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy Ubuntu virtuális gép (a "L" a LEMP verem)
> * A 80-as port megnyitása a webes adatforgalom számára
> * NGINX, a MySQL és a PHP telepítése
> * Ellenőrizze a telepítés és konfigurálás
> * WordPress telepítése a LEMP kiszolgálón


A telepítő a gyors vizsgálat vagy a koncepció igazolása szolgál.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>NGINX, a MySQL és a PHP telepítése

A következő parancsot Ubuntu csomag adatforrások frissítése, és NGINX, a MySQL és a PHP telepítése. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

A csomagok és más függőségek telepítése kéri. Amikor a rendszer kéri, egy gyökérszintű jelszót beállítása MySQL, majd [Enter] folytatja. Kövesse a hátralévő utasításokat. Ez a folyamat telepíti a minimálisan szükséges PHP-bővítmények a MySQL PHP használatához szükséges. 

![MySQL legfelső szintű jelszófrissítési lap][1]

## <a name="verify-installation-and-configuration"></a>Ellenőrizze a telepítés és konfigurálás


### <a name="nginx"></a>NGINX

A következő paranccsal NGINX verziójának ellenőrzése:
```bash
nginx -v
```

Az NGINX telepítve, és nyissa meg a virtuális géphez a 80-as porton a webkiszolgáló mostantól elérhető az internetről. A NGINX kezdőlapon megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Az SSH-kapcsolatot a virtuális gép használt nyilvános IP-cím használata:

![NGINX alapértelmezett oldal][3]


### <a name="mysql"></a>MySQL

A következő paranccsal MySQL verziójának ellenőrzése (vegye figyelembe a beruházási `V` paraméter):

```bash
mysql -V
```

Számítógépek biztonságossá tétele a MySQL telepítését, futtassa a `mysql_secure_installation` parancsfájl. Ha csak hoz létre egy ideiglenes kiszolgálót, kihagyhatja ezt a lépést. 

```bash
mysql_secure_installation
```

Adja meg a gyökér szintű jelszó MySQL, és adja meg a környezet biztonsági beállításait.

Ha ki szeretné próbálni a MySQL-szolgáltatások (MySQL-adatbázis létrehozása, vegye fel a felhasználók vagy konfigurációs beállításokat módosítaná,), MySQL bejelentkezni. Ez a lépés nem szükséges az oktatóanyag elvégzéséhez. 


```bash
mysql -u root -p
```

Ha befejezte, lépjen ki a mysql-parancssorba írja be a `\q`.

### <a name="php"></a>PHP

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

A PHP FastCGI folyamat-kezelő (PHP-FPM) használata NGINX konfigurálása. A következő parancsokat az eredeti NGINX kiszolgáló blokk konfigurációs fájljának biztonsági mentéséhez, és módosítsa az eredeti fájl egy az Ön által választott szerkesztőben:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

A szerkesztő, cserélje le a tartalmát `/etc/nginx/sites-available/default` a következőre. Tekintse meg a megjegyzéseket, a beállítások ismertetése. Helyettesítse be a virtuális Gépet, a nyilvános IP-címe *yourPublicIPAddress*, és hagyja meg a többi beállítást. Ezután mentse a fájlt.

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

Ellenőrizze a szintaxishibákat NGINX konfigurációját:

```bash
sudo nginx -t
```

Ha a szintaxisának helyességét, indítsa újra a NGINX a következő paranccsal:

```bash
sudo service nginx restart
```

Ha azt szeretné, további teszteléséhez, hozzon létre egy gyors PHP adatai lap használatával jeleníthetők meg a böngészőben. A következő parancs létrehozza a PHP-adatok lapján:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Most már a létrehozott PHP adatok lapján ellenőrizheti. Nyisson meg egy böngészőt, és navigáljon `http://yourPublicIPAddress/info.php`. Helyettesítse be a virtuális gép nyilvános IP-címét. Ez a rendszerkép hasonlóan kell kinéznie.

![A PHP adatai lap][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-ban LEMP kiszolgáló telepítette. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * NGINX, a MySQL és a PHP telepítése
> * Ellenőrizze a telepítés és konfigurálás
> * A LEMP veremben WordPress telepítése

A következő oktatóanyag megtudhatja, mennyire biztonságos SSL-tanúsítványokkal webkiszolgálók továbblépés.

> [!div class="nextstepaction"]
> [Biztonságos webkiszolgáló SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
