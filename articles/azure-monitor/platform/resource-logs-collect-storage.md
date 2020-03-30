---
title: Az Azure-erőforrásnaplók archiválása a tárfiókba | Microsoft dokumentumok
description: Ismerje meg, hogyan archiválhatja az Azure-erőforrás naplók hosszú távú megőrzése egy tárfiókban.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274215"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Az Azure-erőforrásnaplók archiválása tárfiókba
Az Azure-beli [platformnaplók,](platform-logs-overview.md) beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat nyújtanak az Azure-erőforrásokhoz és az Azure platformhoz, amelytől függenek.  Ez a cikk ismerteti a platformnaplók gyűjtése egy Azure-tárfiók archiválási adatok megőrzéséhez.

## <a name="prerequisites"></a>Előfeltételek
Létre kell [hoznia egy Azure storage-fiókot,](../../storage/common/storage-account-create.md) ha még nem rendelkezik ilyen. A tárfiók nem kell ugyanabban az előfizetésben, mint az erőforrás-küldő naplók, amíg a felhasználó, aki konfigurálja a beállítást a megfelelő RBAC-hozzáférés mindkét előfizetéshez.


> [!IMPORTANT]
> Az Azure Data Lake Storage Gen2-fiókok jelenleg nem támogatottak a diagnosztikai beállítások célhelyeként, még akkor sem, ha az Azure Portalon érvényes lehetőségként szerepelhetnek.


Ne használjon olyan meglévő tárfiókot, amelymás, nem figyelési adatokat tárol, hogy jobban szabályozhassa az adatokhoz való hozzáférést. Ha a tevékenységnapló és az erőforrásnaplók együtt archiválása, bár, dönthet úgy, hogy ugyanazt a tárfiókot, hogy az összes figyelési adatok at egy központi helyen.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
Platformnaplók küldése a tárolóba és más célhelyekre egy Azure-erőforrás diagnosztikai beállításának létrehozásával. A naplók [és metrikák azure-beli gyűjtéséhez](diagnostic-settings.md) olvassa el a Diagnosztikai beállítás létrehozása című témakört.


## <a name="collect-data-from-compute-resources"></a>Adatok gyűjtése számítási erőforrásokból
A diagnosztikai beállítások erőforrásnaplókat gyűjtenek az Azure számítási erőforrásaihoz, mint bármely más erőforráshoz, de a vendég operációs rendszerüket vagy a számítási feladataikat nem. Az adatok gyűjtéséhez telepítse a [Windows Azure Diagnosztika ügynököt.](diagnostics-extension-overview.md) 


## <a name="schema-of-platform-logs-in-storage-account"></a>Platformnaplók sémája a tárfiókban

Miután létrehozta a diagnosztikai beállítást, létrejön egy tárolótároló a tárfiókban, amint egy esemény bekövetkezik az engedélyezett naplókategóriák egyikében. A tárolón belüli blobok a következő elnevezési konvenciót használják:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Egy hálózati biztonsági csoport blobjának neve például a következőhöz hasonló lehet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc érték (m=00) mindig 00, mivel az erőforrásnapló-események óránként ida ba vannak osztva.

A PT1H.json fájlon belül minden esemény a következő formátumban tárolódik. Ez egy közös legfelső szintű sémát fog használni, de minden Azure-szolgáltatáshoz egyedi lesz az [Erőforrás-naplók sémájában](diagnostic-logs-schema.md) és [a tevékenységnapló sémájában](activity-log-schema.md)leírtak szerint.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> A platformnaplók a Blob storage-ba [json-vonalak](http://jsonlines.org/)használatával kerülnek írásra, ahol minden esemény egy vonal, és az újvonal karakter egy új eseményt jelez. Ezt a formátumot 2018 novemberében vezették be. Ezt a dátumot megelőzően a naplók a blobstorage-ba egy json-tömbként kerültek írásra az [Azure Monitor platformnaplóinak egy tárfiókba archivált formátummódosításra](resource-logs-blob-format.md)című részében leírtak szerint.

## <a name="next-steps"></a>További lépések

* [További információ az erőforrásnaplókról.](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozásával naplót és metrikákat gyűjthet az Azure-ban.](diagnostic-settings.md)
* [Blobok letöltése elemzésre.](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Archiválja az Azure Active Directory-naplókat az Azure Monitor segítségével.](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
