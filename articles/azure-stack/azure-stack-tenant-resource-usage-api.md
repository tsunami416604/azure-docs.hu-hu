---
title: "A bérlői API-erőforrás-használat |} Microsoft Docs"
description: "Referencia erőforrás-használat API, amely beolvassa az Azure-verem használati adatait."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: bc0b9993119342f07c28ed0384c11ae0f15bc439
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="tenant-resource-usage-api"></a>A bérlői API-erőforrás-használat

A bérlő a bérlői API segítségével megtekintheti a bérlői saját erőforrás-használati adatok. Ez az API konzisztensek legyenek az Azure használati API (jelenleg magán előnézetben).

A Windows PowerShell-parancsmag **Get-UsageAggregates** megszerezni a használati adatok, például az Azure-ban.

## <a name="api-call"></a>Az API-hívás
### <a name="request"></a>Kérés
A kérelem lekérdezi a kért előfizetések és a kért időkeretet használat részletei. Nem található kérelemtörzs van.

| **Módszer** | **Kérelem URI-azonosítója** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumentumok
| **Argumentum** | **Leírás** |
| --- | --- |
| *armendpoint* |Az Azure Resource Manager végpont Azure verem környezet. Az Azure-verem egyezmény, hogy az Azure Resource Manager-végpont neve nem formátumú `https://management.{domain-name}`. Például a csomag, a tartománynév local.azurestack.external, akkor a Resource Manager-végpont esetében `https://management.local.azurestack.external`. |
| *subId* |A felhasználó, aki a hívás előfizetés-azonosítója. Ez az API csak a lekérdezés egyetlen előfizetéssel használati is használhatja. Szolgáltatók használhatja a szolgáltató Erőforrás kihasználtsága API-t lekérdezés használatra az összes bérlőre vonatkozó. |
| *reportedStartTime* |Kezdési időpontja a lekérdezést. A következő *dátum és idő* UTC szerint, és a például 13:00 óra elején kell lennie. A napi aggregáció UTC éjfél érték beállítása. A formátum *escape-karakterrel megjelölve* ISO 8601, például 2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z, ahol kettőspont escape-karakterrel megjelölve a % 3a és plus escape-karakterrel megjelölve % 2b számára, hogy az URI rövid. |
| *reportedEndTime* |Befejezési időpontja a lekérdezést. A vonatkozó megkötések *reportedStartTime* ezt az argumentumot is vonatkozik. A következő *reportedEndTime* a jövőben nem lehet. |
| *aggregationGranularity* |Nem kötelező paraméter, amely két különálló lehetséges értékei: napi vagy óránkénti. Javasolt az értékeket, mint egy napi részletességű összegzése a adatokat adja vissza, a másik egy óránkénti megoldás. A napi beállítás az alapértelmezett beállítás. |
| *api-version* |Az ilyen kérést használt protokoll verziója. 2015 06-01. dátumú előnézeti kell használnia. |
| *continuationToken* |A használati API szolgáltató legutóbbi hívásának jogkivonatot beolvasni. Ez a token van szükség, ha egy válasz érték nagyobb, mint 1000 sorok, és úgy működik, mint egy könyvjelzőt folyamatához. Ha nem található, a nap kezdetén az adatok lekérésére, vagy átadott óra, a részletesség alapján. |

### <a name="response"></a>Válasz
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Válasz adatai
| **Argumentum** | **Leírás** |
| --- | --- |
| *id* |A használati összesítés egyedi azonosítója |
| *name* |A használati összesítés neve |
| *Típusa* |Az erőforrás-definíció |
| *subscriptionId* |A felhasználó Azure előfizetés-azonosító |
| *usageStartTime* |A használati gyűjtő, amelyhez az használati összesítés tartozik UTC kezdete |
| *usageEndTime* |A használati gyűjtő, amelyhez az használati összesítés tartozik befejezési időpontja UTC szerint |
| *instanceData* |Kulcs-érték pár (új formátumban) példány részletei:<br>  *resourceUri*: teljesen minősített az erőforrás-azonosító, beleértve a csoportok és a példány neve <br>  *hely*: régióban, amelyben ez a szolgáltatás futtatása <br>  *címkék*: a felhasználó által az erőforráscímkék <br>  *additionalinfo részben*: további részleteket a felhasznált, például az erőforrás operációs rendszerének verziója vagy a kép típusa |
| *mennyiség* |Az erőforrás-felhasználás a időkereten belül történt összeg |
| *meterId* |Az erőforrás, a felhasznált egyedi azonosítója (más néven *ResourceID*) |


## <a name="next-steps"></a>További lépések
[Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)

[Használati kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)

