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
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955867"
---
# <a name="standard-properties-in-log-analytics-records"></a>A Log Analytics-rekordok szabványos tulajdonságai
Az adatok [Log Analytics](../log-analytics/log-analytics-queries.md) rekordkészletet, amelyek mindegyike egy adott adattípus, amely egy egyedi tulajdonságkészlettel rendelkezik van tárolva. Számos adattípusok, amelyek közösek a több típus több alapvető tulajdonságainak lesz. Ez a cikk ismerteti ezeket a tulajdonságokat és példákat, hogyan használhatja ezeket a lekérdezéseket.

Ezek a tulajdonságok néhány létrehozás folyamatban van megvalósítva, így előfordulhat, hogy megjelennek az egyes adattípusok, de még nincs a többi.


## <a name="resourceid"></a>_ResourceId
A **_ResourceId** a tulajdonság tárolja az erőforrás egyedi azonosítója, amely a rekord társítva van. Ez lehetővé teszi a lekérdezés hatókörét, csak a rekordok egy adott erőforrásból, vagy a kapcsolódó adatok join több különböző táblázat standard tulajdonságot.

Az Azure-erőforrásokhoz, értékét **_ResourceId** van a [Azure-erőforrást azonosító URL-cím](../azure-resource-manager/resource-group-template-functions-resource.md). A tulajdonság jelenleg csak Azure-erőforrások, de például a helyi számítógépek Azure-on kívüli erőforrások terjeszteni. 

### <a name="examples"></a>Példák
Az alábbi példa bemutatja egy lekérdezést, amely a teljesítmény- és esemény adatokat minden számítógéphez csatlakozik. Összes esemény azonosítóval jelenik meg _101_ és 50 % feletti processzorhasználat.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Az alábbi példa bemutatja egy lekérdezést, amely összekapcsolja _AzureActivity_ rögzíti a _SecurityEvent_ rögzíti. A felhasználókkal, hogy ezek a gépek naplózott összes tevékenység műveleteket jeleníti meg.

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