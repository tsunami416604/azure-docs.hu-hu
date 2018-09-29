---
title: Az Azure Monitor Log Analytics-rekordok alapvető tulajdonságok |} A Microsoft Docs
description: Az Azure Monitor Log Analytics-több adattípusok előforduló tulajdonságait ismerteti.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: bcc5f23c163a391639d916b8a50c2c05d228ee91
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432086"
---
# <a name="standard-properties-in-log-analytics-records"></a>A Log Analytics-rekordok szabványos tulajdonságai
Az adatok [Log Analytics](../log-analytics/log-analytics-queries.md) rekordkészletet, amelyek mindegyike egy adott adattípus, amely egy egyedi tulajdonságkészlettel rendelkezik van tárolva. Számos adattípusok, amelyek közösek a több típus több alapvető tulajdonságainak lesz. Ez a cikk ismerteti ezeket a tulajdonságokat és példákat, hogyan használhatja ezeket a lekérdezéseket.

Ezek a tulajdonságok néhány létrehozás folyamatban van megvalósítva, így előfordulhat, hogy megjelennek az egyes adattípusok, de még nincs a többi.

## <a name="timegenerated"></a>TimeGenerated
A **TimeGenerated** tulajdonság tartalmazza a dátum és idő, hogy a rekord létrehozásának. Szűrés vagy időpontig összefoglalójához közös tulajdonság biztosít. Amikor kiválaszt egy időtartományt, nézetben vagy Irányítópulton az Azure Portalon, az eredmények szűréséhez TimeGenerated használ.

### <a name="examples"></a>Példák

Az alábbi lekérdezés minden nap az előző héten létrehozott események számát adja vissza.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Típus
A **típus** tulajdonság nevét tárolja a tábla, hogy a rekord, amelyből beolvasták is is értelmezhetők, a rekord típusa. Ez a tulajdonság akkor hasznos, a lekérdezések, amelyek több táblából, például rekordok a `search` operátor szerinti szűrése, megkülönböztetni a különböző típusú rekordok. **$table** helyén használható **típus** egyes helyeken.

### <a name="examples"></a>Példák
Az alábbi lekérdezés a bejegyzések száma az elmúlt egy órában gyűjtött típus szerint adja vissza.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_erőforrás-azonosító
A  **\_ResourceId** a tulajdonság tárolja az erőforrást, amelyhez társítva van a rekord egyedi azonosítója. Ez lehetővé teszi a lekérdezés hatókörét, csak a rekordok egy adott erőforrásból, vagy a kapcsolódó adatok join több különböző táblázat standard tulajdonságot.

Az Azure-erőforrásokhoz, értékét **_ResourceId** van a [Azure-erőforrást azonosító URL-cím](../azure-resource-manager/resource-group-template-functions-resource.md). A tulajdonság jelenleg csak Azure-erőforrások, de például a helyi számítógépek Azure-on kívüli erőforrások terjeszteni.

> [!NOTE]
> Egyes adattípusok már rendelkezik mezőkkel, amelyek tartalmazzák az Azure erőforrás-azonosító, vagy legalább részeit, például az előfizetés-azonosítójára. Bár ezek a mezők őrzi meg az előző verziókkal való kompatibilitás, javasoljuk, hogy használja a _ResourceId közötti korreláció végrehajtásához, mivel a egységesebb lesz.

### <a name="examples"></a>Példák
A következő lekérdezés teljesítmény- és esemény adatokat minden számítógéphez csatlakozik. Összes esemény azonosítóval jelenik meg _101_ és 50 % feletti processzorhasználat.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Az alábbi lekérdezés illesztések _AzureActivity_ rögzíti a _SecurityEvent_ rögzíti. A felhasználókkal, hogy ezek a gépek naplózott összes tevékenység műveleteket jeleníti meg.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>További lépések

- További információk [Log Analytics-adatok tárolt](../log-analytics/log-analytics-queries.md).
- Gyorsan egy leckében a [lekérdezések írása a Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Gyorsan egy leckében a [a táblázatok összekapcsolása a Log Analytics-lekérdezések](../log-analytics/query-language/joins.md).
