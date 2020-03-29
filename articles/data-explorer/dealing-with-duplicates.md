---
title: Ismétlődő adatok kezelése az Azure Data Explorerben
description: Ez a témakör az Azure Data Explorer használatakor különböző megközelítéseket jelenít meg az ismétlődő adatok kezelésére.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608226"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Ismétlődő adatok kezelése az Azure Data Explorerben

A felhőbe adatokat küldő eszközök az adatok helyi gyorsítótárát tartják fenn. Az adatok méretétől függően a helyi gyorsítótár napokig vagy akár hónapokig is tárolhatja az adatokat. Meg szeretné védeni az analitikus adatbázisokat a hibás anamandós eszközöktől, amelyek újraküldik a gyorsítótárazott adatokat, és adatátfedéseket okoznak az analitikus adatbázisban. Ez a témakör az ilyen típusú forgatókönyvek ismétlődő adatainak kezelésével kapcsolatos gyakorlati tanácsokat ismerteti.

Az adatmásolás legjobb megoldása a párhuzamosságok megakadályozása. Ha lehetséges, javítsa ki a problémát korábban az adatfolyamat, amely megtakarítja az adatfolyamat mentén történő adatáthelyezéssel kapcsolatos költségeket, és elkerüli az erőforrások elköltését a rendszerbe betöltött ismétlődő adatok kal. Azonban olyan helyzetekben, amikor a forrásrendszer nem módosítható, számos módon lehet kezelni ezt a forgatókönyvet.

## <a name="understand-the-impact-of-duplicate-data"></a>Az ismétlődő adatok hatásának megismerése

Az ismétlődő adatok százalékos arányának figyelése. Az ismétlődő adatok százalékos arányának felfedezése után elemezheti a probléma hatókörét és az üzleti hatást, és kiválaszthatja a megfelelő megoldást.

Mintalekérdezés az ismétlődő rekordok százalékos arányának azonosítására:

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

## <a name="solutions-for-handling-duplicate-data"></a>Megoldások ismétlődő adatok kezelésére

### <a name="solution-1-dont-remove-duplicate-data"></a>Megoldás #1: Ne távolítson el ismétlődő adatokat

Ismerje meg az üzleti követelményeket és az ismétlődő adatok tűréshatárát. Egyes adatkészletek az ismétlődő adatok bizonyos százalékával kezelhetők. Ha a duplikált adatok nak nincs jelentős hatása, figyelmen kívül hagyhatja a jelenlétét. Az az előnye, hogy nem távolítja el az ismétlődő adatokat, nincs további többletterhelés a betöltési folyamatra vagy a lekérdezés teljesítményére.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Megoldás #2: Ismétlődő sorok kezelése lekérdezés közben

Egy másik lehetőség az adatok ismétlődő sorainak kiszűrése lekérdezés közben. Az [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) összesített függvény segítségével kiszűrheti az ismétlődő rekordokat, és az utolsó rekordot az időbélyeg (vagy egy másik oszlop) alapján adja vissza. Ennek a módszernek az előnye a gyorsabb betöltés, mivel a de-párhuzamosságok lekérdezési idő alatt következnek be. Ezenkívül az összes rekord (beleértve az ismétlődéseket is) rendelkezésre áll naplózásra és hibaelhárításra. A függvény használatának hátránya a `arg_max` további lekérdezési idő és terhelés a CPU-n minden alkalommal, amikor az adatok lekérdezése történik. A lekérdezett adatok mennyiségétől függően ez a megoldás nem lesz működőképes vagy memóriaigényes, és más beállításokra kell váltania.

A következő példában lekérdezzük az egyedi rekordokat meghatározó oszlopok utolsó rekordját:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Ez a lekérdezés függvényen belül is elhelyezhető a tábla közvetlen lekérdezése helyett:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Megoldás #3: A betöltési folyamat során a szűrőismétlődések

Egy másik megoldás az ismétlődések szűrése a betöltési folyamat során. A rendszer figyelmen kívül hagyja az ismétlődő adatokat a Kusto táblákba való betöltés során. Az adatokat a rendszer egy átmeneti táblába viszi, és az ismétlődő sorok eltávolítása után egy másik táblába másolja. Ennek a megoldásnak az az előnye, hogy a lekérdezési teljesítmény jelentősen javul az előző megoldáshoz képest. A hátrányok közé tartozik a megnövekedett betöltési idő és a további adattárolási költségek. Emellett ez a megoldás csak akkor működik, ha a párhuzamosságok nem egyidejűleg történnek. Ha több egyidejű betöltés van ismétlődő rekordokat tartalmazó, akkor az összes betöltésre kerülhet sor, mivel a deduplikációs folyamat nem talál egyező rekordokat a táblában.    

A következő példa ezt a módszert ábrázolja:

1. Hozzon létre egy másik táblát ugyanazzal a sémával:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Hozzon létre egy függvényt az ismétlődő rekordok kiszűréséhez az új rekordok nak a korábban bevitt rekordokkal való csatlakozása elleni kapcsolattal.

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
    > Az illesztések cpu-hoz kötött műveletek, és további terhelést adnak hozzá a rendszerhez.

1. Állítsa be `DeviceEventsUnique` a [frissítési házirendet](/azure/kusto/management/update-policy) az asztalra. A frissítési házirend akkor aktiválódik, `DeviceEventsAll` amikor új adatok kerülnek a táblába. A Kusto motor automatikusan végrehajtja a funkciót, ahogy új [kiterjedések](/azure/kusto/management/extents-overview) jönnek létre. A feldolgozás hatóköre az újonnan létrehozott adatokra terjed ki. A következő parancs összefűzi`DeviceEventsAll`a forrástáblát ( ), a céltáblát (`DeviceEventsUnique`), valamint a függvényt `RemoveDuplicatesDeviceEvents` a frissítési házirend létrehozásához.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > A frissítési házirend meghosszabbítja a betöltés időtartamát, mivel az adatok szűrése a `DeviceEventsAll` betöltés `DeviceEventsUnique` során, majd kétszer (a táblához és a táblához) történik.

1. (Nem kötelező) Az adatok másolatainak `DeviceEventsAll` tárolásának elkerülése érdekében állítson be alacsonyabb adatmegőrzést a táblában. Adja meg a napok számát az adatmennyiségtől és a hibaelhárításhoz szükséges adatok megőrzésének időtartamától függően. Beállíthatja, hogy `0d` napok megőrzése az ELÁBÉ mentése és a teljesítmény javítása érdekében, mivel az adatok nem töltődnek fel a tárolóba.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Összefoglalás

Az adatmásolás többféleképpen kezelhető. Értékelje ki a lehetőségeket gondosan, figyelembe véve az árat és a teljesítményt, hogy meghatározza a megfelelő módszert a te dolgod.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)
