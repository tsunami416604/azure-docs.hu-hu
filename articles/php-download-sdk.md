---
title: A PHP-hoz készült Azure SDK letöltése
description: Megtudhatja, hogyan töltse le és telepítse az Azure SDK-val készült PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: cfcf908145e8a384782953e045f9e10fd3c0e8f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639469"
---
# <a name="download-the-azure-sdk-for-php"></a>A PHP-hoz készült Azure SDK letöltése

## <a name="overview"></a>Áttekintés

Az Azure SDK for PHP tartalmaz, amelyek lehetővé teszik a fejlesztés, telepítéséhez és felügyeletéhez a PHP-alkalmazások az Azure-összetevők. Az Azure SDK for PHP konkrétan a következő:

* **A PHP klienskódtárak segítségével az Azure-**. Ezek osztálykönyvtárakhoz egy felületet biztosít az Azure-szolgáltatások, például az adatok szolgáltatások eléréséhez, és a felhőalapú szolgáltatások.
* **Az Azure parancssori felület Mac, Linux és Windows (az Azure CLI)**. Ez a központi telepítésére és felügyeletére az Azure-szolgáltatások, például az Azure Websites és az Azure virtuális gépek parancsokat. A bármilyen platformon, beleértve a Mac, Linux és a Windows Azure CLI munkát.
* **(Csak Windows) Azure PowerShell**. Ez olyan PowerShell-parancsmagok telepítése és kezelése az Azure-szolgáltatások, például a Cloud Services és a virtuális gépek.
* **Az Azure emulátorok (csak Windows)**. A számítási és tárolási emulátorok helyi emulátorok felhőszolgáltatások és adatok szolgáltatások, amelyek lehetővé teszik az alkalmazás helyi teszteléséhez. Az Azure emulátorok csak futtassa a Windows.

Az alábbi szakaszok ismertetik töltse le és telepítse a fent leírt összetevőit.

Ebben a témakörben található utasítások azt feltételezik, hogy [PHP] [ install-php] telepítve.

> [!NOTE]
> Php-hez 5.5-ös vagy újabb verzióját használja a PHP klienskódtárak segítségével az Azure-kell rendelkeznie.
>
>

## <a name="php-client-libraries-for-azure"></a>PHP-klienskódtárak Azure-hoz

A PHP Klienskódtárak segítségével az Azure-bA egy felületet biztosít az Azure-szolgáltatások, például az adatok szolgáltatások eléréséhez, és a felhőalapú szolgáltatások, az operációs rendszert. Ezek a könyvtárak a szerkesztő használatával is telepíthető.

A PHP Klienskódtárak segítségével az Azure használatával kapcsolatos információkért lásd: [a Blob szolgáltatás használata][blob-service], [használata a Table szolgáltatás] [ table-service] és [a Queue szolgáltatás használata][queue-service].

### <a name="install-via-composer"></a>Keresztül szerkesztő telepítése

1. [Telepítse a Git][install-git]. A Windows rendszeren is szüksége lesz a Git végrehajtható hozzáadása a PATH környezeti változóba.

2. Hozzon létre egy fájlt **composer.json** a projekt gyökérkönyvtárában, és az alábbi kód felvétele:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Töltse le **[composer.phar] [ composer-phar]** a projekt gyökérkönyvtárában.

4. Nyisson meg egy parancssort, és hajtsa végre ezt a projekt legfelső szintű

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Az Azure PowerShell és az Azure emulátorok

Az Azure PowerShell olyan PowerShell-parancsmagok telepítése és kezelése az Azure-szolgáltatások (például a Felhőszolgáltatások és virtuális gépek). Az Azure emulátorok emulátorok felhőszolgáltatások és adatok szolgáltatások, amelyek lehetővé teszik az alkalmazás helyi teszteléséhez. Ezek az összetevők támogatottak csak Windows.

Azure PowerShell és az Azure emulátorok telepítése javasolt módja a [Microsoft Webplatform-telepítő][download-wpi]. Vegye figyelembe, hogy is beállíthatja további fejlesztési összetevők, például a PHP, SQL Server, a Microsoft SQL Server és a WebMatrix Drivers telepítéséhez.

Azure PowerShell használatával kapcsolatos információkért lásd: [hogyan használható az Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felület telepítése és kezelése az Azure-szolgáltatások, például az Azure Websites és az Azure virtuális gépek parancsokat. Az Azure parancssori felület telepítésével kapcsolatos információkért lásd: [az Azure parancssori felület telepítése](cli-install-nodejs.md).

## <a name="next-steps"></a>További lépések

További információkért lásd: a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
