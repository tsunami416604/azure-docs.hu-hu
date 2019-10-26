---
title: Hatékony naplózási lekérdezések írása a Azure Monitorban | Microsoft Docs
description: Az erőforrásokra mutató hivatkozások a Log Analytics lekérdezések írásához.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894135"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Hatékony naplózási lekérdezések írása a Azure Monitorban
Ez a cikk a hatékony naplózási lekérdezéseknek a Azure Monitor-ben történő írásához nyújt javaslatokat. Ezen stratégiák használatával gondoskodhat arról, hogy a lekérdezések gyorsan és minimálisan meghallva fussanak.

## <a name="scope-your-query"></a>A lekérdezés hatóköre
Ha a lekérdezés több adattal rendelkezik, mint amennyit ténylegesen szüksége lehet, hosszú ideig futó lekérdezéshez vezethet, és gyakran túl sok adatmennyiséget eredményez az eredmények hatékony elemzése érdekében. Szélsőséges esetekben előfordulhat, hogy a lekérdezés még időtúllépést és hibát jelez.

### <a name="specify-your-data-source"></a>Adatforrás meghatározása
Egy hatékony lekérdezés írásának első lépése a hatókörének korlátozása a szükséges adatforrásokra. A tábla meghatározása mindig előnyben részesített a széles szöveges keresés futtatásához, például `search *`. Egy adott tábla lekérdezéséhez indítsa el a lekérdezést a táblázat nevével a következő módon:

``` Kusto
requests | ...
```

A [kereséssel](/azure/kusto/query/searchoperator) egy adott tábla több oszlopában is kereshet egy értéket, például a következőhöz hasonló lekérdezéssel:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

A [Union](/azure/kusto/query/unionoperator) használatával több táblázatot is lekérdezheti, például az alábbiakat:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Időtartomány megadásának időpontja
A lekérdezést a szükséges időtartományra is korlátoznia kell. Alapértelmezés szerint a lekérdezés az elmúlt 24 órában gyűjtött adatokat is tartalmazza. Ezt a lehetőséget módosíthatja az [időtartomány-választóban](get-started-portal.md#select-a-time-range) , vagy explicit módon hozzáadhatja a lekérdezéshez. A táblázat neve után a legjobb lehetőség az Időszűrő hozzáadása, hogy a lekérdezés többi része csak az adott tartományon belüli adatok feldolgozását dolgozza fel:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Csak a legújabb rekordok beolvasása

Ha csak a legújabb rekordokat szeretné visszaadni, használja a *felső* operátort a következő lekérdezésben, amely a *nyomkövetési* táblában naplózott legújabb 10 rekordot adja vissza:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Rekordok szűrése
Ha csak az adott feltételnek megfelelő naplókat szeretné áttekinteni, használja a *Where* operátort a következő lekérdezésben, amely csak azokat a rekordokat adja vissza, amelyekben a _severityLevel_ értéke 0-nál nagyobb:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Karakterlánc-összehasonlítások
A [karakterláncok kiértékelése](/azure/kusto/query/datatypes-string-operators)során általában `has`t kell használnia `contains` helyett, amikor teljes tokeneket keres. `has` hatékonyabb, mert nem kell megkeresnie az alsztringeket.

## <a name="returned-columns"></a>Visszaadott oszlopok

A [Project](/azure/kusto/query/projectoperator) használatával Szűkítse le a feldolgozott oszlopok készletét, hogy csak a szükségesek legyenek:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Míg a [kibővítés](/azure/kusto/query/extendoperator) használatával kiszámíthatja az értékeket, és saját oszlopokat hozhat létre, általában hatékonyabb lesz a tábla oszlopainak szűrése.

Például az alábbi első lekérdezés, amely a szűrők _művelet\_a névben_ hatékonyabb lenne, mint a második, amely létrehoz egy új _előfizetési_ oszlopot és szűrőket a következőhöz:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Illesztések használata
A [JOIN](/azure/kusto/query/joinoperator) operátor használatakor válassza ki azt a táblázatot, amelynek kevesebb sora van a lekérdezés bal oldalán.


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az ajánlott eljárások lekérdezéséről, olvassa el az [ajánlott eljárások lekérdezését](/azure/kusto/query/best-practices)ismertető témakört.