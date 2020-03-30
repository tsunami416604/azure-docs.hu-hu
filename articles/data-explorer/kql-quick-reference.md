---
title: KQL – rövid útmutató
description: A hasznos KQL függvények listája és definícióik szintaktikai példákkal.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139079"
---
# <a name="kql-quick-reference"></a>KQL – rövid útmutató

Ez a cikk a függvények listáját és azok leírását tartalmazza a Kusto lekérdezési nyelv használatának megkezdéséhez.

| Operátor/függvény                               | Leírás                           | Szintaxis                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Szűrés/Keresés/Feltétel**                      |**_Releváns adatok keresése szűréssel vagy kereséssel_** |                      |
| [Ahol](/azure/kusto/query/whereoperator)                      | Szűrők egy adott állítmányon           | `T | where Predicate`                         |
| [ahol tartalmaz/ tartalmaz](/azure/kusto/query/whereoperator)        | `Contains`: Bármilyen karakterláncalatti egyezést keres <br> `Has`: Egy adott szót keres (jobb teljesítmény)  | `T | where col1 contains/has "[search term]"`|
| [Keresés](/azure/kusto/query/searchoperator)                    | A táblázat összes oszlopának keresése az értékre | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [venni](/azure/kusto/query/takeoperator)                        | A megadott számú rekordot adja eredményül. Lekérdezés tesztelése<br>**_Megjegyzés:_** `_take`_ `_limit`és _ szinonimák. | `T | take NumberOfRows` |
| [Esetben](/azure/kusto/query/casefunction)                        | Más rendszerek if/then/elseif állapotutasításához hasonlóan feltételutasítást ad hozzá. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | Táblázatot hoz létre a beviteli tábla megadott oszlopainak különböző kombinációjával | `distinct [ColumnName], [ColumnName]` |
| **Dátum/idő**                                   |**_Dátum- és időfüggvényeket használó műveletek_**               |                          |
|[Ezelőtt](/azure/kusto/query/agofunction)                           | A lekérdezés végrehajtásának időpontjához viszonyított időeltolást adja eredményül. Például `ago(1h)` egy órával az aktuális óra leolvasása előtt. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | [Különböző dátumformátumú adatokat ad eredményül.](/azure/kusto/query/format-datetimefunction#supported-formats) | `format_datetime(datetime , format)` |
| [Bin](/azure/kusto/query/binfunction)                          | Az összes érték kerekítése időkeretben és csoportosításuk | `bin(value,roundTo)` |
| **Oszlopok létrehozása/eltávolítása**                   |**_Táblázat oszlopainak hozzáadása és eltávolítása_** |                                                    |
| [Nyomtatási](/azure/kusto/query/printoperator)                      | Egyetlen sor kiírása egy vagy több skaláris kifejezéssel | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Projekt](/azure/kusto/query/projectoperator)                  | A megadott sorrendbe felvenni kívánt oszlopok kijelölése | `T | project ColumnName [= Expression] [, ...]` <br> Vagy <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt-el](/azure/kusto/query/projectawayoperator)         | A kimenetből kizárandó oszlopok kijelölése | `T | project-away ColumnNameOrPattern [, ...]` |
| [Kiterjesztése](/azure/kusto/query/extendoperator)                    | Számított oszlop létrehozása és hozzáadása az eredményhalmazhoz | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Rendezési és összesítő adatkészlet**                 |**_Az adatok átstrukturálása az adatok értelmes módon történő rendezésével vagy csoportosításával_**|                  |
| [Rendezés](/azure/kusto/query/sortoperator)                        | A beviteli táblázat sorait növekvő vagy csökkenő sorrendben egy vagy több oszlop szerint rendezi | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Top](/azure/kusto/query/topoperator)                          | Az adatkészlet első N-sorait adja eredményül, amikor az adatkészlet et a`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Összefoglalni](/azure/kusto/query/summarizeoperator)              | Csoportosítja a sorokat a `by` csoportoszlopok szerint, és kiszámítja az összesítéseket az egyes csoportokra | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [Számít](/azure/kusto/query/countoperator)                       | Megszámolja a bemeneti tábla rekordjait (például T)<br>Ez a kezelő gyorsírás`summarize count() `| `T | count` |
| [csatlakozás](/azure/kusto/query/joinoperator)                        | Két tábla sorait egyesítve új táblát alkotva az egyes táblák megadott oszlopainak értékeivel. Az illesztési típusok teljes `flouter` `inner`skáláját `innerunique` `leftanti`támogatja: `leftsemi`, `rightanti` `rightantisemi`, `rightouter`, `leftantisemi`, `leftouter`, , , , ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unió](/azure/kusto/query/unionoperator)                      | Két vagy több táblát vesz igénybe, és az összes sort visszaadja | `[T1] | union [T2], [T3], …` |
| [Tartomány](/azure/kusto/query/rangeoperator)                      | Értékek számtani sorozatát levemidítő táblázat létrehozása | `range columnName from start to stop step step` |
| **Adatok formázása**                                 | **_Az adatok átalakítása a kimenetre hasznos módon_** | |
| [Keresési](/azure/kusto/query/lookupoperator)                    | Egy ténytábla oszlopainak bővítése dimenziótáblában keresett értékekkel | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-kibontás](/azure/kusto/query/mvexpandoperator)               | A dinamikus tömböket sorokká alakítja (többértékű bővítés) | `T | mv-expand Column` |
| [parse](/azure/kusto/query/parseoperator)                      | Kiértékel egy karakterlánc-kifejezést, és az értékét egy vagy több számított oszlopra elemzi. Strukturálatlan adatok strukturálására használható. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-sorozat](/azure/kusto/query/make-seriesoperator)          | Megadott összesített értékek sorozatát hozza létre egy megadott tengely mentén | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Hagyja](/azure/kusto/query/letstatement)                         | Nevet köt olyan kifejezésekhez, amelyek a kötött értékre hivatkozhatnak. Az értékek lambda kifejezések lehetnek, amelyek ad-hoc függvényeket hoznak létre a lekérdezés részeként. Kifejezéseket `let` hozhat létre olyan táblák felett, amelyek eredményei új táblának tűnnek. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Általános**                                     | **_Egyéb műveletek és funkciók_** | |
| [Hivatkozhat](/azure/kusto/query/invokeoperator)                    | Futtatja a függvényt az asztalon, amelyet bemenetként fogad. | `T | invoke function([param1, param2])` |
| [pluginName kiértékelése](/azure/kusto/query/evaluateoperator)     | Kiértékeli a lekérdezési nyelvi bővítményeket (bővítmények) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Vizualizáció**                               | **_Az adatokat grafikus formátumban megjelenítő műveletek_** | |
| [Render](/azure/kusto/query/renderoperator) | Az eredményeket grafikus kimenetként jeleníti meg | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
