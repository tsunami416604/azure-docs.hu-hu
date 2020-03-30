---
title: Gyakorlati tanácsok az Azure Data Explorer adatainak lekérdezéséhez és megjelenítéséhez a Power BI használatával
description: Ebben a cikkben gyakorlati tanácsok a Power BI használatával az Azure Data Explorer adatainak lekérdezéséhez és megjelenítéséhez.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251738"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Gyakorlati tanácsok az Azure Data Explorer adatainak lekérdezéséhez és megjelenítéséhez a Power BI használatával

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. [A Power BI](https://docs.microsoft.com/power-bi/) egy üzleti elemzési megoldás, amely lehetővé teszi az adatok megjelenítését és az eredmények megosztását a szervezeten belül. Az Azure Data Explorer három lehetőséget kínál az adatokhoz való csatlakozásra a Power BI-ban. Használja a [beépített összekötőt,](power-bi-connector.md) [importáljon lekérdezést az Azure Data Explorerből a Power BI-ba,](power-bi-imported-query.md)vagy használjon [SQL-lekérdezést](power-bi-sql-query.md). Ez a cikk tippeket ad az Azure Data Explorer-adatok Power BI-val történő lekérdezéséhez és megjelenítéséhez. 

## <a name="best-practices-for-using-power-bi"></a>Gyakorlati tanácsok a Power BI használatához 

Ha több terabájtnyi friss nyers adattal dolgozik, kövesse az alábbi irányelveket, hogy a Power BI irányítópultjai és jelentései lendületesek és naprakészek maradjanak:

* **Utazási fény** – Csak a jelentésekhez szükséges adatokat hozza a Power BI-ba. A mély interaktív elemzéshez használja az [Azure Data Explorer web felhasználói felületét,](web-query-data.md) amely a d-hoc feltárásra van optimalizálva a Kusto lekérdezési nyelvvel.

* **Összetett modell** – [Összetett modell](https://docs.microsoft.com/power-bi/desktop-composite-models) használatával kombinálhatja a legfelső szintű irányítópultok összesített adatait a szűrt működési nyers adatokkal. Egyértelműen meghatározhatja, hogy mikor és mikor használjon összesített nézetet. 

* **Importálási mód és DirectQuery mód** – **Importálási** mód használata kisebb adatkészletek interakciójához. A **DirectQuery** módot nagyméretű, gyakran frissített adatkészletek esetén használhatja. Létrehozhat például dimenziótáblákat **importálási** módban, mivel kicsik, és nem változnak gyakran. Állítsa be a frissítési időközt az adatfrissítések várható gyakoriságának megfelelően. Ténytáblák létrehozása **DirectQuery** módban, mivel ezek a táblák nagyok és nyers adatokat tartalmaznak. Ezekkel a táblákkal szűrt adatokat jeleníthet meg a Power BI [részletező használatával.](https://docs.microsoft.com/power-bi/desktop-drillthrough)

* **Párhuzamosság** – Az Azure Data Explorer egy lineárisan skálázható adatplatform, ezért javíthatja az irányítópult-renderelés teljesítményét a végpontok közötti folyamat párhuzamosságának növelésével az alábbiak szerint:

   * Az egyidejű kapcsolatok számának növelése a [DirectQuery ben a Power BI-ban.](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)

   * Használjon [gyenge konzisztenciát a párhuzamosság javítására](/azure/kusto/concepts/queryconsistency). Ez hatással lehet az adatok frissességére.

* **Hatékony szeletelők** – A [szinkronizáló szeletelők](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) segítségével megakadályozhatja, hogy a jelentések adatokat töltsenek be, mielőtt készen állna. Miután strukturálta az adatkészletet, elhelyezheti az összes vizualizációt, és megjelölte az összes szeletelőt, kiválaszthatja a szinkronizálási szeletelőt, hogy csak a szükséges adatokat töltse be.

* **Szűrők használata** – A lehető legtöbb Power BI-szűrő használatával az Azure Data Explorer-keresés a megfelelő adatszegmensekre összpontosíthat.

* **Hatékony vizualizációk** – Válassza ki az adatokleg legeredményesebb vizualizációit.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tippek az Azure Data Explorer-összekötő használata a Power BI számára az adatok lekérdezéséhez

Az alábbi szakasz tippeket és trükköket tartalmaz a Kusto lekérdezési nyelvének A Power BI használatával kapcsolatban. Adatok megjelenítéséhez használja az [Azure Data Explorer power bi-összekötőjét](power-bi-connector.md)

### <a name="complex-queries-in-power-bi"></a>Összetett lekérdezések a Power BI-ban

Összetett lekérdezések könnyebben fejezhető ki Kusto, mint a Power Query. Ezeket [Kusto-függvényként](/azure/kusto/query/functions)kell végrehajtani, és meg kell hívni a Power BI-ban. Erre a módszerre akkor `let` van szükség, ha a **DirectQueryt** a Kusto-lekérdezésben lévő utasításokkal használja. Mivel a Power BI két `let` lekérdezést egyesít, és `join` az utasítások nem használhatók az operátorral, szintaktikai hibák léphetnek fel. Ezért mentse az illesztés minden egyes részét Kusto függvényként, és hagyja, hogy a Power BI összekapcsolja ezt a két funkciót.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Relatív dátum-idő operátor szimulálása

A Power BI nem tartalmaz *relatív* dátum-idő operátort, például `ago()`.
Szimulálni, `ago()`használja `DateTime.FixedLocalNow()` a `#duration` kombinációt és a Power BI-függvényeket.

A lekérdezés helyett `ago()` az operátor használata:

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

### <a name="reaching-kusto-query-limits"></a>A Kusto lekérdezési korlátok elérése 

A Kusto-lekérdezések alapértelmezés szerint legfeljebb 500 000 sort vagy 64 MB-ot adnak vissza a [lekérdezési korlátokban leírtak szerint.](/azure/kusto/concepts/querylimits) Ezeket az alapértelmezett beállításokat az **Azure Data Explorer (Kusto)** kapcsolatablakának Speciális **beállításaival** felülbírálhatja:

![speciális beállítások](media/power-bi-best-practices/advanced-options.png)

Ezek a beállítások a lekérdezéssel kapcsolatos [utasítások beállításával módosítják](/azure/kusto/query/setstatement) az alapértelmezett lekérdezési korlátokat:

  * **A lekérdezés eredményrekordszámának korlátozása** létrehoz egy`set truncationmaxrecords`
  * **A lekérdezési eredmény adatméretének korlátozása bájtban** létrehoz egy`set truncationmaxsize`
  * **Az eredményhalmaz csonkolásának letiltása**`set notruncation`

### <a name="using-query-parameters"></a>Lekérdezési paraméterek használata

A [lekérdezési paraméterek](/azure/kusto/query/queryparametersstatement) segítségével dinamikusan módosíthatja a lekérdezést. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Lekérdezési paraméter használata a kapcsolat részleteiben

Lekérdezési paraméter használatával szűrheti a lekérdezés ben lévő információkat, és optimalizálhatja a lekérdezés teljesítményét.
 
A **Lekérdezések szerkesztése** ablakban a **Kezdőlap** > **– Speciális szerkesztő**

1. Keresse meg a lekérdezés következő szakaszát:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Példa:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Cserélje le a lekérdezés megfelelő részét a paraméterre. Ossza fel a lekérdezést több részre, és fűzze össze őket egy ampersand (&) használatával a paraméterrel együtt.

   Például a fenti lekérdezésben vesszük `State == 'ALABAMA'` a részt, és `State == '` felosztjuk a következőkre: és `'` a `State` paramétert helyezzük el közöttük:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Ha a lekérdezés idézőjeleket tartalmaz, megfelelően kódolja őket. Például a következő lekérdezés: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   az Advanced **Editor** az alábbiak szerint jelenik meg két idézőjellel:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   A következő lekérdezést három idézőjelre kell cserélni:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Lekérdezési paraméter használata a lekérdezési lépésekben

A lekérdezési paraméter bármely lekérdezési lépésben használható, amely támogatja azt. Szűrje például az eredményeket egy paraméter értéke alapján.

![eredmények szűrése paraméter használatával](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Ne használja a Power BI adatfrissítési ütemezőjét vezérlőparancsok kiadására a Kusto számára

A Power BI tartalmaz egy adatfrissítési ütemezőt, amely rendszeres időközönként lekérdezéseket tud kiadni egy adatforráshoz. Ez a mechanizmus nem használható a vezérlőparancsok Kusto-ra ütemezésére, mert a Power BI feltételezi, hogy minden lekérdezés írásvédett.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>A Power BI csak&lt;rövid (2000 karakter) lekérdezéseket tud küldeni a Kusto-nak

Ha egy lekérdezés futtatása a Power BI-ban a következő hibát eredményezi: _"DataSource.Error: Web.Contents nem tudta beszerezni a tartalmat..."_ a lekérdezés valószínűleg hosszabb, mint 2000 karakter. A Power BI a **PowerQuery** segítségével lekérdezi a Kusto-t egy HTTP GET-kéréssel, amely a lekérdezést a beolvasott URI részeként kódolja. Ezért a Power BI által kiadott Kusto-lekérdezések a kérelem URI-jának maximális hosszára korlátozódnak (2000 karakter, mínusz kis eltolás). Kerülő megoldásként definiálhat egy [tárolt függvényt](/azure/kusto/query/schema-entities/stored-functions) a Kusto-ban, és a Power BI használhatja ezt a függvényt a lekérdezésben.

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése az Azure Data Explorer-összekötő használatával a Power BI-hoz](power-bi-connector.md)




