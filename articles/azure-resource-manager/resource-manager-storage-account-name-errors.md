---
title: Az Azure storage-fiók neve hibák |} A Microsoft Docs
description: Ismerteti a hibák jelentkezhetnek, ha a tárfiók nevének megadása.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: c3d4d764b1076c8705cfa64d6c0b38e3b8c1a801
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716389"
---
# <a name="resolve-errors-for-storage-account-names"></a>A tárfiók nevére ki a hibákat

Ez a cikk ismerteti a tárfiók üzembe helyezésekor elnevezési hibák.

## <a name="symptom"></a>Jelenség

Ha a tárfiók nevét a tiltott karaktereket tartalmaz, akkor megjelenik egy hibaüzenet, például:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Storage-fiókok esetében meg kell adnia egy nevet az erőforrás, amely egyedi Azure-ban. Ha nem ad meg egy egyedi nevet, akkor megjelenik egy hibaüzenet, például:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Ha üzembe helyezése a tárfiók neve megegyezik egy meglévő tárfiókot az előfizetésében, de egy másik helyen adja meg, hogy megjelenik egy hibaüzenet a tárfiók már létezik egy másik helyen található. Törölje a meglévő tárfiókot, vagy adja meg a meglévő storage-fiók ugyanazon a helyen.

## <a name="cause"></a>Ok

Tárfiókok nevének 3 – 24 karakter hosszúságúnak kell és számokból és kisbetűkből állhat csak. A nevének egyedinek kell lennie.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a tárfiók neve egyedi. Az elnevezési konvenciókba eredményével összefűzésével is létrehozhat egy egyedi nevet a [uniqueString](resource-group-template-functions-string.md#uniquestring) függvény.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Győződjön meg arról, hogy a tárfiók neve nem haladja meg a 24 karakternél. A [uniqueString](resource-group-template-functions-string.md#uniquestring) függvény 13 karaktert adja vissza. Ha összefűzése egy előtagot vagy utótaggal vannak ellátva a **uniqueString** vezethet, adjon meg egy értéket a 11 karaktert vagy kisebb.

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

Győződjön meg arról, hogy a tárfiók neve nem tartalmaz semmilyen nagybetűk és speciális karaktereket.