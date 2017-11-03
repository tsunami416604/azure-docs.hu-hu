---
title: "Az Azure storage-fiók neve hibák |} Microsoft Docs"
description: "Ismerteti a hibák jelentkezhetnek, ha a tárfiók nevének megadása."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>Javítsa a hibákat a tárfiókok neve

Ez a cikk ismerteti az elnevezési hibák jelentkezhetnek, ha a tárfiók telepítése.

## <a name="symptom"></a>Jelenség

Ha a tárfiók nevének tiltott karaktereket tartalmaz, például hibaüzenetet kap:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Storage-fiókok meg kell adnia az egész Azure egyedi erőforrás nevét. Ha nem ad meg egy egyedi nevet, például hibaüzenetet kap:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Ha központi telepítése a tárfiók neve megegyezik egy meglévő tárfiók az előfizetésben, de adjon meg egy másik helyre, hibaüzenet arról, hogy a tárfiók már létezik egy másik helyen. Törölje a meglévő tárfiókot használ, vagy adja meg a meglévő tárfiókot és ugyanazon a helyen.

## <a name="cause"></a>Ok

Tárfiókok neve 3 – 24 karakter hosszúságúnak kell és számokat és kisbetűket tartalmazhatnak csak. A nevének egyedinek kell lennie.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1 megoldás

Győződjön meg arról, hogy a tárfiók neve egyedi. Az elnevezési eredményével összefűzésével létrehozhat egy egyedi nevet a [uniqueString](resource-group-template-functions-string.md#uniquestring) függvény.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>Megoldás 2

Győződjön meg arról, hogy a tárfiók neve legfeljebb 24 karakter hosszúságú. A [uniqueString](resource-group-template-functions-string.md#uniquestring) függvény 13 karaktert adja vissza. Ha előtag összefűzésére vagy utólagos javítás, hogy a **uniqueString** vezethet, adjon meg egy értéket a 11 karakter vagy kevesebb.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

### <a name="solution-3"></a>Megoldás 3

Győződjön meg arról, hogy a tárfiók neve nem tartalmaz minden nagybetűk- vagy speciális karaktereket.