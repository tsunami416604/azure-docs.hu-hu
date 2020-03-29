---
title: Tárfiók névhibáival kapcsolatos hibák
description: Ez a témakör a tárfiók nevének megadásakor előforduló hibákat ismerteti.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153972"
---
# <a name="resolve-errors-for-storage-account-names"></a>A tárfióknevek hibáinak elhárítása

Ez a cikk a tárfiók telepítésekor előforduló elnevezési hibákat ismerteti.

## <a name="symptom"></a>Hibajelenség

Ha a tárfiók neve tiltott karaktereket tartalmaz, olyan hibaüzenetet kap, mint például:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

A tárfiókok, meg kell adnia egy nevet az erőforrás, amely egyedi az Azure-ban. Ha nem egyedi nevet ad meg, ilyen hibaüzenet jelenik meg:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Ha üzembe helyez egy tárfiókot az előfizetésben egy meglévő tárfiók nevével, de más helyet ad meg, hibaüzenetet kap, amely azt jelzi, hogy a tárfiók már létezik egy másik helyen. Törölje a meglévő tárfiókot, vagy adja meg ugyanazt a helyet, mint a meglévő tárfiók.

## <a name="cause"></a>Ok

A tárfióknevek nek 3 és 24 karakter közötti hosszúságúnak kell lenniük, és csak számokat és kisbetűket használhatnak. A névnek egyedinek kell lennie.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a tárfiók neve egyedi. Egyedi nevet hozhat létre az egyedi [karakterlánc-függvény](template-functions-string.md#uniquestring) eredményével való összefűzésével.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Győződjön meg arról, hogy a tárfiók neve nem haladja meg a 24 karaktert. Az [uniqueString](template-functions-string.md#uniquestring) függvény 13 karaktert ad vissza. Ha összefűz egy előtagot vagy postfixet az **uniqueString** eredményéhez, adjon meg egy legkisebb karakterértékű értéket.

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

Győződjön meg arról, hogy a tárfiók neve nem tartalmaz nagybetűs vagy speciális karaktereket.