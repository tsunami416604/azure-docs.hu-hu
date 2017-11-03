---
title: "A CustomScript bővítménnyel használata a Linux virtuális gépeken |} Microsoft Docs"
description: "Ismerje meg, hogyan használható a CustomScript bővítménnyel a Linux virtuális gépek Azure klasszikus telepítési modellel készült alkalmazások központi telepítése."
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: guybo
ms.openlocfilehash: cb1fc9a44dc9e57d9cc9f1c546ad937d67e63c2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>LAMP-alkalmazás üzembe helyezése a Linuxhoz készült Azure CustomScript bővítménnyel
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A Resource Manager modellt használja LÁMPA verem telepítésével kapcsolatos információkért lásd: [Itt](../tutorial-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A Microsoft Azure CustomScript bővítménnyel Linux testre szabhatja a virtuális gépek (VM) lehetőséget biztosít a virtuális gép (például Python és Bash) által támogatott bármilyen programozási nyelven írt tetszőleges kód futtatásával. Ez nagyon rugalmas megoldást alkalmazás-telepítést több gépet automatizálásához.

A CustomScript bővítménnyel, az Azure portál, a Windows PowerShell vagy az Azure parancssori felület (CLI) használatával telepítheti.

Ebben a cikkben fogjuk használni a egy egyszerű LÁMPA alkalmazás egy Ubuntu virtuális gép üzembe helyezése az Azure parancssori felület a klasszikus üzembe helyezési modellel készült.

## <a name="prerequisites"></a>Előfeltételek
Ehhez a példához először létre kell hoznia két Azure virtuális gépeken futó Ubuntu 14.04 vagy újabb. A virtuális gépek nevezzük *parancsfájl-vm* és *lámpa-vm*. Egyedi neveket használja a virtuális gépek létrehozásakor. A parancssori felület parancsait futtatásához használt egyik, és egyet a LÁMPA alkalmazás központi telepítéséhez használandó.

Egy Azure Storage-fiókot és egy kulcs az eléréséhez (letölthető Ez az Azure-portál) is kell.

Ha segítségre van szüksége az Azure-on Linux virtuális gépek létrehozása [hozzon létre egy virtuális gép futó Linux](createportal.md).

A telepítés parancsok használata Ubuntu, de az egyetlen támogatott Linux distro telepítési módosíthatja.

A parancsfájl-virtuális gép virtuális Gépet az Azure parancssori felület telepítve, az Azure-bA működő kapcsolattal rendelkező rendelkeznie kell. Tekintse meg a Súgó [telepítése és konfigurálása az Azure parancssori felület](../../../cli-install-nodejs.md).

## <a name="upload-a-script"></a>A parancsfájl feltöltése
Parancsfájl futtatása egy távoli virtuális gépen a LÁMPA verem telepítéséhez és PHP lap létrehozása a CustomScript bővítménnyel fogjuk használni. Ahhoz, hogy a parancsfájl bárhonnan hozzáférhessen azt fogja feltölteni, az Azure blob.

### <a name="script-overview"></a>Parancsfájl áttekintése
A parancsfájl például egy LÁMPA verem telepíti az Ubuntu (beleértve a MySQL csendes telepítéshez beállítása), egy egyszerű PHP-fájlt ír és Apache kezdődik.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Töltse fel a parancsfájl
Mentse a parancsfájlt egy szövegfájlt, mint például *install_lamp.sh*, majd töltse fel az Azure Storage és. Ehhez egyszerűen az Azure parancssori felület. Az alábbi példa feltölti a "parancsfájlok" nevű tárolót. Ha a tároló nem létezik a lesz szüksége, először létrehozásához.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

A JSON-fájl, amely leírja a parancsprogram letöltése Azure Storage-ból is létrehozhat. Mentse ezt *public_config.json* (cseréje a "mystorage" nevű, a tárfiók):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>A bővítmény telepítése
A következő parancs segítségével mostantól a Linux CustomScript bővítménnyel telepíteni a távoli virtuális Gépet az Azure parancssori felület használatával.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

Az előző parancs letölti és futtatja a *install_lamp.sh* parancsfájl a nevű virtuális Gépre *lámpa-vm*.

Mivel az alkalmazás tartalmaz egy webkiszolgálón, ne felejtse el egy HTTP-figyelő port a távoli virtuális Gépen nyissa meg a következő parancsot.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás
Akkor is ellenőrizhesse milyen mértékben a egyéni parancsfájl futtatása, ha megnézi a naplófájl a távoli virtuális Gépre. SSH-kapcsolatot *lámpa-vm* és a naplófájlt a következő parancsot a farok.

    cd /var/log/azure/customscript
    tail -f handler.log

Miután lefuttatta a CustomScript bővítménnyel, megkeresheti az információ létrehozott PHP oldalra. Ebben a cikkben a például a PHP az oldal *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>További források
Az alapvető lépéseket segítségével összetettebb alkalmazásokat. Ebben a példában a telepítési parancsfájl az Azure Storage nyilvános blob néven lett mentve. Biztonságosabb megoldás az lenne a telepítési parancsfájl tárolót, mint a biztonságos blob egy [biztonságos hozzáférési aláírás](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

További erőforrások az Azure parancssori felület, Linux és a CustomScript bővítménnyel a következő találhatók.

[CustomScript bővítménnyel Linux virtuális gép testreszabása feladatok automatizálásához](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Az Azure Linux Extensions (GitHub)](https://github.com/Azure/azure-linux-extensions)