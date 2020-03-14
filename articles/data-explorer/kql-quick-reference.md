---
title: KQL – rövid útmutató
description: A hasznos KQL függvények listája, valamint azok definíciói a szintaxissal kapcsolatos példákkal.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139079"
---
# <a name="kql-quick-reference"></a>KQL – rövid útmutató

Ez a cikk a függvények listáját és azok leírását mutatja be a Kusto lekérdezési nyelv használatának megkezdéséhez.

| Operátor/függvény                               | Leírás                           | Szintaxis                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Szűrés/keresés/feltétel**                      |**_Releváns adatokat kereshet szűréssel vagy kereséssel_** |                      |
| [ahol](/azure/kusto/query/whereoperator)                      | Szűrők egy adott predikátumon           | `T | where Predicate`                         |
| [Hol található/tartalmaz](/azure/kusto/query/whereoperator)        | `Contains`: bármilyen alsztring egyezést keres <br> `Has`: egy adott szót keres (jobb teljesítmény)  | `T | where col1 contains/has "[search term]"`|
| [Keresés](/azure/kusto/query/searchoperator)                    | A tábla összes oszlopának megkeresése az értékhez | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [eltarthat](/azure/kusto/query/takeoperator)                        | A megadott számú rekordot adja vissza. Lekérdezés tesztelése<br>**_Megjegyzés_** : `_take`_ és `_limit`_ szinonimák. | `T | take NumberOfRows` |
| [esetben](/azure/kusto/query/casefunction)                        | Feltételi utasítást is hozzáadhat, amely hasonló az IF/then/elseif más rendszerekben való hozzáadásához. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [különböző](/azure/kusto/query/distinctoperator)                | Táblázatot hoz létre a bemeneti tábla megadott oszlopainak eltérő kombinációjával. | `distinct [ColumnName], [ColumnName]` |
| **Dátum és idő**                                   |**_Dátum-és időfüggvényeket használó műveletek_**               |                          |
|[ezelőtt](/azure/kusto/query/agofunction)                           | A lekérdezés végrehajtásának időpontjához viszonyított időeltolódást adja vissza. `ago(1h)` például egy óra az aktuális óra olvasása előtt. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | [Különböző formátumú](/azure/kusto/query/format-datetimefunction#supported-formats)adatok visszaadása. | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction)                          | Az összes érték kerekítése egy adott időkeretben és csoportokba | `bin(value,roundTo)` |
| **Oszlopok létrehozása/eltávolítása**                   |**_Oszlopok hozzáadása vagy eltávolítása egy táblában_** |                                                    |
| [nyomtatási](/azure/kusto/query/printoperator)                      | Egyetlen sor kimenete egy vagy több skaláris kifejezéssel | `print [ColumnName =] ScalarExpression [',' ...]` |
| [projekt](/azure/kusto/query/projectoperator)                  | Kijelöli a megadott sorrendben szerepeltetni kívánt oszlopokat. | `T | project ColumnName [= Expression] [, ...]` <br> Vagy <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt – vendég](/azure/kusto/query/projectawayoperator)         | Kiválasztja a kimenetből kizárandó oszlopokat. | `T | project-away ColumnNameOrPattern [, ...]` |
| [kiterjesztése](/azure/kusto/query/extendoperator)                    | Létrehoz egy számított oszlopot, és hozzáadja az eredményhalmazban | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Adatkészlet rendezése és összesítése**                 |**_Az adat átstrukturálása rendezéssel vagy csoportosítással értelmes módon_**|                  |
| [Rendezés](/azure/kusto/query/sortoperator)                        | A bemeneti tábla sorainak rendezése egy vagy több oszlop szerint növekvő vagy csökkenő sorrendben | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Top](/azure/kusto/query/topoperator)                          | Az adatkészlet első N sorát adja vissza, ha az adatkészlet `by` használatával van rendezve. | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Összegzés](/azure/kusto/query/summarizeoperator)              | Csoportosítja a sorokat a `by` csoport oszlopainak megfelelően, és kiszámítja az összesítéseket az egyes csoportokon | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Megszámolja a bemeneti tábla rekordjait (például T)<br>Ez az operátor a `summarize count() ` rövidítése| `T | count` |
| [csatlakozás](/azure/kusto/query/joinoperator)                        | Egyesíti a két tábla sorait úgy, hogy az egyes táblákból a megadott oszlop (ok) megfeleltetésével új táblát formáljon. A az illesztési típusok teljes skáláját támogatja: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Union](/azure/kusto/query/unionoperator)                      | Két vagy több táblát ad vissza, és visszaadja az összes sorát | `[T1] | union [T2], [T3], …` |
| [tartomány](/azure/kusto/query/rangeoperator)                      | Létrehoz egy táblázatot az értékek számtani sorozatával | `range columnName from start to stop step step` |
| **Adatformátum**                                 | **_Az adat átstrukturálása a kimenetre hasznos módon_** | |
| [keresési](/azure/kusto/query/lookupoperator)                    | Kibővíti a egyedkapcsolati tábla oszlopait egy dimenzió táblában megkeresett értékekkel | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV – Kibontás](/azure/kusto/query/mvexpandoperator)               | Dinamikus tömbök bekapcsolása sorokba (többértékű bővítés) | `T | mv-expand Column` |
| [elemezni](/azure/kusto/query/parseoperator)                      | Kiértékel egy karakterlánc-kifejezést, és egy vagy több számított oszlopba elemzi az értékét. Strukturálatlan adatmennyiségek strukturálására használható. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [sorozat készítése](/azure/kusto/query/make-seriesoperator)          | Megadott összesített értékek sorozatának létrehozása egy megadott tengely mentén | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [lehetővé teszik](/azure/kusto/query/letstatement)                         | Egy nevet hoz létre olyan kifejezésekhez, amelyek hivatkozhatnak a kötött értékére. Az értékek lambda kifejezésekkel hozhatók létre ad-hoc függvények a lekérdezés részeként. A `let` használatával hozzon létre kifejezéseket olyan táblákon, amelyek eredményei egy új táblához hasonlítanak. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Általános**                                     | **_Egyéb műveletek és függvények_** | |
| [Invoke](/azure/kusto/query/invokeoperator)                    | A függvényt a bemenetként kapott táblán futtatja. | `T | invoke function([param1, param2])` |
| [pluginName kiértékelése](/azure/kusto/query/evaluateoperator)     | A lekérdezés nyelvi bővítményeinek (plugins) kiértékelése | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Vizualizáció**                               | **_Az adatok grafikus formátumban való megjelenítésének műveletei_** | |
| [renderelési](/azure/kusto/query/renderoperator) | Az eredményeket grafikus kimenetként jeleníti meg | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
