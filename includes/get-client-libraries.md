---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
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
