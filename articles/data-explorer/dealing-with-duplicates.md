---
title: Az Azure Data Explorer duplikált adatok kezelése
description: Ez a témakör bemutatja, különböző megközelítések a duplikált adatok foglalkozik, amikor az Azure az adatkezelő segítségével.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045781"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Az Azure Data Explorer duplikált adatok kezelése

A felhőbe történő adatküldés eszközök kezelése az adatok a helyi gyorsítótárat. Az adatok méretétől függően a helyi gyorsítótár sikerült tárolja az adatokat napokat vagy akár hónapok. Küldje el újra a gyorsítótárazott adatok, és az analitikai adatbázis okozhat az adatdeduplikáció nem megfelelően működő eszközökre az analitikai adatbázisok védelme érdekében szeretné. Ez a témakör ismerteti az ajánlott eljárások az ilyen típusú forgatókönyvek duplikált adatok kezelésének alapelveit.

A legjobb megoldás az adatdeduplikáció megakadályozza, hogy az ismétlődést. Ha lehetséges az adatok folyamatot, amely mentén adatfolyamat adatáthelyezés társított költségek menti, és elkerülhető az erőforrásokat fordítania a duplikált adatok betöltődnek a rendszer másolásának menetét ismerheti meg a korábbi a probléma megoldásához. Azonban az olyan esetekben, ahol nem lehet módosítani a forrásrendszerben, különböző módja van ebben a forgatókönyvben kezelésére.

## <a name="understand-the-impact-of-duplicate-data"></a>A duplikált adatok hatását ismertetése

Figyelheti a duplikált adatok aránya. Százalékos aránya a duplikált adatok felderítése, után elemezheti a problémát, és üzleti hatás hatókörét, és válassza ki a megfelelő megoldást.

Mintalekérdezés ismétlődő rekordok aránya azonosításához:

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

## <a name="solutions-for-handling-duplicate-data"></a>Ismétlődő adatok kezelésének alapelveit megoldások

### <a name="solution-1-dont-remove-duplicate-data"></a>#1. megoldás: Ne távolítsa el a duplikált adatok

Ismerje meg, az üzleti követelmények és a duplikált adatok hibatűrésének. Bizonyos adatkészletek és a duplikált adatok bizonyos százalék kezelheti. Ha a duplikált adatok az jelentős hatással nem rendelkezik, figyelmen kívül hagyhatja a jelenléte. A nem távolítja el a duplikált adatok előnye a betöltési folyamat és a lekérdezések teljesítményének a nincs szükség további erőforrásokra.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>#2. megoldás: Ismétlődő sorok kezelésére lekérdezési során

Egy másik lehetőség, hogy az ismétlődő sorok az adatok kiszűréséhez lekérdezés során. A [ `arg_max()` ](/azure/kusto/query/arg-max-aggfunction) összesített függvény használható kiszűrhetők az ismétlődő rekordok, és az időbélyegző (vagy egy másik oszlopot) alapuló utolsó rekordját adja vissza. Ezzel a módszerrel előnye gyorsabb adatfeldolgozást, mivel a duplikátumok lekérdezési idő során történik. Emellett minden rekord (a duplikáltakat is beleértve) érhetők el a naplózás és hibaelhárítás. A hátránya, hogy használatával a `arg_max` függvény egy további lekérdezési idő- és a Processzor terhelése minden alkalommal, amikor a rendszer lekéri az adatokat. A lekérdezett adatok mennyiségétől függően ez a megoldás nem működik vagy memória-igényes válhat, és a szükséges átváltása más beállítások.

A következő példában betöltött oszlopkészleteket, amelyek meghatározzák az egyedi bejegyzések utolsó rekordját azt lekérdezés:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Ez a lekérdezés is elhelyezhető közvetlenül kérdez le a táblázat helyett egy függvényen belül:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>#3. megoldás: Ismétlődő szűrése a betöltési folyamat során

Egy másik megoldás, ha ismétlődő szűrése a betöltési folyamat során. A rendszer figyelmen kívül hagyja a a duplikált adatok során Adatbetöltési Kusto-táblákba. Adatok betöltődnek az előkészítési táblába, és átmásolja egy másik tábla az ismétlődő sorok eltávolítása után. Ez a megoldás előnye, hogy a lekérdezési teljesítmény jelentős mértékben javítja szemben a korábbi megoldás. A hátrányait közé tartozik a megnövekedett betöltési időt és további tárolási költségeket.

A következő példa ezt a módszert ábrázolja:

1. Hozzon létre egy másik tábla ugyanazzal a sémával:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Hozzon létre egy függvényt, az ismétlődő rekordok által kiszűréséhez elleni volt az új bejegyzések a korábban betöltött olyanokra cserélni.

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
    > Illesztések processzorigénye műveletek, és adja hozzá a további terhelést a rendszeren.

1. Állítsa be [frissítési szabályzat](/azure/kusto/management/update-policy) a `DeviceEventsUnique` tábla. A frissítési szabályzat akkor aktiválódik, amikor a hiányzóra változik az új adatok a `DeviceEventsAll` tábla. A Kusto-motor automatikusan végrehajtja a függvényt, új [egységek](/azure/kusto/management/extents-overview) jönnek létre. A feldolgozás az újonnan létrehozott adatok hatókörét. A következő parancsot a forrástábla összefűzi (`DeviceEventsAll`), céltábla (`DeviceEventsUnique`), és a függvény `RemoveDuplicatesDeviceEvents` a frissítési szabályzat létrehozása.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Frissítési szabályzat kibővíti az Adatbetöltési időtartama, mivel az adatok betöltési során szűrve és majd kétszer betöltött (az a `DeviceEventsAll` táblát, és a `DeviceEventsUnique` tábla).

1. (Nem kötelező) Egy alacsonyabb adatmegőrzés beállítani a `DeviceEventsAll` tábla elkerülése érdekében az adatok másolatainak tárolásához. Válassza ki az adatmennyiség függően napok száma, és mennyi ideig szeretné megőrizni az adatokat a hibaelhárításhoz. Beállíthatja `0d` napos megőrzés COGS mentéséhez, és javítható a teljesítmény, mivel az adatok tárolási nem feltöltött.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Összegzés

Adatdeduplikáció többféle módon lehet kezelni. Értékelje ki a beállításokat, gondosan meg, hogy a megfelelő módszert a vállalati fiók ára és teljesítménye, figyelembe véve.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Data Explorer lekérdezéseket írni](write-queries.md)
