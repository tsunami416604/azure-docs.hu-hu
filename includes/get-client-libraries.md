---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474287"
---
### <a name="install-via-composer"></a>Telepítés zeneszerzőn keresztül
1. Hozzon létre egy **composer.json** nevű fájlt a projekt gyökerében, és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Töltse le **[a composer.phar fájlt][composer-phar]** a projekt gyökérfájljában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökér
   
    ```
    php composer.phar install
    ```

Másik lehetőségként nyissa meg az [Azure Storage PHP ügyfélkönyvtárát][php-sdk-github] a GitHubon a forráskód klónozásához.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
