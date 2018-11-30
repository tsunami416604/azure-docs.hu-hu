---
title: A PHP-hoz készült Azure SDK letöltése
description: Ismerje meg, hogyan tölthet le és telepítse az Azure SDK a php-hez.
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
ms.openlocfilehash: 4f2c242e27d8a0ed6687eb4a3510bbce6e07694f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421487"
---
# <a name="download-the-azure-sdk-for-php"></a>A PHP-hoz készült Azure SDK letöltése

## <a name="overview"></a>Áttekintés

A php-hez készült Azure SDK összetevők, amelyek lehetővé teszik, hogy fejlesztése, üzembe helyezése és kezelése az Azure-ban PHP-alkalmazásokat tartalmaz. Pontosabban a php-hez készült Azure SDK a következőket tartalmazza:

* **A PHP-klienskódtárak Azure**. Ezek a kódtárak osztály lehetővé tevő interfészt biztosítanak az Azure-szolgáltatások, például az adatkezelési szolgáltatások eléréséhez, és felhőszolgáltatásokat fejleszthet.
* **Az Azure parancssori felület Mac, Linux és Windows (az Azure parancssori felület)**. Ez a központi telepítésére és felügyeletére az Azure-szolgáltatások, például az Azure Websites és az Azure Virtual Machines parancsokat. Az Azure CLI-vel munkahelyi bármilyen platformon, beleértve a Mac, Linux és Windows.
* **Az Azure PowerShell (csak Windows)**. Ez az üzembe helyezése és kezelése az Azure-szolgáltatások, például a Cloud Services és a virtuális gépek PowerShell-parancsmagok egy halmaza.
* **Az Azure-emulátorok (csak Windows)**. A számítási és tárolási emulátory systému olyan helyi emulátor cloud services és az adatkezelési szolgáltatások, amelyek lehetővé teszik, hogy az alkalmazás helyi tesztelése. Az Azure-emulátorok csak futtatható Windows.

Az alábbi szakaszok ismertetik töltse le és telepítse a fent leírt összetevőket.

A témakörben található utasítások azt feltételezik, hogy [PHP] [ install-php] telepítve.

> [!NOTE]
> 5.5-ös vagy újabb verziója használandó a PHP-klienskódtárak Azure PHP kell rendelkeznie.
>
>

## <a name="php-client-libraries-for-azure"></a>PHP-klienskódtárak Azure-hoz

A PHP-Klienskódtárak Azure illesztőfelületet biztosítanak az Azure-szolgáltatások, például az adatkezelési szolgáltatások eléréséhez, és a felhőszolgáltatások bármilyen operációs rendszerből. Ezek a könyvtárak a szerkesztő használatával is telepíthető.

Az Azure-hoz a PHP-Klienskódtárak használatával kapcsolatos információkért lásd: [használata a Blob szolgáltatás][blob-service], [a Table Service használata] [ table-service]és [a Queue szolgáltatás használata][queue-service].

### <a name="install-via-composer"></a>Composer-n keresztül telepítése

1. [A Git telepítése][install-git]. A Windows is szüksége lesz a Git végrehajtható hozzáadása a PATH környezeti változóhoz.

2. Hozzon létre egy fájlt **composer.json** a projekt gyökérkönyvtárában található és adja hozzá a következő kódot:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Töltse le **[composer.phar] [ composer-phar]** a projekt gyökérkönyvtárában.

4. Nyisson meg egy parancssort, és hajtsa végre ezt a projekt gyökérkönyvtárában

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Az Azure PowerShell és az Azure-emulátorok

Az Azure PowerShell az üzembe helyezése és kezelése az Azure-szolgáltatások (például a Cloud Services és Virtual Machines) PowerShell-parancsmagok egy halmaza. Az Azure-emulátorok emulátory systému a cloud services és az adatkezelési szolgáltatások, amelyek lehetővé teszik, hogy az alkalmazás helyi tesztelése is. Ezek az összetevők támogatott csak Windows.

Azure PowerShell-lel és az Azure-emulátorok telepítéséhez ajánlott módja a használja a [Microsoft Webplatform-telepítő][download-wpi]. Vegye figyelembe, hogy azt is beállíthatja más fejlesztési összetevők, például a PHP, SQL Server, a Microsoft SQL Server, a PHP és a WebMatrix Drivers telepítéséhez.

Azure PowerShell használatával kapcsolatos információkért lásd: [hogyan használja az Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI parancsok központi telepítésére és felügyeletére az Azure-szolgáltatások, például az Azure Websites és az Azure Virtual Machines. Azure CLI telepítésével kapcsolatos információkért lásd: [az Azure CLI telepítése](cli-install-nodejs.md).

## <a name="next-steps"></a>További lépések

További információkért lásd: a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
