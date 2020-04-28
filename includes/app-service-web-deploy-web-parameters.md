---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67178927"
---
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy paramétert, amely az összes paraméter értékét tartalmazza.
Meg kell határoznia a paramétert azokhoz az értékekhez, amelyek a telepített projekttől függően változnak, vagy amely az Ön által telepített környezet alapján változik. Ne definiáljon paramétereket olyan értékekhez, amelyek mindig azonosak maradnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

A paraméterek meghatározásakor a **allowedValues** mezőben adhatja meg, hogy a felhasználó milyen értékeket biztosíthat az üzembe helyezés során. Ha a telepítés során nem ad meg értéket, a **defaultValue** mező használatával rendeljen hozzá egy értéket a paraméterhez.

A sablon minden paraméterét le fogjuk írni.

### <a name="sitename"></a>siteName
A létrehozni kívánt webalkalmazás neve.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
A webalkalmazás üzemeltetéséhez használni kívánt App Service-csomag neve.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU
A üzemeltetési csomag díjszabási szintje.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

A sablon meghatározza a paraméter számára engedélyezett értékeket, és az alapértelmezett értéket (S1) rendeli hozzá, ha nincs megadva érték.

### <a name="workersize"></a>workerSize
A üzemeltetési csomag példányának mérete (kis, közepes vagy nagy).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

A sablon meghatározza a paraméter számára engedélyezett értékeket (0, 1 vagy 2), és az alapértelmezett értéket (0) rendeli hozzá, ha nincs megadva érték. Az értékek kis, közepes és nagy értéknek felelnek meg.

