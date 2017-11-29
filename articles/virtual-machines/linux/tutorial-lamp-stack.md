---
title: "LÁMPA telepítése egy Linux virtuális gép az Azure-ban |} Microsoft Docs"
description: "Az oktatóanyag - telepítés a LÁMPA verem a Linux virtuális gép az Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 8fcf411db844e227e0c4db0e690a1832f98b42f1
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Egy Azure virtuális gépen LÁMPA webkiszolgáló telepítése
Ez a cikk végigvezeti egy Apache webkiszolgálón, a MySQL és a PHP (a LÁMPA stack) az Azure-ban Ubuntu virtuális gép telepítése. Ha inkább a NGINX webkiszolgáló, tekintse meg a [LEMP verem](tutorial-lemp-stack.md) oktatóanyag. A művelet a LÁMPA kiszolgáló megtekintéséhez opcionálisan is telepítése és konfigurálása egy WordPress-webhely. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy Ubuntu virtuális gép (a "L" a LÁMPA verem)
> * A 80-as port megnyitása a webes adatforgalom számára
> * Apache, a MySQL és a PHP telepítése
> * Ellenőrizze a telepítés és konfigurálás
> * WordPress telepítése a LÁMPA kiszolgálón


A telepítő a gyors vizsgálat vagy a koncepció igazolása szolgál. A LÁMPA verem, beleértve az éles környezetbe, ajánlásokat bővebben lásd: a [Ubuntu dokumentáció](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Apache, a MySQL és a PHP telepítése

A következő parancsot Ubuntu csomag adatforrások frissítése, és Apache, a MySQL és a PHP telepítése. Vegye figyelembe a beszúrási jel (^), a parancs része végén, a `lamp-server^` csomag neve. 


```bash
sudo apt update && sudo apt install lamp-server^
```


A csomagok és más függőségek telepítése kéri. Amikor a rendszer kéri, egy gyökérszintű jelszót beállítása MySQL, majd [Enter] folytatja. Kövesse a hátralévő utasításokat. Ez a folyamat telepíti a minimálisan szükséges PHP-bővítmények a MySQL PHP használatához szükséges. 

![MySQL legfelső szintű jelszófrissítési lap][1]

## <a name="verify-installation-and-configuration"></a>Ellenőrizze a telepítés és konfigurálás


### <a name="apache"></a>Apache

A következő paranccsal Apache verziójának ellenőrzése:
```bash
apache2 -v
```

Az Apache telepítve, és nyissa meg a virtuális géphez a 80-as porton a webkiszolgáló most már elérhető az internetről. A Apache2 Ubuntu alapértelmezett lapnak a megtekintésére, nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Az SSH-kapcsolatot a virtuális gép használt nyilvános IP-cím használata:

![Apache alapértelmezett oldal][3]


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

Ha azt szeretné, további teszteléséhez, hozzon létre egy gyors PHP adatai lap használatával jeleníthetők meg a böngészőben. A következő parancs létrehozza a PHP-adatok lapján:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Most már a létrehozott PHP adatok lapján ellenőrizheti. Nyisson meg egy böngészőt, és navigáljon `http://yourPublicIPAddress/info.php`. Helyettesítse be a virtuális gép nyilvános IP-címét. Ez a rendszerkép hasonlóan kell kinéznie.

![A PHP adatai lap][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-ban LÁMPA kiszolgáló telepítette. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * Apache, a MySQL és a PHP telepítése
> * Ellenőrizze a telepítés és konfigurálás
> * WordPress telepítése a LÁMPA kiszolgálón

A következő oktatóanyag megtudhatja, mennyire biztonságos SSL-tanúsítványokkal webkiszolgálók továbblépés.

> [!div class="nextstepaction"]
> [Biztonságos webkiszolgáló SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png