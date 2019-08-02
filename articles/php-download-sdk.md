---
title: A PHP-hoz készült Azure SDK letöltése
description: Ismerje meg, hogyan töltheti le és telepítheti a PHP-hez készült Azure SDK-t.
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
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67873898"
---
# <a name="download-the-azure-sdk-for-php"></a>A PHP-hoz készült Azure SDK letöltése

## <a name="overview"></a>Áttekintés

A PHP-hez készült Azure SDK olyan összetevőket tartalmaz, amelyek lehetővé teszik a PHP-alkalmazások fejlesztését, üzembe helyezését és kezelését az Azure-hoz. A PHP-hez készült Azure SDK a következőket tartalmazza:

* **Az Azure**-hoz készült php-ügyféloldali kódtárak. Ezek a kódtárak olyan felületet biztosítanak az Azure-szolgáltatások eléréséhez, mint például az adatkezelési szolgáltatások és a felhőalapú szolgáltatások.
* **A Mac, Linux és Windows rendszerhez készült Azure parancssori felület (Azure CLI)** . Ez az Azure-szolgáltatások, például az Azure websites és az Azure Virtual Machines üzembe helyezésére és kezelésére szolgáló parancsok összessége. Az Azure CLI bármilyen platformon működik, beleértve a Mac, Linux és Windows rendszert is.
* **Azure PowerShell (csak Windows)** . Ez az Azure-szolgáltatások üzembe helyezésére és kezelésére szolgáló PowerShell-parancsmagok, például a Cloud Services és a Virtual Machines.
* **Az Azure-emulátorok (csak Windows)** . A számítási és tárolási emulátorok a Cloud Services és az adatkezelési szolgáltatások helyi emulátorai, amelyek lehetővé teszik az alkalmazások helyi tesztelését. Az Azure-emulátorok csak Windows rendszeren futnak.

Az alábbi szakaszok a fent ismertetett összetevők letöltését és telepítését ismertetik.

A jelen témakörben szereplő utasítások feltételezik, hogy telepítve van a [php][install-php] .

> [!NOTE]
> Az Azure-hoz készült PHP-ügyféloldali kódtárak használatához PHP 5,5 vagy újabb verzió szükséges.
>
>

## <a name="php-client-libraries-for-azure"></a>PHP-klienskódtárak Azure-hoz

Az Azure-hoz készült PHP-ügyféloldali kódtárak olyan felületet biztosítanak az Azure-szolgáltatások, mint például az adatkezelési szolgáltatások és a felhőalapú szolgáltatások eléréséhez bármely operációs rendszerről. Ezek a kódtárak a zeneszerző használatával telepíthetők.

További információ az Azure-hoz készült PHP ügyféloldali kódtárak használatáról: [a blob szolgáltatás használata][blob-service], [a Table Service][table-service] használata és [a üzenetsor-szolgáltatás][queue-service]használata.

### <a name="install-via-composer"></a>Telepítés a Zeneszerzőn keresztül

1. [Telepítse a Git szoftvert][install-git]. Windows rendszeren a git végrehajtható fájlt is hozzá kell adnia a PATH környezeti változóhoz.

2. Hozzon létre egy **zeneszerző. JSON** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a következő kódot:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. A **[zeneszerző. farmakovigilancia][composer-phar]** letöltése a projekt gyökerében.

4. Nyisson meg egy parancssort, és hajtsa végre ezt a projekt gyökerében

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell és Azure-emulátorok

A Azure PowerShell PowerShell-parancsmagokat tartalmaz az Azure-szolgáltatások üzembe helyezéséhez és kezeléséhez (például Cloud Services és Virtual Machines). Az Azure-emulátorok a Cloud Services és az adatkezelési szolgáltatások emulátorai, amelyek lehetővé teszik az alkalmazások helyi tesztelését. Ezek az összetevők csak a Windows támogatottak.

A Azure PowerShell és az Azure-emulátorok telepítésének ajánlott módja a [Microsoft webplatform-telepítő][download-wpi]használata. Vegye figyelembe, hogy más fejlesztői összetevőket is telepít, például a PHP-t, a SQL Servert, a PHP-hoz készült Microsoft-SQL Server illesztőprogramokat és a WebMatrix.

További információ a Azure PowerShell használatáról: a [Azure PowerShell használata][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI az Azure-szolgáltatások, például az Azure websites és az Azure Virtual Machines üzembe helyezésére és kezelésére szolgáló parancsok összessége. További információ az Azure CLI telepítéséről: [Az Azure CLI telepítése](cli-install-nodejs.md).

## <a name="next-steps"></a>További lépések

További információ: [php fejlesztői központ](https://azure.microsoft.com/develop/php/).

[install-php]: https://www.php.net/manual/en/install.php
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
