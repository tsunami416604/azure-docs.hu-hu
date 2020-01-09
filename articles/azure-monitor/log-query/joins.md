---
title: Illesztések Azure Monitor log lekérdezésekben | Microsoft Docs
description: Ez a cikk az illesztések Azure Monitor napló lekérdezésekben való használatával kapcsolatos leckét tartalmaz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f2880044e48e59d0d5f005f9772cdd0f807f7f29
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397840"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Illesztések Azure Monitor log lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt végezze el [a Azure Monitor log Analytics](get-started-portal.md) és [Azure monitor a naplók lekérdezését](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Az illesztések lehetővé teszik több táblázat adatainak elemzését ugyanabban a lekérdezésben. A két adathalmaz sorait a megadott oszlopok megfelelő értékeivel egyesítik.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

Ebben a példában az első adatkészlet szűri az összes bejelentkezési eseményt. Ez egy második adatkészlettel van csatlakoztatva, amely az összes kijelentkezési eseményt szűri. A tervezett oszlopok a _számítógép_, a _fiók_, a _TargetLogonId_és a _TimeGenerated_. Az adatkészleteket egy megosztott oszlop ( _TargetLogonId_) korrelálja. A kimenet egy korreláción belüli egyetlen rekord, amely a bejelentkezési és a kijelentkezési időt is tartalmazhatja.

Ha mindkét adatkészlet azonos nevű oszlopokkal rendelkezik, akkor a jobb oldali adatkészlet oszlopai egy indexet kapnak, így ebben a példában az eredmények a bal oldali táblából származó értékekkel _és a_ jobb oldali tábla értékeit tartalmazó _TargetLogonId_ jelennek meg. Ebben az esetben a második _TargetLogonId1_ oszlop el lett távolítva a `project-away` operátor használatával.

> [!NOTE]
> A teljesítmény javítása érdekében a `project` operátor használatával csak a csatlakoztatott adatkészletek megfelelő oszlopait tartsa meg.


A következő szintaxissal csatlakozhat két adatkészlethez, az összevont kulcs pedig eltérő névvel rendelkezik a két tábla között:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Keresési táblák
Az illesztések gyakori használata az értékek statikus leképezését használja a `datatable` használatával, amelyek segíthetnek az eredmények további bemutatása érdekében. Például a biztonsági események adatértékének az esemény nevével való dúsításához az egyes események AZONOSÍTÓi esetében.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Csatlakozás DataTable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Összekapcsolási típus
Adja meg a _Kind_ argumentummal való illesztés típusát. Mindegyik típus a következő táblázatban leírtak szerint eltérő egyezést hajt végre az adott táblák rekordjai között.

| Összekapcsolás típusa | Leírás |
|:---|:---|
| innerunique | Ez az alapértelmezett csatlakozási mód. Először a bal oldali tábla egyező oszlopának értékei találhatók, és az ismétlődő értékek törlődnek.  Ezután az egyedi értékek halmaza illeszkedik a jobb oldali táblához. |
| belső | A rendszer csak a mindkét táblában lévő rekordokat tartalmazza az eredmények között. |
| leftouter | A bal oldali tábla összes rekordja, valamint a jobb oldali tábla megfelelő rekordjai az eredmények között szerepelnek. A nem egyező kimeneti tulajdonságok null értéket tartalmaznak.  |
| leftanti | Az eredmények között szerepelnek a bal oldalon lévő, a jobb oldali egyezéseket nem tartalmazó rekordok. Az eredmények táblában csak a bal oldali tábla oszlopai láthatók. |
| leftsemi | A jobb oldali egyezések közül a bal oldalon lévő rekordok beletartoznak az eredményekbe. Az eredmények táblában csak a bal oldali tábla oszlopai láthatók. |


## <a name="best-practices"></a>Ajánlott eljárások

Az optimális teljesítmény érdekében vegye figyelembe a következő szempontokat:

- Használjon időszűrőt az egyes táblákon, hogy csökkentse azokat a rekordokat, amelyeket ki kell értékelni az illesztéshez.
- `where` és `project` használatával csökkentse a bemeneti táblák sorainak és oszlopainak számát a csatlakozás előtt.
- Ha egy tábla mindig kisebb, mint a többi, használja az illesztés bal oldalán.


## <a name="next-steps"></a>Következő lépések
Tekintse meg az Azure Monitor log-lekérdezések használatának egyéb tanulságait:

- [Karakterlánc-műveletek](string-operations.md)
- [Összesítési függvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON-és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Diagramok](charts.md)
