---
title: Aszinkron műveletek állapota
description: Bemutatja, hogyan követhető az aszinkron műveletek az Azure-ban. A hosszú ideig futó művelet állapotának lefelvételéhez használt értékeket jeleníti meg.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485441"
---
# <a name="track-asynchronous-azure-operations"></a>Aszinkron Azure-műveletek nyomon követése
Egyes Azure REST-műveletek aszinkron módon futnak, mert a művelet nem hajtható végre gyorsan. Ez a cikk azt ismerteti, hogyan követheti nyomon az aszinkron műveletek állapotát a válaszban visszaadott értékeken keresztül.  

## <a name="status-codes-for-asynchronous-operations"></a>Állapotkódok az aszinkron műveletekhez
Az aszinkron művelet kezdetben a következő HTTP-állapotkódot adja vissza:

* 201 (létrehozva)
* 202 (Elfogadva) 

Ha a művelet sikeresen befejeződik, a következő értéket adja vissza:

* 200 (OK)
* 204 (Nincs tartalom) 

Tekintse meg a [REST API dokumentációját](/rest/api/) a végrehajtandó műveletre adott válaszok megtekintéséhez.

## <a name="monitor-status-of-operation"></a>A működés állapotának figyelése
Az aszinkron REST-műveletek fejlécértékeket adnak vissza, amelyek segítségével meghatározhatja a művelet állapotát. A következő tetszésszerint három fejlécértéket kell megvizsgálni:

* `Azure-AsyncOperation`- URL a művelet folyamatos állapotának ellenőrzéséhez. Ha a művelet ezt az értéket adja vissza, mindig használja azt (a Hely helyett) a művelet állapotának nyomon követéséhez.
* `Location`- URL a művelet befejezésének meghatározásához. Ezt az értéket csak akkor használja, ha az Azure-AsyncOperation nem adja vissza.
* `Retry-After`- Az aszinkron művelet állapotának ellenőrzése előtt várjon másodperceket.

Azonban nem minden aszinkron művelet adja vissza ezeket az értékeket. Előfordulhat például, hogy ki kell értékelnie az Azure-AsyncOperation fejlécértékét egy művelethez, és a Hely fejléc értékét egy másik művelethez. 

A fejlécértékeket úgy lehet beolvasni, ahogy a kérés bármely fejlécértékét. A C#-ban például a fejlécértékét `HttpWebResponse` a `response` következő kóddal elnevezett objektumból lehet beolvasni:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation kérés és válasz

Az aszinkron művelet állapotának lekéréséhez küldjön get-kérelmet az Azure-AsyncOperation fejlécértékében lévő URL-címre.

A műveletből származó válasz törzse a műveletről tartalmaz információkat. A következő példa a műveletből visszaadott lehetséges értékeket mutatja be:

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

Csak `status` az összes válaszhoz kerül visszaadásra. A hibaobjektum akkor jelenik meg, ha az állapot sikertelen vagy megszakítva. Az összes többi érték nem kötelező; ezért a kapott válasz máshogy nézhet ki, mint a példa.

## <a name="provisioningstate-values"></a>provisioningState értékek

Olyan műveletek, amelyek erőforrást hoznak létre, frissítnek vagy törölnek (PUT, PATCH, DELETE), általában `provisioningState` értéket adnak vissza. Ha egy művelet befejeződött, a következő három érték egyikét adja vissza: 

* Sikeres
* Sikertelen
* Megszakítva

Az összes többi érték azt jelzi, hogy a művelet még fut. Az erőforrás-szolgáltató visszaadhat egy testreszabott értéket, amely jelzi az állapotát. Például előfordulhat, hogy a kérelem fogadásakor és futtatásakor megkapja az **Elfogadva.**

## <a name="example-requests-and-responses"></a>Példa kérések és válaszok

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuális gép indítása (202 az Azure-AsyncOperation művelettel)
Ez a példa bemutatja, hogyan határozhatja meg a virtuális gépek **indítási** művelet állapotát. Az első kérelem formátuma a következő:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

202-es állapotkódot ad vissza. A fejlécértékek között a következők láthatók:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Az aszinkron művelet állapotának ellenőrzéséhez küldjön egy másik kérést az adott URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

A választörzs a művelet állapotát tartalmazza:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Erőforrások üzembe helyezése (201 az Azure-AsyncOperation művelettel)

Ez a példa bemutatja, hogyan határozhatja meg az erőforrások Azure-ba történő üzembe helyezéséhez üzembe **helyezési** művelet állapotát. Az első kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

201-es állapotkódot ad vissza. A válasz törzse a következőket tartalmazza:

```json
"provisioningState":"Accepted",
```

A fejlécértékek között a következők láthatók:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Az aszinkron művelet állapotának ellenőrzéséhez küldjön egy másik kérést az adott URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

A választörzs a művelet állapotát tartalmazza:

```json
{"status":"Running"}
```

A telepítés befejezése után a válasz a következőket tartalmazza:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Tárfiók létrehozása (202 hely és újrapróbálkozás után)

Ez a példa bemutatja, hogyan határozhatja meg a tárolási fiókok **létrehozási** műveletállapotát. Az első kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

És a kérelem törzse tartalmazza a tárfiók tulajdonságait:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

202-es állapotkódot ad vissza. A fejlécértékek között a következő két érték látható:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Miután megvárta az Újrapróbálkozás után megadott számú másodpercet, ellenőrizze az aszinkron művelet állapotát egy másik kérés elküldésével az adott URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Ha a kérelem még fut, 202-es állapotkódot kap. Ha a kérés befejeződött, a 200-as állapotkódot kap, és a válasz törzse tartalmazza a létrehozott tárfiók tulajdonságait.

## <a name="next-steps"></a>További lépések

* Az egyes REST-műveletekről a [REST API dokumentációjában](/rest/api/)olvashat dokumentációt.
* A sablonok Erőforrás-kezelő REST API-n keresztüli központi telepítéséről az [Erőforrások telepítése erőforrás-kezelői sablonokkal és az Erőforrás-kezelő REST API-jával című témakörben](../templates/deploy-rest.md)olvashat.