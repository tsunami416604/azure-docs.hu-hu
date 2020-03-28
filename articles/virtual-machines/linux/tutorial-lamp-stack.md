---
title: Oktatóanyag – A LAMP telepítése Linux os virtuális gépen az Azure-ban
description: Ebből az oktatóanyagból elsajátíthatja, hogyan telepíthet LAMP stacket az Azure-ban üzemeltetett linuxos virtuális gépre
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 2a636ae5609d9cb5c81782af5a419a27d7880106
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154372"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: LAMP-webkiszolgáló telepítése Linux rendszerű virtuális gépre az Azure-ban

Ez a cikk ismerteti, hogyan helyezhet üzembe Apache-webkiszolgálót, MySQL-t és PHP-t (a LAMP-vermet) Ubuntu rendszerű virtuális gépen az Azure-ban. Ha az NGINX-webkiszolgálót szeretné használni, tekintse meg a [LEMP-veremmel](tutorial-lemp-stack.md) kapcsolatos oktatóanyagot. Ha szeretné működés közben megtekinteni a LAMP-vermet, telepíthet és konfigurálhat egy WordPress-webhelyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása (a LAMP-veremben az „L” betű)
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

Ez a telepítés gyors teszteléshez és megvalósíthatósági vizsgálatokhoz használható. További információt a LAMP-veremről – beleértve az éles környezetre vonatkozó javaslatokat – az [Ubuntu dokumentációjában](https://help.ubuntu.com/community/ApacheMySQLPHP) talál.

Ez az oktatóanyag az [Azure Cloud Shellen](https://docs.microsoft.com/azure/cloud-shell/overview)belüli CLI-t használja, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **Próbálja ki** a kódblokk tetejéről.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Az Apache, a MySQL és a PHP telepítése

Futtassa az alábbi parancsot az Ubuntu-csomag forrásainak frissítéséhez és az Apache, a MySQL és a PHP telepítéséhez. A parancs végén lévő beszúrási jel (^) a `lamp-server^` csomag nevének része. 


```bash
sudo apt update && sudo apt install lamp-server^
```

A rendszer felszólítja a csomagok és más függőségek telepítésére. Ezzel a folyamat telepíti a PHP MySQL-lel való használatához minimálisan szükséges PHP-bővítményeket.  

## <a name="verify-installation-and-configuration"></a>A telepítés és a konfigurálás ellenőrzése


### <a name="verify-apache"></a>Az Apache ellenőrzése

Ellenőrizze az Apache verzióját a következő paranccsal:
```bash
apache2 -v
```

Most, hogy az Apache telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Az Apache2 Ubuntu alapértelmezett oldalának megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Használja azt a nyilvános IP-címet, amellyel SSH-kapcsolatot létesített a virtuális géppel:

![Az Apache alapértelmezett oldala][3]


### <a name="verify-and-secure-mysql"></a>A MySQL ellenőrzése és védelme

Ellenőrizze a MySQL verzióját a következő paranccsal (ügyeljen a nagybetűs `V` paraméterre):

```bash
mysql -V
```

A MySQL telepítésének biztosítása érdekében, beleértve a `mysql_secure_installation` gyökérjelszó beállítását, futtassa a parancsfájlt. 

```bash
sudo mysql_secure_installation
```

Beállíthatja a Jelszó érvényesítése beépülő modult (ajánlott). Ezután állítson be egy jelszót a MySQL root felhasználó, és konfigurálja a fennmaradó biztonsági beállításokat a környezetben. Javasoljuk, hogy minden kérdésre válaszoljon az "Y" (igen) gombra.

Ha ki szeretné próbálni a MySQL funkcióit (MySQL-adatbázis létrehozása, felhasználók hozzáadása vagy a konfigurációs beállítások módosítása), jelentkezzen be a MySQL-be. Ez a lépés nem kötelező az oktatóanyag elvégzéséhez.

```bash
sudo mysql -u root -p
```

Amikor végzett, a `\q` parancs beírásával lépjen ki a mysql parancssorból.

### <a name="verify-php"></a>PHP ellenőrzése

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

Ha további tesztelést szeretne végezni, hozzon létre egy rövid PHP-információs oldalt. amely a böngészőben tekinthető meg. A következő paranccsal hozhatja létre a PHP-információs oldalt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Most ellenőrizheti a létrehozott PHP-információs oldalt. Nyissa meg a böngészőt, és lépjen a `http://yourPublicIPAddress/info.php` webhelyre. Helyettesítse be a virtuális gép nyilvános IP-címét. Ennek a képen láthatóhoz hasonlóan kell kinéznie.

![PHP-információs oldal][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy LAMP-kiszolgálót helyezett üzembe az Azure-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

A következő oktatóanyagra lépésként megtudhatja, hogyan biztosíthat webkiszolgálókat TLS/SSL-tanúsítványokkal.

> [!div class="nextstepaction"]
> [Biztonságos webkiszolgáló a TLS-sel](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
