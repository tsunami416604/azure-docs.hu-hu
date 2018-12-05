---
title: Az Azure Log Analytics-lekérdezések csatlakozik |} A Microsoft Docs
description: Ez a cikk tartalmazza a lecke az illesztések, az a Log Analytics lekérdezési nyelv használatával.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: c1f578c11fff963ee4bec47bd3737cd224b14720
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882571"
---
# <a name="joins-in-log-analytics-queries"></a>A Log Analytics-lekérdezések csatlakozik

> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-portal.md) és [Ismerkedés a lekérdezések](get-started-queries.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Az összekapcsolások lehetővé teszik az ugyanabból a lekérdezés több táblájából adatok elemzéséhez. Két adatkészletet sorait, egyesítse a megadott oszlop egyező értékei alapján.


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

Ebben a példában az első adatkészletet szűri az összes bejelentkezési eseményeket. Ez egy második adatkészlet szűri az összes kijelentkezési események van csatlakoztatva. Az előre jelzett oszlop _számítógép_, _fiók_, _TargetLogonId_, és _TimeGenerated_. Az adatkészletek közötti kapcsolatot, közös oszlop szerint _TargetLogonId_. A kimenet a korrelációs, amely rendelkezik a bejelentkezési és kijelentkezési idő kiszolgálónként egy rekordot.

Ha mindkét olyan adatkészlettel rendelkezik oszlopokat ugyanazzal a névvel, a jobb oldali adathalmaz oszlopainak lenne kell megadni indexszámmal, így ebben a példában az eredmények jelennek _TargetLogonId_ értékekkel a bal oldali táblából, és  _TargetLogonId1_ a jobb oldali táblában a megfelelő értékekkel. Ebben az esetben a második _TargetLogonId1_ oszlop használatával el lett távolítva a `project-away` operátor.

> [!NOTE]
> A teljesítmény javítása érdekében hagyja a csatlakoztatott-adatkészletek, használatával csak a kapcsolódó oszlopokban a `project` operátor.


Az alábbi szintaxissal való csatlakozásra, két adatkészletet, és a csatlakoztatott kulcs van egy másik nevet a két táblázat között:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Keresési táblák
Egyik gyakori felhasználási illesztések használ statikus hozzárendelés használatával értékek `datatable` , amelyek segítségével a az eredményeket több presentable módon be. Például a biztonsági feldúsítani eseményadatok az esemény nevét, minden esemény esetén az azonosítóját.

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

![Összekapcsolás a következővel datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Típusú JOIN
Adja meg az illesztés típusának a _kind_ argumentum. Egyes hajt végre egy másik egyezés a rekordokat az adott táblák közötti, az alábbi táblázatban leírtak szerint.

| Összekapcsolás típusa | Leírás |
|:---|:---|
| innerunique | Ez az alapértelmezett illesztési módban. Először a bal oldali tábla egyező oszlop értékeinek találhatók, és a duplikált értékek el lesznek távolítva.  Ezután egyedi értékek rendszer összeveti a jobb oldali tábla. |
| belső | A két tábla csak a megfelelő rekordok szerepelnek az eredményeket. |
| leftouter | A bal oldali tábla összes rekordját és a megfelelő rekordokat a jobb oldali táblában szerepelnek az eredményeket. Páratlan output tulajdonságai null értékeket tartalmaz.  |
| leftanti | Rekordok bal oldalán, amelyeken nincs egyezés a jobb eredmények szerepelnek. Az eredmények táblának csak a bal oldali tábla oszlopai. |
| leftsemi | Jobb oldalról egyezések rendelkező rekordokat bal oldalán szerepelnek az eredményeket. Az eredmények táblának csak a bal oldali tábla oszlopai. |


## <a name="best-practices"></a>Ajánlott eljárások

Vegye figyelembe az optimális teljesítmény érdekében a következőket:

- Minden tábla idő szűrő használatával csökkentheti a rekordokat, amelyek a join kell kiértékelni.
- Használat `where` és `project` csökkentése érdekében a sorok és a csatlakozás előtt a bemeneti tábla oszlopainak számát.
* Ha egy tábla minden esetben kisebb, mint a másik, az legyen az illesztés bal oldalán.


## <a name="next-steps"></a>További lépések
Tekintse meg a Log Analytics lekérdezési nyelv segítségével a többi leckék:

- [Karakterlánc-műveletek](string-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Diagramok](charts.md)