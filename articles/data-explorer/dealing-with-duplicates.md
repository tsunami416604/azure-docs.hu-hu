---
title: Ismétlődő adatkezelés az Azure-ban Adatkezelő
description: Ez a témakör az Azure-Adatkezelő használatának különböző módszereit mutatja be az ismétlődő adatkezeléshez.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608226"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Ismétlődő adatkezelés az Azure-ban Adatkezelő

Az adatokat a felhőbe küldő eszközök fenntartják az adatok helyi gyorsítótárát. Az adatmérettől függően a helyi gyorsítótár napok vagy akár hónapok között is tárolhatja az adattárolást. A gyorsítótárazott adatok újraküldését és az elemzési adatbázisban az adatok ismétlődését okozó hibás eszközökről szeretné védeni az analitikai adatbázisokat. Ez a témakör az ilyen típusú forgatókönyvek ismétlődő adatok kezelésére vonatkozó ajánlott eljárásokat ismerteti.

Az adatismétlődéssel kapcsolatos legjobb megoldás megakadályozza az ismétlődést. Ha lehetséges, javítsa ki a problémát az adatfolyamatnál korábban, ami az adatfolyamattal együtt az adatátvitelhez kapcsolódó költségeket takarítja meg, és elkerüli a kiadások erőforrásait a rendszerbe betöltött ismétlődő adatmennyiséggel. Azonban olyan helyzetekben, amikor a forrásoldali rendszer nem módosítható, többféle módon is kezelheti ezt a forgatókönyvet.

## <a name="understand-the-impact-of-duplicate-data"></a>Az ismétlődő adatmennyiség következményeinek megismerése

Az ismétlődő adatmennyiség százalékos arányának figyelése. Miután felderíti az ismétlődő adatmennyiséget, elemezheti a probléma hatókörét és az üzleti hatást, és kiválaszthatja a megfelelő megoldást.

Példa lekérdezésre az ismétlődő rekordok százalékos arányának azonosításához:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Ismétlődő adatkezelési megoldások

### <a name="solution-1-dont-remove-duplicate-data"></a>Megoldás #1: Ne távolítsa el az ismétlődő elemeket

Ismerje meg az üzleti követelményeket és a duplikált adatmennyiségek tűréshatárát. Egyes adatkészletek képesek kezelni az ismétlődő adatokat egy adott százalékával. Ha a duplikált adatértéknek nincs jelentős hatása, figyelmen kívül hagyhatja a jelenlétét. Ha nem távolítja el az ismétlődő adatokat, a betöltési folyamat vagy a lekérdezési teljesítmény nem jár további terheléssel.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Megoldás #2: Ismétlődő sorok kezelése a lekérdezés során

Egy másik lehetőség az, hogy kiszűrje az adatsorok ismétlődő sorait a lekérdezés során. Az [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) összesített függvény használható az ismétlődő rekordok szűrésére és az utolsó rekord visszaküldésére az időbélyeg (vagy egy másik oszlop) alapján. Ennek a módszernek a használatának előnye a gyorsabb betöltés, mivel a lekérdezés ideje alatt a rendszer duplikálja a ismétlődést. Emellett az összes rekord (beleértve az ismétlődéseket is) elérhető a naplózáshoz és a hibaelhárításhoz. A `arg_max` függvény használatának hátránya a további lekérdezési idő és a CPU betöltése minden alkalommal, amikor az adat le van kérdezve. A lekérdezett adatmennyiségtől függően ez a megoldás nem működőképes vagy a memóriát is igénybe vehet, és más beállításokra kell váltania.

Az alábbi példában lekérdezjük az utolsó olyan rekordot, amely az egyedi rekordokat meghatározó oszlopok halmazára mutat:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Ez a lekérdezés egy függvényen belül is elhelyezhető a tábla közvetlen lekérdezése helyett:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Megoldás #3: Ismétlődések szűrése a betöltési folyamat során

Egy másik megoldás az ismétlődések szűrése a betöltési folyamat során. A Kusto táblákba való betöltés során a rendszeren figyelmen kívül hagyja az ismétlődő adatot. Az adatgyűjtés egy előkészítési táblába történik, és az ismétlődő sorok eltávolítása után egy másik táblába másolódik. A megoldás előnye, hogy a lekérdezési teljesítmény jelentősen javul az előző megoldáshoz képest. A hátrányok közé tartozik a megnövekedett betöltési idő és a további adattárolási költségek. További, ez a megoldás csak akkor működik, ha az ismétlődések nem kerülnek be egyszerre. Ha többször is ismétlődő rekordokat tartalmazó betöltések vannak, akkor a rendszer az összes betöltést elvégezheti, mivel a deduplikálás folyamata nem talál a táblában meglévő egyező rekordokat.    

A következő példa a metódust ábrázolja:

1. Hozzon létre egy másik táblát ugyanazzal a sémával:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Hozzon létre egy függvényt az ismétlődő rekordok szűréséhez, ha az új rekordokat a korábban betöltött adatokkal kívánja összekapcsolni.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Az illesztések CPU-kötésű műveletek, és további terhelést vesznek fel a rendszeren.

1. Állítsa be a [frissítési szabályzatot](/azure/kusto/management/update-policy) a `DeviceEventsUnique` táblára. A frissítési szabályzat akkor aktiválódik, amikor az új adatbevitel `DeviceEventsAll` bekerül a táblába. A Kusto motor automatikusan végrehajtja a függvényt új [egységek](/azure/kusto/management/extents-overview) létrehozásakor. A feldolgozás az újonnan létrehozott adathalmazra terjed ki. A következő parancs a forrástábla (`DeviceEventsAll`), a céltábla (`DeviceEventsUnique`) és a függvény `RemoveDuplicatesDeviceEvents` együttes használatával hozza létre a frissítési szabályzatot.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > A frissítési szabályzat meghosszabbítja a betöltés időtartamát, mivel az adatfeldolgozás során szűri az adatmennyiséget, majd `DeviceEventsAll` kétszer (a táblához és a `DeviceEventsUnique` táblához).

1. Választható Állítsa be a `DeviceEventsAll` tábla alacsonyabb adatmegőrzését, hogy ne tárolja az adatok másolatait. Válassza ki az adatmennyiségtől függően a napok számát, valamint azt, hogy mennyi ideig kívánja megőrizni a hibaelhárításhoz szükséges információkat. Beállíthatja, hogy `0d` a napok megőrzésével mentse az ELÁBÉ-t, és javítsa a teljesítményt, mivel az adatok nincs feltöltve a tárolóba.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Összegzés

Az adatismétlődés több módon is kezelhető. Alaposan értékelje ki a beállításokat, figyelembe véve a díjszabást és a teljesítményt, hogy meghatározza a vállalat megfelelő módszerét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)
