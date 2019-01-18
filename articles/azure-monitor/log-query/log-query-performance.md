---
title: Az Azure monitorban hatékony log-lekérdezések írása |} A Microsoft Docs
description: Megtudhatja, hogyan kell lekérdezéseket írni a Log Analytics az erőforrásokhoz való hivatkozásokat.
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
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: f7eca75c6c445f801ad5c4dbc080d2ece9dc23f9
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392230"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Az Azure monitorban hatékony log-lekérdezések írása
Ez a cikk az Azure monitorban hatékony log lekérdezések írására vonatkozó javaslatokat nyújt. Ezek a stratégiák használ, biztosíthatja, hogy gyorsan fog futni, a lekérdezések, illetve a minimális overheard.

## <a name="scope-your-query"></a>A lekérdezés hatókörét
A lekérdezések feldolgozásához ténylegesen szüksége több adatot egy hosszan futó lekérdezést vezetnek, és hatékonyan elemezheti az eredményeket a túl sok adatot gyakran eredményez. Szélsőséges esetben a lekérdezés időkorlátja még és sikertelen lesz.

### <a name="specify-your-data-source"></a>Adja meg az adatforrás
Az első lépés egy hatékony lekérdezések írása korlátozná a szükséges adatforrásokhoz hatókörében. Adjon meg egy tábla mindig előnyben részesített keresztül egy széles szöveges keresés, például a futó `search *`. Egy adott tábla lekérdezése, indítsa el a lekérdezést a tábla neve, ahogy a következő:

``` Kusto
requests | ...
```

Használhat [keresési](/azure/kusto/query/searchoperator) érték keresése több oszlopban az adott táblák használata a következőhöz hasonló lekérdezést:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Használat [union](/kusto/query/unionoperator) lekérdezése több táblát a következőhöz hasonló:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Adjon meg egy időtartományt
A lekérdezés a szükséges adatok időtartománya is korlátozza. A lekérdezés alapértelmezés szerint az elmúlt 24 órában gyűjtött adatokat tartalmazza. Módosíthatja a megfelelő elemet a [tartomány időválasztó](get-started-portal.md#select-a-time-range) , vagy explicit módon felveheti a lekérdezést. A legjobb adja hozzá az Időszűrő közvetlenül a tábla neve után, hogy a lekérdezés részeit csak dolgozza fel az adatokat a tartományon belül:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Csak a legújabb bejegyzések beolvasása

Csak a legújabb bejegyzések használja a *felső* bejelentkezve operátor, ahogy a következő lekérdezést, amely a legújabb 10 rekordjait adja vissza a *nyomkövetések* tábla:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Rekordok szűrése
Csak egy adott feltételnek megfelelő naplók áttekintéséhez használja az *ahol* operátor, ahogy a következő lekérdezést, amely csak a rekord visszaadása, amelyben a _err_ értéke 0-nál nagyobb:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>A karakterlánc-összehasonlítások
Amikor [karakterláncok kiértékelése](/azure/kusto/query/datatypes-string-operators), általában használjon `has` helyett `contains` teljes jogkivonatok keresésekor. `has` még hatékonyabban, mivel nem rendelkezik a karakterláncrész kikeresése sikertelen volt.

## <a name="returned-columns"></a>Visszaadott oszlopok

Használat [projekt](/azure/kusto/query/projectoperator) szűkítése csak azokat kell feldolgozott oszlopok:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Bár használhatja [kiterjesztése](/azure/kusto/query/extendoperator) értékeket számolnak ki, és saját oszlopok létrehozásához, általában hatékonyabb csak egy táblázatoszlop szűrés.

Például az első lekérdezés az alábbi, a szűrők _művelet\_neve_ hatékonyabb, mint a második, amely létrehoz egy új lenne _előfizetés_ oszlopot és a szűrők:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Illesztések használata
Használatakor a [illesztési](/azure/kusto/query/joinoperator) operátor szerinti szűrése, válassza a bal oldalon a lekérdezés kívánt kevesebb sort tartalmazó tábla.


## <a name="next-steps"></a>További lépések
Lekérdezés ajánlott eljárásokkal kapcsolatos további tudnivalókért lásd: [ajánlott eljárások lekérdezése](/azure/kusto/query/best-practices).