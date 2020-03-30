---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178927"
---
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy Parameters nevű szakaszt, amely az összes paraméterértéket tartalmazza.
Meg kell határoznia egy paramétert az értékekhez, amely a telepített projekttől vagy a környezettől függően változik. Ne adjon meg paramétereket olyan értékekhez, amelyek mindig változatlanok maradnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

Paraméterek definiálásakor a **allowedValues** mező segítségével adja meg, hogy a felhasználó milyen értékeket adhat meg a telepítés során. A **defaultValue** mező segítségével rendeljen értéket a paraméterhez, ha a telepítés során nincs megadva érték.

A sablonban minden paramétert ismertetünk.

### <a name="sitename"></a>siteName
A létrehozni kívánt webalkalmazás neve.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
A webalkalmazás üzemeltetéséhez használandó App Service-csomag neve.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>Sku
A tárhelycsomag árképzési szintje.

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

A sablon meghatározza a paraméterhez engedélyezett értékeket, és ha nincs megadva érték, alapértelmezett értéket (S1) rendel hozzá.

### <a name="workersize"></a>workerSize (dolgozóméret)
A tárhelycsomag példánymérete (kicsi, közepes vagy nagy).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

A sablon meghatározza a paraméterhez engedélyezett értékeket (0, 1 vagy 2), és alapértelmezett értéket (0) rendel hozzá, ha nincs megadva érték. Az értékek kis, közepes és nagy értékeknek felelnek meg.

