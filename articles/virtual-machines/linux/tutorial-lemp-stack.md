---
title: Oktatóanyag – LEMP üzembe helyezése Linux rendszerű virtuális gépen az Azure-ban
description: Ebből az oktatóanyagból elsajátíthatja, hogyan telepíthet LEMP stacket az Azure-ban üzemeltetett linuxos virtuális gépre
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d603dbf2746608f499ba37b4f17b533b64bc941
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80154355"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: LEMP-webkiszolgáló telepítése Linux rendszerű virtuális gépre az Azure-ban

Ez a cikk ismerteti, hogyan helyezhet üzembe NGINX-webkiszolgálót, MySQL-t és PHP-t (a LEMP-vermet) Ubuntu rendszerű virtuális gépen az Azure-ban. A LEMP-verem a népszerű [LAMP-verem](tutorial-lamp-stack.md) alternatívája, amelyet az Azure-ban is telepíthet. Ha szeretné működés közben megtekinteni a LEMP-kiszolgálót, telepíthet és konfigurálhat egy WordPress-webhelyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása (a LEMP-veremben az „L” betű)
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az NGINX, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LEMP-kiszolgálón

Ez a telepítés gyors teszteléshez és megvalósíthatósági vizsgálatokhoz használható.

Ez az oktatóanyag a CLI-t használja a [Azure Cloud Shellon](https://docs.microsoft.com/azure/cloud-shell/overview)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kód bármely blokkjának elejéről.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Az NGINX, a MySQL és a PHP telepítése

Futtassa az alábbi parancsot az Ubuntu-csomag forrásainak frissítéséhez és az NGINX, a MySQL és a PHP telepítéséhez. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

A rendszer felszólítja a csomagok és más függőségek telepítésére. Ezzel a folyamat telepíti a PHP MySQL-lel való használatához minimálisan szükséges PHP-bővítményeket.  

## <a name="verify-installation-and-configuration"></a>A telepítés és a konfigurálás ellenőrzése


### <a name="verify-nginx"></a>NGINX ellenőrzése

Ellenőrizze az NGINX verzióját a következő paranccsal:
```bash
nginx -v
```

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Az NGINX kezdőlapjának megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Használja azt a nyilvános IP-címet, amellyel SSH-kapcsolatot létesített a virtuális géppel:

![Az NGINX alapértelmezett oldala][3]


### <a name="verify-and-secure-mysql"></a>A MySQL ellenőrzése és védelme

Ellenőrizze a MySQL verzióját a következő paranccsal (ügyeljen a nagybetűs `V` paraméterre):

```bash
mysql -V
```

A MySQL telepítésének biztonságossá tételéhez, beleértve a gyökér jelszavának beállítását is `mysql_secure_installation` , futtassa a parancsfájlt. 

```bash
sudo mysql_secure_installation
```

Igény szerint beállíthatja a jelszó ellenőrzése beépülő modult (ajánlott). Ezután állítson be egy jelszót a MySQL root felhasználóhoz, és konfigurálja a környezete fennmaradó biztonsági beállításait. Javasoljuk, hogy az "Y" (igen) kérdésre válaszoljon az összes kérdésre.

Ha ki szeretné próbálni a MySQL funkcióit (MySQL-adatbázis létrehozása, felhasználók hozzáadása vagy a konfigurációs beállítások módosítása), jelentkezzen be a MySQL-be. Ez a lépés nem kötelező az oktatóanyag elvégzéséhez. 


```bash
sudo mysql -u root -p
```

Amikor végzett, a `\q` parancs beírásával lépjen ki a mysql parancssorból.

### <a name="verify-php"></a>A PHP ellenőrzése

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

Konfigurálja az NGINX-et a PHP FastCGI Process Manager (PHP-FPM) használatára. Futtassa az alábbi parancsokat az eredeti NGINX-kiszolgálóblokk konfigurációs fájljának biztonsági mentéséhez, majd szerkessze az eredeti fájlt egy tetszőleges szerkesztőben:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

A szerkesztőben cserélje le az `/etc/nginx/sites-available/default` tartalmát az alábbira. A megjegyzésekben találja a beállítások magyarázatát. Helyettesítse be a virtuális gép nyilvános IP-címét a *yourPublicIPAddress*, erősítse meg a `fastcgi_pass`PHP-verziót, és hagyja meg a többi beállítást. Ezután mentse a fájlt.

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
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
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

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan védheti meg a TLS/SSL-tanúsítványokkal rendelkező webkiszolgálókat.

> [!div class="nextstepaction"]
> [Biztonságos webkiszolgáló TLS-vel](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
