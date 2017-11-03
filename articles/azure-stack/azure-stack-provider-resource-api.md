---
title: "Szolgáltató Erőforrás kihasználtsága API |} Microsoft Docs"
description: "Az erőforrás-felhasználás API, amely Azure verem használati adatainak beolvasása referenciája"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b6055923-b6a6-45f0-8979-225b713150ae
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: alfredop
ms.openlocfilehash: 0c45ce3bc93945ed8700464beebabcda07e8d77c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="provider-resource-usage-api"></a>Szolgáltatói erőforrás-használati API
A kifejezés *szolgáltató* minden vonatkozik, a szolgáltatás-rendszergazda vagy olyan meghatalmazott szolgáltatót. Azure verem operátorok és delegált szolgáltatók használatával a szolgáltató használati API tekintse meg a közvetlen bérlőiknek. Például ahogy az ábrán is látható, P0 hívhatják meg a szolgáltató a P1 tartozó használati adatainak megszerzése API, és P2 tartozó közvetlen használatát, és P1 P3 és P4 használati információt kérjen.

![A szolgáltató hierarchia fogalmi modellt](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API-hívás referencia
### <a name="request"></a>Kérés
A kérelem lekérdezi a kért előfizetések és a kért időkeretet használat részletei. Nem található kérelemtörzs van.

API használata egy szolgáltató API-t, így a hívó egy a szolgáltató előfizetésben tulajdonos, közreműködő vagy olvasó szerepkört kell hozzárendelni.

| **Módszer** | **Kérelem URI-azonosítója** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}& subscriberId = {sub1.1} & api-version = 2015-06-01. dátumú előnézeti & continuationToken = {jogkivonat-value} |

### <a name="arguments"></a>Argumentumok
| **Argumentum** | **Leírás** |
| --- | --- |
| *armendpoint* |Az Azure Resource Manager végpont Azure verem környezet. Az Azure-verem egyezmény, hogy az Azure Resource Manager-végpont neve nem formátumú `https://adminmanagement.{domain-name}`. Például a csomag, ha a tartománynév *local.azurestack.external*, akkor a Resource Manager-végpont esetében `https://adminmanagement.local.azurestack.external`. |
| *subId* |Előfizetés-azonosítója a felhasználó, aki a hívást. |
| *reportedStartTime* |Kezdési időpontja a lekérdezést. A következő *DateTime* az egyezményes világidő (UTC) és a például 13:00 óra elején kell lennie. A napi aggregáció UTC éjfél érték beállítása. A formátum *escape-karaktersorozatot* ISO 8601. Például *2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z*, ahol a kettőspont escape-karakterrel megjelölve a *% 3a* és a plusz escape-karakterrel megjelölve a *% 2b* úgy, hogy rövid URI. |
| *reportedEndTime* |Befejezési időpontja a lekérdezést. A vonatkozó megkötések *reportedStartTime* ezt az argumentumot is vonatkozik. A következő *reportedEndTime* értéke nem lehet a jövőben vagy az aktuális dátumot. Ha igen, az eredmény értéke "feldolgozása nem teljes." |
| *aggregationGranularity* |Nem kötelező paraméter, amely két különálló lehetséges értékei: napi vagy óránkénti. Javasolt az értékeket, mint egy napi részletességű összegzése a adatokat adja vissza, a másik egy óránkénti megoldás. A napi beállítás az alapértelmezett beállítás. |
| *subscriberId* |Előfizetés-azonosító. Szűrt adatok lekérése, a szolgáltató közvetlen bérlő előfizetés-azonosító megadása kötelező. Ha nincs előfizetés-azonosító paraméter van megadva, a hívás használati adatokat a szolgáltató közvetlen bérlők adja vissza. |
| *API-verzió* |Az ilyen kérést használt protokoll verziója. Ez a beállítás értéke *2015 06-01. dátumú előnézeti*. |
| *continuationToken* |A használati API szolgáltató legutóbbi hívásának jogkivonatot beolvasni. Ez a token van szükség, ha egy válasz érték nagyobb, mint 1000 sorok, és úgy működik, mint egy könyvjelzőt folyamatához. Ha a jogkivonat nincs megadva, a nap kezdetén az adatok lekérésére, vagy átadott óra, a részletesség alapján. |

### <a name="response"></a>Válasz
/Subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 és reportedEndTime LEKÉRÉSE = 2015-06-01T00 % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity = napi & subscriberId sub1.1 & api-version = = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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
| *azonosítója* |A használati összesítés egyedi azonosítója. |
| *név* |A használati összesítés neve. |
| *típusa* |Erőforrás-definícióban. |
| *előfizetés-azonosító* |Előfizetés-azonosító az Azure-verem felhasználó. |
| *usageStartTime* |UTC kezdési időpontja a használati gyűjtő, amelyhez az használati összesítés tartozik.|
| *usageEndTime* |Befejezési időpontja UTC a használati gyűjtő, amelyhez az használati összesítés tartozik. |
| *instanceData* |Kulcs-érték pár (új formátumban) példány részletei:<br> *resourceUri*: teljesen minősített az erőforrás-azonosító, amely tartalmazza az erőforráscsoportok és a példány nevét. <br> *hely*: régióban, amelyben ez a szolgáltatás futtatták. <br> *címkék*: a felhasználó által megadott erőforrás-címkéket. <br> *additionalinfo részben*: részletesebben az erőforrást a felhasznált, például az operációs rendszer verziója vagy a kép típusa. |
| *mennyiség* |Ez időkereten belül történt hálózatierőforrás-fogyasztás mennyisége. |
| *meterId* |Az erőforrás, a felhasznált egyedi azonosítója (más néven *ResourceID*). |

## <a name="next-steps"></a>Következő lépések
[Bérlői API-referencia erőforrás-használat](azure-stack-tenant-resource-usage-api.md)

[Használati kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)
