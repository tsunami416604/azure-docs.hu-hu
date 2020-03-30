---
title: Szabályozási hibák elhárítása az Azure-ban | Microsoft dokumentumok
description: Szabályozási hibák, újrapróbálkozások és visszalépés az Azure Compute-ban.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: f5fbd80fc9a8e519cf8f49ab16d7e747c6a8171b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045360"
---
# <a name="troubleshooting-api-throttling-errors"></a>API-szabályozási hibák elhárítása 

Az Azure Compute-kérelmek et előfizetéssel és régiónként szabályozhatja a szolgáltatás általános teljesítményének elősegítése érdekében. Biztosítjuk, hogy az Azure Compute Resource Provider (CRP) összes hívása, amely a Microsoft.Compute névtér ben kezeli az erőforrásokat, ne haladja meg a maximálisan engedélyezett API-kérelmek arányát. Ez a dokumentum ismerteti az API-szabályozás, a szabályozással kapcsolatos problémák elhárításának részleteit, valamint a szabályozás elkerülésére ajánlott eljárásokat.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Szabályozás az Azure Resource Manager és az erőforrás-szolgáltatók szerint  

Az Azure Resource Manager az Azure Resource Manager az Azure Resource Manager az összes bejövő API-kérelem hitelesítését és első rendelési érvényesítését és szabályozását végzi. Az Azure Resource Manager hívássebesség-korlátozási és a kapcsolódó diagnosztikai válasz HTTP-fejlécei [itt](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling)találhatók.
 
Ha egy Azure API-ügyfél szabályozási hibát kap, a HTTP állapota 429 Túl sok kérelem. Ha tudni szeretné, hogy a kérelem szabályozását az Azure Resource Manager vagy `x-ms-ratelimit-remaining-subscription-reads` egy mögöttes `x-ms-ratelimit-remaining-subscription-writes` erőforrás-szolgáltató, például a CRP végzi-e, ellenőrizze a GET-kérelmek és válaszfejlécek nem GET-kérelmeket. Ha a fennmaradó hívásszám megközelíti a 0-t, az előfizetés általános híváskorlátja az Azure Resource Manager által meghatározott elérte. Az összes előfizetési ügyfél tevékenységei tössze számítanak. Ellenkező esetben a szabályozás a célerőforrás-szolgáltatótól érkezik (a `/providers/<RP>` kérelem URL-címének szegmense által címzett). 

## <a name="call-rate-informational-response-headers"></a>Hívásdíj-információs válaszfejlécek 

| Fejléc                            | Értékformátum                           | Példa                               | Leírás                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-erőforrás |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Az erőforrásgyűjtőt vagy műveletcsoportot lefedő szabályozási szabályzat fennmaradó API-hívásszáma, beleértve a kérelem célját is                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | A HTTP-kérelemért a vonatkozó szabályzat korlátjára vonatkozó hívásszámok száma "felszámítva". Ez a legjellemzőbb 1. A kötegelt kérelmek, például a virtuálisgép-méretezési csoport méretezése több számot is felszámíthatnak. |


Vegye figyelembe, hogy egy API-kérelem több sávszélesség-szabályozási szabályzatok. Minden házirendhez `x-ms-ratelimit-remaining-resource` külön fejléc lesz. 

Itt van egy minta válasz a virtuálisgép-méretezési csoport kérésének törlésére.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Szabályozási hiba részletei

A 429-es HTTP-állapotot általában a kérés elutasítására használják, mert elérte a hívássebesség-korlátot. A számítási erőforrás-szolgáltató tipikus szabályozási hibaválasza az alábbi példához hasonlóan fog kinézni (csak a releváns fejlécek jelennek meg):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

A házirend fennmaradó hívásszáma 0 az, amely miatt a szabályozáshiba ad vissza. Ebben az esetben `HighCostGet30Min`ez a . A választörzs általános formátuma az általános Azure Resource Manager API-hibaformátum (amely megfelel az OData-nak). A fő hibakód , `OperationNotAllowed`az egyik számítási erőforrás-szolgáltató használja a szabályozási hibák (többek között az ügyfél hibák) jelentésére. A `message` belső hiba(ok) tulajdonsága egy szerializált JSON-struktúrát tartalmaz a szabályozásmegsértése részleteivel.

Amint azt a fentiekben is `Retry-After` látható, minden szabályozási hiba tartalmazza a fejlécet, amely megadja azt a minimális számú másodpercet, amelyet az ügyfélnek várnia kell a kérelem újbóli megkísérlése előtt. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API-hívási sebesség és szabályozási hibaelemző
A hibaelhárítási szolgáltatás előzetes verziója érhető el a számítási erőforrás-szolgáltató API-jához. Ezek a PowerShell-parancsmagok az API-kérelmek sebességére vonatkozó statisztikákat biztosítanak műveletenkénti időintervallumonként és a szabályzatonkénti szabályozásmegsértésekről:
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Az API-hívási statisztikák nagyszerű betekintést nyújtanak az előfizetés ügyfél(ek) viselkedésébe, és lehetővé teszik a szabályozást okozó hívásminták egyszerű azonosítását.

Az analizátor egyelőre korlátozza, hogy nem számolja a lemez- és pillanatkép-erőforrástípusokra vonatkozó kérelmeket (a felügyelt lemezek támogatása érdekében). Mivel adatokat gyűjt a CRP telemetriai adataiból, nem tud segíteni az ARM szabályozási hibáinak azonosításában is. De ezek könnyen azonosíthatók a megkülönböztető ARM válasz fejlécek alapján, amint azt korábban tárgyaltuk.

A PowerShell-parancsmagok egy REST-szolgáltatás API-t használnak, amelyet az ügyfelek könnyen meghívhatnak (bár hivatalos támogatás nélkül). A HTTP-kérelem formátumának megtekintéséhez futtassa a parancsmagokat a -Debug kapcsolóval, vagy olvassa le a végrehajtásukat a Fiddler segítségével.


## <a name="best-practices"></a>Ajánlott eljárások 

- Ne próbálkozzon újra az Azure Service API-hibáival feltétel nélkül és/vagy azonnal. Gyakori előfordulása az ügyfélkód bekerülni egy gyors újrapróbálkozási ciklus, ha olyan hibába ütközik, amely nem újrapróbálkozásra képes. Az újrapróbálkozások végül kimerítik a célművelet csoportjának engedélyezett híváskorlátját, és hatással vannak az előfizetés többi ügyfeleire. 
- A nagy mennyiségű API-automatizálási esetekben fontolja meg a proaktív ügyféloldali önszabályozás t, ha a célműveletcsoport rendelkezésre álló hívásszáma egy alacsony küszöbérték alá csökken. 
- Az aszinkron műveletek nyomon követésekor tartsa tiszteletben az Újrapróbálkozás utáni fejléc-tippeket. 
- Ha az ügyfélkódnak információra van szüksége egy adott virtuális gépről, a virtuális gépet közvetlenül kérdezze le ahelyett, hogy az összes virtuális gépet felsorolná az erőforráscsoportot tartalmazó erőforráscsoportban vagy a teljes előfizetésben, majd kiválasztaná a szükséges virtuális gépet az ügyféloldalon. 
- Ha az ügyfélkódnak virtuális gépekre, lemezekre és pillanatképekre van szüksége egy adott Azure-helyről, használja a lekérdezés helyalapú `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` űrlapját az összes előfizetéses virtuális gép lekérdezése helyett, majd az ügyféloldalon lévő hely szerinti szűrést: lekérdezés a számítási erőforrás-szolgáltató regionális végpontjaira. 
-   Különösen az API-erőforrások, különösen a virtuális gépek és a virtuálisgép-méretezési készletek létrehozásakor vagy frissítésekor sokkal hatékonyabb a `provisioningState`visszaadott aszinkron művelet befejezése, mint maga az erőforrás URL-je lekérdezése (a).

## <a name="next-steps"></a>További lépések

Az Azure-beli egyéb szolgáltatások újrapróbálkozási útmutatójával kapcsolatos további információkért olvassa [el az Újrapróbálkozási útmutató t adott szolgáltatásokhoz című témakört.](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
