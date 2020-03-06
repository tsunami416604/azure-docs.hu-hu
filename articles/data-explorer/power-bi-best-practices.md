---
title: Ajánlott eljárások az Azure Adatkezelő-beli adatlekérdezés és-megjelenítés Power BI használatához
description: Ebből a cikkből megtudhatja, hogyan kérheti le és jelenítheti meg az Azure Adatkezelő-beli adatPower BI az ajánlott eljárások használatával.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395224"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Ajánlott eljárások az Azure Adatkezelő-beli adatlekérdezés és-megjelenítés Power BI használatához

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A [Power bi](https://docs.microsoft.com/power-bi/) egy üzleti elemzési megoldás, amellyel megjelenítheti az adatait, és megoszthatja az eredményeket a szervezeten belül. Az Azure Adatkezelő három lehetőséget biztosít a Power BIban lévő adatkapcsolatokhoz. Használja a [beépített összekötőt](power-bi-connector.md), [importáljon egy lekérdezést az Azure Adatkezelőból a Power BIba](power-bi-imported-query.md), vagy használjon [SQL-lekérdezést](power-bi-sql-query.md). Ez a cikk tippekkel szolgál az Azure Adatkezelő-adatPower BIekkel való lekérdezéséhez és megjelenítéséhez. 

## <a name="best-practices-for-using-power-bi"></a>Ajánlott eljárások Power BI használatához 

Ha terabájtos friss nyers adatmennyiséggel dolgozik, kövesse az alábbi irányelveket Power BI-irányítópultok és-jelentések folyamatos és frissített állapotának megtartásához:

* Az **utazáshoz** csak a jelentésekhez szükséges, Power bi. A mélyreható interaktív elemzéshez használja az [Azure adatkezelő webes felhasználói felületét](web-query-data.md) , amely a Kusto lekérdezési nyelvvel ad hoc feltárásra van optimalizálva.

* **Összetett modell** – az [összetett modell](https://docs.microsoft.com/power-bi/desktop-composite-models) használatával összevonhatja az összesített adatokat a legfelső szintű irányítópultok számára a szűrt, működő nyers adatokat használva. Egyértelműen meghatározhatja, hogy mikor kell nyers adatokat használni, és mikor kell összesített nézetet használni. 

* **Importálási mód és DirectQuery mód** – az **importálási** mód használata a kisebb adatkészletek interakciója céljából. **DirectQuery** mód használata nagyméretű, gyakran frissített adatkészletekhez. Létrehozhat például olyan dimenziós táblákat, amelyek **importálási** mód használatával kicsik, és nem változnak gyakran. Állítsa be a frissítési időközt az adatfrissítések várható arányának megfelelően. Hozzon létre **DirectQuery** módot használó egyedkapcsolat-táblákat, mivel ezek a táblázatok nagyméretűak, és nyers adatmennyiséget tartalmaznak. Ezekkel a táblázatokkal Power BI [részletezés](https://docs.microsoft.com/power-bi/desktop-drillthrough)használatával lehet bemutatni a szűrt adatvédelmet.

* **Párhuzamosság** – az Azure adatkezelő egy lineárisan skálázható adatplatform, így javíthatja az irányítópultok megjelenítésének teljesítményét azáltal, hogy növeli a végpontok közötti folyamat párhuzamosságát a következőképpen:

   * Növelje az [egyidejű kapcsolatok számát a DirectQuery Power BIban](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * [A párhuzamosság növeléséhez használjon gyenge konzisztenciát](/azure/kusto/concepts/queryconsistency). Ez hatással lehet az adatfrissítésre.

* **Hatékony szeletelők** – a [szinkronizálási szeletelők](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) használatával megakadályozhatja, hogy a jelentések készen álljanak az adatok betöltésére. Az adathalmaz strukturálása után helyezze az összes vizualizációt, és jelölje ki az összes szeletelőt, a szinkronizálási szeletelőt kiválasztva csak a szükséges adatmennyiséget töltheti be.

* **Szűrők használata** – a lehető legtöbb Power bi szűrőt használhatja az Azure adatkezelő keresésre a releváns adatszegmenseken.

* **Hatékony vizualizációk** – válassza ki az adataihoz leginkább megfelelő vizualizációkat.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tippek az Azure Adatkezelő-összekötő használatához a Power BI az adatlekérdezéshez

A következő szakasz tippeket és trükköket tartalmaz a Kusto lekérdezési nyelvének Power BI használatával történő használatához. Az [Azure adatkezelő-összekötő](power-bi-connector.md) használata a Power bi az adatmegjelenítéshez

### <a name="complex-queries-in-power-bi"></a>Összetett lekérdezések a Power BIban

Az összetett lekérdezések könnyebben Kusto, mint a Power Query. Ezeket a [Kusto függvényekként](/azure/kusto/query/functions)kell megvalósítani, és Power bi-ben kell meghívni őket. Ez a módszer akkor szükséges, ha a Kusto-lekérdezésben **DirectQuery** használ a `let` utasítással. Mivel Power BI két lekérdezéshez csatlakozik, és `let` utasítások nem használhatók a `join` operátorral, szintaktikai hibák jelentkezhetnek. Ezért mentse az illesztés egyes részeit Kusto-függvényként, és engedélyezze a Power BI számára a két funkció összekapcsolását.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Relatív dátum-idő operátor szimulálása

Power BI nem tartalmaz *relatív* dátum-idő operátort, például `ago()`.
`ago()`szimulálása `DateTime.FixedLocalNow()` és `#duration` Power BI függvények kombinációját használja.

A lekérdezés helyett a `ago()` operátort használja:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Használja a következő egyenértékű lekérdezést:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Kusto-lekérdezési korlátok elérése 

A Kusto lekérdezések alapértelmezés szerint legfeljebb 500 000 sort vagy 64 MB-ot adnak vissza a [lekérdezési korlátok](/azure/kusto/concepts/querylimits)című cikkben leírtak szerint. Ezeket az alapértelmezett értékeket felülbírálhatja **speciális beállításokkal** az **Azure adatkezelő (Kusto)** -kapcsolatok ablakban:

![speciális beállítások](media/power-bi-best-practices/advanced-options.png)

Ezekkel a beállításokkal a lekérdezés az alapértelmezett lekérdezési korlátok megváltoztatására vonatkozó [utasításokat állítja be](/azure/kusto/query/setstatement) :

  * **Lekérdezési eredmény rekordjának korlátozása** `set truncationmaxrecords` generálása
  * A **lekérdezési eredmények adatméretének korlátozása bájtban** `set truncationmaxsize` generál
  * **Eredményhalmaz letiltásának tiltása** `set notruncation` generálása

### <a name="using-query-parameters"></a>Lekérdezési paraméterek használata

A lekérdezési [Paraméterek](/azure/kusto/query/queryparametersstatement) használatával dinamikusan módosíthatja a lekérdezést. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Lekérdezési paraméter használata a kapcsolat részleteiben

Lekérdezési paraméter használatával szűrheti az információkat a lekérdezésben, és optimalizálhatja a lekérdezési teljesítményt.
 
A **lekérdezések szerkesztése** ablakban a **Home** > **speciális szerkesztő**

1. Keresse meg a lekérdezés következő szakaszát:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Például:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Cserélje le a lekérdezés megfelelő részét a paraméterrel. Ossza szét a lekérdezést több részre, és fűzze össze őket egy jel (&) használatával, a paraméterrel együtt.

   A fenti lekérdezésben például elvégezjük a `State == 'ALABAMA'` részt, és felosztjuk a következőre: `State == '` és `'`, és a `State` paramétert helyezjük el egymás között:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Ha a lekérdezés idézőjeleket tartalmaz, akkor kódolja azokat megfelelően. Például a következő lekérdezés: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   a következő két idézőjelkel jelenik meg a **speciális szerkesztőban** :

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   A következő lekérdezéssel kell helyettesíteni három idézőjelet:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Lekérdezési paraméter használata a lekérdezési lépésekben

Egy lekérdezési paramétert is használhat bármely olyan lekérdezési lépésben, amely támogatja azt. Például szűrheti az eredményeket egy paraméter értéke alapján.

![eredmények szűrése paraméter használatával](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Ne használja a Power BI adatfrissítési ütemező segítségével Kusto a vezérlési parancsokat

Power BI tartalmaz egy adatfrissítési ütemező, amely rendszeres időközönként lekérdezést tud kiadni egy adatforrásra vonatkozóan. Ezt a mechanizmust nem szabad felhasználni a Kusto tartozó vezérlési parancsok megadására, mert Power BI feltételezi, hogy az összes lekérdezés írásvédett.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI csak rövid (&lt;2000 karakterből álló) lekérdezéseket küldhet a Kusto

Ha a Power BI lekérdezés futtatásakor a következő hibaüzenet jelenik meg: _"DataSource. error: web. contents nem sikerült lekérni a tartalmakat..."_ a lekérdezés valószínűleg hosszabb 2000 karakternél. Power BI a **PowerQuery** használatával kérdezi le a Kusto-t egy olyan HTTP Get kérelem kibocsátásával, amely kódolja a lekérdezést a beolvasott URI azonosítójának részeként. Ezért a Power BI által kiadott Kusto-lekérdezések a kérelem URI-ja maximális hosszára korlátozódnak (2000 karakter, mínusz kis eltolás). Megkerülő megoldásként megadhat egy [tárolt függvényt](/azure/kusto/query/schema-entities/stored-functions) a Kusto-ben, és Power bi használhatja ezt a függvényt a lekérdezésben.

## <a name="next-steps"></a>Következő lépések

[Adatmegjelenítés az Azure Adatkezelő Connector használatával Power BI](power-bi-connector.md)




