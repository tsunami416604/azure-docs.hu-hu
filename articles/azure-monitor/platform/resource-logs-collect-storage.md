---
title: Azure-beli erőforrás-naplók archiválása a Storage-fiókba | Microsoft Docs
description: Ismerje meg, hogyan archiválhatja Azure-beli erőforrás-naplóit a Storage-fiókok hosszú távú megőrzéséhez.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394931"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure-beli erőforrás-naplók archiválása a Storage-fiókba
Az Azure [platform-naplói](platform-logs-overview.md) , beleértve az Azure-tevékenységeket és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azoktól függő Azure-platformról.  Ez a cikk ismerteti a platform-naplók Azure Storage-fiókba való gyűjtését az archiváláshoz szükséges adatok megőrzése érdekében.

## <a name="prerequisites"></a>Előfeltételek
Ha még nem rendelkezik ilyennel, [létre kell hoznia egy Azure Storage-fiókot](../../storage/common/storage-account-create.md) . A Storage-fióknak nem kell ugyanabban az előfizetésben lennie, mint az erőforrás-küldési naplók, feltéve, hogy a beállítást konfiguráló felhasználó mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik.


> [!IMPORTANT]
> A Azure Data Lake Storage Gen2 fiókok jelenleg nem támogatottak a diagnosztikai beállítások célhelye, annak ellenére, hogy érvényes lehetőségként szerepelnek a Azure Portalban.


Ne használjon olyan meglévő Storage-fiókot, amely más, nem monitorozási adattárolási információkkal rendelkezik, így jobban szabályozhatja az adathozzáférést. Ha a tevékenység naplóját és az erőforrás-naplókat együtt archiválja, akkor dönthet úgy, hogy ugyanazt a Storage-fiókot használja, hogy az összes figyelési adattal egy központi helyen maradjon.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
Platform-naplók küldése a tárolóba és más célállomásokra az Azure-erőforrások diagnosztikai beállításának létrehozásával. A részletekért lásd: [diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md) .


## <a name="collect-data-from-compute-resources"></a>Adatok gyűjtése a számítási erőforrásokból
A diagnosztikai beállítások olyan erőforrás-naplókat gyűjtenek az Azure számítási erőforrásaihoz, mint bármely más erőforrás, nem pedig a vendég operációs rendszerük vagy a munkaterhelések. Az adatok gyűjtéséhez telepítse a [Windows Azure Diagnostics Agent ügynököt](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>A Storage-fiókban található platform-naplók sémája

Miután létrehozta a diagnosztikai beállítást, a rendszer egy tárolót hoz létre a Storage-fiókban, amint egy esemény bekövetkezik az engedélyezett naplózási kategóriák egyikében. A tárolóban lévő Blobok a következő elnevezési konvenciót használják:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például egy hálózati biztonsági csoport blobjának neve a következőhöz hasonló lehet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mivel az erőforrás-naplózási események óránként egyedi blobokra vannak bontva.

Az PT1H. JSON fájlon belül minden eseményt a következő formátumban tárol a rendszer. Ez közös legfelső szintű sémát használ, de minden egyes Azure-szolgáltatás esetében egyedinek kell lennie, az [erőforrás-naplók sémája](diagnostic-logs-schema.md) és a [tevékenység napló sémája](activity-log-schema.md)szerint.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> A platform naplói a blob Storage-ba kerülnek a [JSON-vonalak](http://jsonlines.org/)használatával, ahol minden esemény egy sor, a sortörési karakter pedig új eseményt jelez. Ez a formátum a 2018 novemberében lett implementálva. Ezen időpont előtt a naplók a blob Storage-ba kerülnek, mint a rekordok JSON-tömbje, amelyet a következő témakörben talál: [felkészülés a formátum változására a Storage-fiókba archivált Azure monitor platform-naplók](resource-logs-blob-format.md)számára.

## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](platform-logs-overview.md).
* [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md).
* [Blobok letöltése elemzéshez](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiválja Azure Active Directory naplókat a Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
