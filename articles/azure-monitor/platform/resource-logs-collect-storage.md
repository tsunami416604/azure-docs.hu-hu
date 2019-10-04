---
title: Azure-beli erőforrás-naplók archiválása a Storage-fiókba | Microsoft Docs
description: Ismerje meg, hogyan archiválhatja Azure-beli erőforrás-naplóit a Storage-fiókok hosszú távú megőrzéséhez.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 8a1802f0f24ba5ccad3ec1c45f3baa29dfe6909f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262556"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure-beli erőforrás-naplók archiválása a Storage-fiókba
Az Azure-beli [erőforrás-naplók](resource-logs-overview.md) részletes és gyakori információkat biztosítanak az Azure-erőforrások belső működéséről. Ez a cikk az erőforrás-naplók Azure Storage-fiókba való gyűjtését ismerteti az adatok archiválás céljából történő megőrzése érdekében.

## <a name="prerequisites"></a>Előfeltételek
Ha még nem rendelkezik ilyennel, [létre kell hoznia egy Azure Storage-fiókot](../../storage/common/storage-quickstart-create-account.md) . A Storage-fióknak nem kell ugyanabban az előfizetésben lennie, mint az erőforrás-küldési naplók, feltéve, hogy a beállítást konfiguráló felhasználó mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik.

Ne használjon olyan meglévő Storage-fiókot, amely más, nem figyelési adattárolási információkkal rendelkezik, így hatékonyabban vezérelheti a figyeléshez való hozzáférést. Ha a [tevékenység naplóját](activity-logs-overview.md) is archiválja egy Storage-fiókba, akkor dönthet úgy, hogy ugyanazt a Storage-fiókot használja, hogy az összes figyelési adatmennyiséget egy központi helyen tárolja.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
A rendszer alapértelmezés szerint nem gyűjti az erőforrás-naplókat. Gyűjtsön egy Azure Storage-fiókban és más célhelyen egy Azure-erőforrás diagnosztikai beállításának létrehozásával. A részletekért lásd: [diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md) .


## <a name="data-retention"></a>Adatmegőrzés
Az adatmegőrzési házirend határozza meg, hogy hány nap elteltével kell megőrizni az adatokat az egyes naplók és a Storage-fiókokban tárolt metrikai adatok között. Az adatmegőrzési szabályzat 0 és 365 közötti tetszőleges számú nap lehet. A nulla adatmegőrzési szabály azt határozza meg, hogy az adott naplózási kategóriához tartozó események határozatlan ideig vannak tárolva.

Az adatmegőrzési szabályzatok naponta lesznek alkalmazva, így a nap végén (UTC) az adatmegőrzési szabályon túli naplókat törli a rendszer. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Erőforrás-naplók sémája a Storage-fiókban

Miután létrehozta a diagnosztikai beállítást, a rendszer egy tárolót hoz létre a Storage-fiókban, amint egy esemény bekövetkezik az engedélyezett naplózási kategóriák egyikében. A tárolóban lévő Blobok a következő elnevezési konvenciót használják:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például egy hálózati biztonsági csoport blobjának neve a következőhöz hasonló lehet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mivel az erőforrás-naplózási események óránként egyedi blobokra vannak bontva.

Az PT1H. JSON fájlon belül minden eseményt a következő formátumban tárol a rendszer. Ez közös legfelső szintű sémát használ, de minden egyes Azure-szolgáltatás esetében egyedinek kell lennie az [erőforrás-naplók sémájában](resource-logs-overview.md#resource-logs-schema)leírtak szerint.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> A platform naplói a blob Storage-ba kerülnek a [JSON-vonalak](http://jsonlines.org/)használatával, ahol minden esemény egy sor, a sortörési karakter pedig új eseményt jelez. Ez a formátum a 2018 novemberében lett implementálva. Ezen időpont előtt a naplók a blob Storage-ba kerülnek, mint a rekordok JSON-tömbje, amelyet a következő témakörben talál: [felkészülés a formátum változására a Storage-fiókba archivált Azure monitor platform-naplók](resource-logs-blob-format.md)számára.

## <a name="next-steps"></a>További lépések

* [Blobok letöltése elemzéshez](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiválja Azure Active Directory naplókat a Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [További információ az erőforrás-naplókról](../../azure-monitor/platform/resource-logs-overview.md).

