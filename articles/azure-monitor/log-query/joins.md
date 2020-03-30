---
title: Csatlakozik az Azure Monitor naplólekérdezéseihez | Microsoft dokumentumok
description: Ez a cikk egy leckét tartalmaz az Azure Monitor naplólekérdezései ben csatlakozik használatával kapcsolatos leckét.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670202"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Csatlakozik az Azure Figyelő naplólekérdezéseihez

> [!NOTE]
> A lecke befejezése előtt el kell [végeznie az Azure Monitor naplóanalytics](get-started-portal.md) és [az Azure Monitor naplólekérdezéseinek első lépéseit.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Az illesztések lehetővé teszik több tábla adatainak elemzését ugyanabban a lekérdezésben. Két adatkészlet sorait egyesítik a megadott oszlopok egyező értékeivel.


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

Ebben a példában az első adatkészlet szűri az összes bejelentkezési eseményt. Ez egy második adatkészlettel van összekapcsolva, amely az összes kijelentkezési eseményre szűri. A kivetített oszlopok a _következők: Számítógép_, _Fiók_, _TargetLogonId_és _TimeGenerated_. Az adatkészleteket egy _TargetLogonId_megosztott oszlop korrelál. A kimenet korrelációnként egyetlen rekord, amely a bejelentkezési és a kijelentkezési időt is rendelkezik.

Ha mindkét adatkészlet azonos nevű oszlopokkal rendelkezik, a jobb oldali adatkészlet oszlopai indexszámot kapnak, így ebben a példában az eredmények a Bal oldali tábla értékeit tartalmazó _TargetLogonId értéket,_ a jobb oldali tábla értékeit pedig a _TargetLogonId1_ értéket jelenítik meg. Ebben az esetben a második _TargetLogonId1_ oszlopot az `project-away` operátor használatával távolították el.

> [!NOTE]
> A teljesítmény javítása érdekében az `project` operátor használatával csak az egyesített adatkészletek megfelelő oszlopait őrizte meg.


Két adatkészlet összekapcsolása az alábbi szintaxissal, és az összekapcsolt kulcs neve eltér a két tábla között:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Keressünk táblázatokat
Az illesztések gyakori használata az értékek `datatable` statikus leképezésének használata, amely segíthet az eredmények szalonképesebb módon történő átalakításában. Például a biztonsági esemény adatok gazdagítása az egyes eseményazonosítók eseménynevével.

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

![Összekapcsolódás adattáblával](media/joins/dim-table.png)

## <a name="join-kinds"></a>Illesztési fajták
Adja meg a _kedves_ argumentumillesztés típusát. Minden típus az alábbi táblázatban leírtak szerint más egyezést hajt végre az adott táblák rekordjai között.

| Illesztés típusa | Leírás |
|:---|:---|
| innerunique | Ez az alapértelmezett illesztési mód. Először a bal oldali táblában található egyező oszlop értékei találhatók, és a duplikált értékek törlődnek.  Ezután az egyedi értékek készlete a megfelelő táblához igazodik. |
| Belső | Az eredmények csak a két tábla egyező rekordjai szerepelnek. |
| bal oldali | A bal oldali tábla összes rekordja és a jobb oldali tábla egyező rekordjai szerepelnek az eredményekközött. A nem egyező kimeneti tulajdonságok null értékeket tartalmaznak.  |
| leftanti között | A bal oldali rekordok, amelyek nem rendelkeznek jobb oldali egyezések szerepelnek az eredmények között. Az eredménytábla csak a bal oldali táblázat oszlopait tartalmazja. |
| balfél | A bal oldali, jobbról egyező rekordok szerepelnek az eredményekközött. Az eredménytábla csak a bal oldali táblázat oszlopait tartalmazja. |


## <a name="best-practices"></a>Ajánlott eljárások

Az optimális teljesítmény érdekében vegye figyelembe a következő pontokat:

- Az egyes táblákon használjon időszűrőt az illesztéshez kiértékelendő rekordok csökkentéséhez.
- `where` Használja, `project` és csökkentse a sorok és oszlopok számát a beviteli táblázatokban az illesztés előtt.
- Ha az egyik tábla mindig kisebb, mint a másik, használja az illesztés bal oldalaként.


## <a name="next-steps"></a>További lépések
Tekintse meg az Azure Monitor naplólekérdezéseinek egyéb leckéit:

- [Sztringműveletek](string-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Diagramok](charts.md)
