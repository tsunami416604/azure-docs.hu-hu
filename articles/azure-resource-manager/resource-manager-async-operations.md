---
title: Aszinkron műveletek – Azure Resource Manager állapota
description: Ismerteti, hogyan lehet az Azure-beli aszinkron műveletek követése. Megjelenít egy hosszú ideig futó művelet állapotának lekéréséhez használt értékek.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1b05ed50f08ddbf2eb5da8e08f5bf623596e1f9b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135457"
---
# <a name="track-asynchronous-azure-operations"></a>Az Azure aszinkron műveletek követése
Néhány Azure REST-művelet aszinkron módon futtatható, mert a művelet nem hajtható végre gyorsan. Ez a cikk ismerteti, a válaszban visszaadott értékek aszinkron műveletek állapotának nyomon követését.  

## <a name="status-codes-for-asynchronous-operations"></a>Az aszinkron műveletek állapotkódok
Egy aszinkron művelet kezdetben vagy a HTTP-állapotkódot adja vissza:

* 201 (Created)
* 202 (Accepted) 

Ha a művelet sikeresen befejeződött, függvény vagy:

* 200 (OK)
* 204 (nincs tartalom) 

Tekintse meg a [REST API-dokumentáció](/rest/api/) megtekintheti a válaszokat a művelethez használt végrehajtása.

## <a name="monitor-status-of-operation"></a>Művelet állapotának figyelése
Az aszinkron REST-műveletek fejléc értékek, amelyek segítségével állapítja meg, a művelet állapotát adja vissza. Nincsenek potenciálisan három fejlécértékeket vizsgálata:

* `Azure-AsyncOperation` -URL az a művelet állapotának folyamatos ellenőrzéséhez. Ha a művelet ezt az értéket ad vissza, mindig (hely) és nem nyomon követhető vele, a művelet állapotát.
* `Location` -URL-cím meghatározásához, amikor befejeződik a művelet. Ez az érték csak akkor használja az Azure-AsyncOperation nem adják vissza.
* `Retry-After` – Az aszinkron művelet állapotának ellenőrzésével időszakát (másodpercben) a száma.

Nem minden aszinkron művelet azonban ezeket az értékeket adja vissza. Például előfordulhat, hogy kell kiértékelni egy művelet az Azure-AsyncOperation fejlécet értékét, és a hely Fejlécérték egy másik művelet. 

Kérheti le a fejléc értékek szerint kérheti le egy kérelem minden fejléc értéke lenne. Például a C#, kérheti a Fejlécérték egy `HttpWebResponse` nevű objektum `response` a következő kóddal:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Az Azure-AsyncOperation kérések és válaszok

Az aszinkron művelet állapotának lekéréséhez, hogy az URL-cím az Azure-AsyncOperation fejlécet érték egy GET kérés küldése.

Ez a művelet a válasz törzse a művelettel kapcsolatos adatokat tartalmaz. Az alábbi példa bemutatja a művelet által visszaadott a lehetséges értékek:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Csak `status` az összes választ adja vissza. A hiba objektum van adja vissza, ha az állapot sikertelen vagy megszakítva. Minden más érték megadása nem kötelező; ezért a választ kap, mint a példa láthatótól.

## <a name="provisioningstate-values"></a>provisioningState értékek

Műveletek létrehozása, frissítése vagy törlése (PUT, PATCH, DELETE) erőforrás általában visszaadása egy `provisioningState` értéket. Művelet befejezése után az alábbi értékek egyike érkezett: 

* Sikeres
* Meghiúsult
* Megszakítva

Minden egyéb értékek azt jelzik, hogy továbbra is fut a műveletet. Az erőforrás-szolgáltató egy testre szabott értéket, amely azt jelzi, hogy állapotában adhat vissza. Például előfordulhat, hogy kap **elfogadva** fogadott és fut a kérelem esetén.

## <a name="example-requests-and-responses"></a>Például kérelmek és válaszok

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Indítsa el a virtuális gép (az Azure-AsyncOperation 202)
Ez a példa bemutatja, hogyan állapotának megállapítása **start** műveletet a virtuális gépekhez. A kezdeti kérelmet a következő formátumban kell megadni:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Állapotkód: 202 adja vissza. A fejléc értékek közül jelenik meg:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Az aszinkron művelet, egy másik kérést küld az adott URL-cím állapotának ellenőrzéséhez.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

A válasz törzse tartalmazza a művelet állapotát:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>(Az Azure-AsyncOperation 201) erőforrások üzembe helyezése

Ez a példa bemutatja, hogyan állapotának megállapítása **központi telepítések** művelet üzembe erőforrásokat az Azure-bA. A kezdeti kérelmet a következő formátumban kell megadni:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Akkor a 201-es állapotkódot adja vissza. A válasz törzse tartalmazza:

```json
"provisioningState":"Accepted",
```

A fejléc értékek közül jelenik meg:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Az aszinkron művelet, egy másik kérést küld az adott URL-cím állapotának ellenőrzéséhez.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

A válasz törzse tartalmazza a művelet állapotát:

```json
{"status":"Running"}
```

Ha a telepítés befejeződött, a válasz tartalmazza:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Storage-fiók (a hely és Retry-After 202) létrehozása

Ez a példa bemutatja, hogyan állapotának megállapítása a **létrehozása** művelet tárfiókok esetében. A kezdeti kérelmet a következő formátumban kell megadni:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

És a kérés törzse tartalmazza a tárfiók tulajdonságait:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Állapotkód: 202 adja vissza. A fejléc értékek közül a következő két érték jelenik meg:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Miután Várakozás száma a másodpercben megadott Retry-After, ellenőrizze az aszinkron művelet állapotát egy másik kérést küld a megadott URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Ha a kérés továbbra is fut, kap egy állapotkód: 202. Ha a kérelem befejeződött, a 200-as állapotkódot kap, és a válasz törzse tartalmazza a létrehozott tárfiók tulajdonságait.

## <a name="next-steps"></a>További lépések

* Minden egyes REST művelet dokumentációjáért lásd: [REST API-dokumentáció](/rest/api/).
* a Resource Manager REST API-n keresztül sablonok telepítésével kapcsolatos információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonok és a Resource Manager REST API](resource-group-template-deploy-rest.md).