---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133276"
---
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz, amely tartalmazza az összes paraméter értékét nevű paraméterek szakaszban.
Meg kell határozni egy paramétere ezeket az értékeket, amelyek alapján a projekt telepítésekor, vagy telepíti, akkor a környezet alapján változhatnak. Nem határoznak meg paramétereket olyan értékhez, amely mindig érintetlen marad. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

Paramétereket meghatározásakor használhatja a **allowedValues** mezőben adja meg, mely a felhasználó értékeket biztosíthat az üzembe helyezés során. Használja a **defaultValue** mezőt értéket rendel a paramétert, ha a nem érték van megadva üzembe helyezés során.

Azt ismerteti, hogy egyes paramétereket a sablonban.

### <a name="sitename"></a>Webhely neve:
A létrehozni kívánt webalkalmazás nevére.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
A webalkalmazás üzemeltetéséhez használt App Service-csomag neve.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Tarifacsomag kiválasztása a szolgáltatási csomag számára.

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

A sablon az értékeket, amelyeknél engedélyezve van ez a paraméter határozza meg, és hozzárendeli egy alapértelmezett értéket (S1 esetén), ha nem ad meg értéket.

### <a name="workersize"></a>workerSize
A példány mérete a szolgáltatási csomag (kicsi, közepes vagy nagy).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

A sablon meghatározza az értékeket, amelyeknél engedélyezve van ez a paraméter (0, 1 vagy 2) a, és hozzárendeli az alapértelmezett érték (0), ha nem ad meg értéket. Az értékek milyen összefüggésben vannak kicsi, közepes és nagy méretű.

