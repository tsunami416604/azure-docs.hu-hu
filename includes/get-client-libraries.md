---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171273"
---
### <a name="install-via-composer"></a>Composer-n keresztül telepítése
1. Hozzon létre egy fájlt **composer.json** a projekt gyökérkönyvtárában található és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Töltse le **[composer.phar] [ composer-phar]** a projekt gyökérkönyvtárában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökérkönyvtárában
   
    ```
    php composer.phar install
    ```

Másik lehetőségként lépjen a [Azure Storage PHP ügyféloldali kódtár] [ php-sdk-github] klónozásához a forráskód a githubon.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
