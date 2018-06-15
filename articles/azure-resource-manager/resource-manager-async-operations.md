---
title: Az Azure aszinkron műveletek |} Microsoft Docs
description: Nyomon követheti az Azure-ban aszinkron műveleteket ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: f62212f0488e4d1be49b419615b3a16b80033fd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358710"
---
# <a name="track-asynchronous-azure-operations"></a>Az Azure aszinkron műveletek nyomon követése
Néhány Azure REST művelet aszinkron módon futtatható, mert a művelet nem fejezhető be gyorsan. Ez a témakör ismerteti a válaszban visszaadott értékek keresztül aszinkron műveletek állapotának nyomon követését.  

## <a name="status-codes-for-asynchronous-operations"></a>Az aszinkron műveletek állapotkódjai
Egy aszinkron művelet kezdetben adja vissza egy HTTP-állapotkód: a következők:

* 201 (Created)
* 202 (Accepted) 

Ha a művelet sikeresen befejeződött, adja vissza, vagy:

* 200-AS (OK)
* 204 (üres) 

Tekintse meg a [REST API-dokumentáció](/rest/api/) tekintheti meg a műveletet, amelyek végrehajtása a válaszokat. 

## <a name="monitor-status-of-operation"></a>Művelet állapotának figyelése
Az aszinkron REST műveleteinek térjen vissza a fejléc értékei, amelyek segítségével állapítja meg, a művelet állapotát. Nincsenek potenciálisan három térközkaraktert vizsgálata:

* `Azure-AsyncOperation` -A művelet folyamatban lévő állapotának ellenőrzése a következő URL-címe A művelet ezt az értéket adja vissza, ha mindig segítségével (hely) helyett a művelet állapotának nyomon követését.
* `Location` -URL-cím meghatározásához, ha egy művelet befejeződött. Használja ezt az értéket csak akkor, ha Azure-aszinkron műveletek nem ad vissza.
* `Retry-After` -Hány másodpercig várjon az aszinkron művelet állapotának ellenőrzésekor.

Nem minden aszinkron művelethez azonban ezeket az értékeket adja vissza. Például szükség lehet az Azure-aszinkron műveletek Fejlécérték egy művelet, és a hely fejléc értékének egy másik művelet. 

A fejléc értékei Ön olvashatók be, mert a szeretné beolvasni a bármely állomásfejléc-érték, egy kérelemre vonatkozóan. Például a C#, visszaállíthatja a Fejlécérték egy `HttpWebResponse` nevű objektum `response` az alábbi kódra:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-aszinkron műveletek kérelem-válasz

Ahhoz, hogy az aszinkron művelet állapotát, GET kérés küldése az Azure-aszinkron műveletek állomásfejléc-érték az URL-címet.

Ez a művelet kapott válasz törzsében működésével kapcsolatos adatokat. A következő példa bemutatja a művelet által visszaadott a lehetséges értékek:

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

Csak `status` az összes választ ad vissza. A hiba objektum ad vissza, ha az állapot sikertelen vagy megszakítva. Minden más értékek a következők kötelező megadni. ezért a választ kap láthatótól tűnhet.

## <a name="provisioningstate-values"></a>provisioningState értékek

Műveletek létrehozása, frissítése vagy törlése (PUT, javítás, Törlés) erőforrás általában vissza egy `provisioningState` érték. Egy művelet befejezése után az alábbi értékek egyike vissza: 

* Sikeres
* Meghiúsult
* Törölve

Minden egyéb értékek azt jelzik, hogy még mindig fut, a műveletet. Az erőforrás-szolgáltató az állapotát jelző testreszabott értéket adhat vissza. Például jelenhet meg **elfogadott** fogadott és megfelelően fut a kérelem esetén.

## <a name="example-requests-and-responses"></a>Példa kérések és válaszok

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Indítsa el a virtuális gép (az Azure-aszinkron műveletek 202)
Ez a példa bemutatja, hogyan állapotának megállapítása **start** műveletet a virtuális gépek. A kezdeti kérelme, mert a következő formátumban:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Állapotkód: 202 adja vissza. A fejléc értékei között jelenik meg:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Az aszinkron művelet, egy másik kérelem küldése adott URL-cím állapotának ellenőrzéséhez.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Az adott válasz törzsének tartalmaz a művelet állapotát:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Erőforrások (az Azure-aszinkron műveletek 201) telepítése

Ez a példa bemutatja, hogyan állapotának megállapítása **központi telepítések** művelet erőforrásokat üzembe helyezi az Azure-bA. A kezdeti kérelme, mert a következő formátumban:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

201-es állapotkód adja vissza. A választörzs tartalmazza:

```json
"provisioningState":"Accepted",
```

A fejléc értékei között jelenik meg:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Az aszinkron művelet, egy másik kérelem küldése adott URL-cím állapotának ellenőrzéséhez.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Az adott válasz törzsének tartalmaz a művelet állapotát:

```json
{"status":"Running"}
```

Ha a telepítés befejeződött, a válasz tartalmazza:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>(A hely és újrapróbálkozási után 202) storage-fiók létrehozása

Ez a példa bemutatja, hogyan állapotának megállapítása a **létrehozása** storage-fiókok műveletet. A kezdeti kérelme, mert a következő formátumban:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

És a kérés törzsében tartalmazza a tárfiók tulajdonságai:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Állapotkód: 202 adja vissza. A fejléc értékei között a következő két érték jelenik meg:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Miután Várakozás száma a másodpercben megadott újrapróbálkozási után ellenőrizze az aszinkron művelet állapotát úgy, hogy küld egy másik kérelem URL-címet.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Ha a kérés továbbra is fut, a állapotkód: 202 jelenik meg. Ha a kérelem befejeződött, a 200-as állapotkód kap, és a választörzs a tárfiók már létrehozott tulajdonságait tartalmazza.

## <a name="next-steps"></a>További lépések

* Az egyes REST műveletekre vonatkozó dokumentációjáért lásd: [REST API-dokumentáció](/rest/api/).
* a Resource Manager REST API-n keresztül sablonok telepítésével kapcsolatos információkért lásd: [központi telepítése a Resource Manager-sablonok és a Resource Manager REST API erőforrások](resource-group-template-deploy-rest.md).