---
title: Az indexelők – Azure Search ütemezése
description: Tartalom indexelése az Azure Search-indexelők ütemezése, rendszeres időközönként vagy egy adott időpontban.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4bf931b19b7490a94f30afde49038cdc7573fab3
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302243"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Az Azure Search indexelők ütemezése
Az indexelő normális esetben lefut egyszer, azonnal a létrehozást követően. Futtathatja azt újra igény szerint a portálon, a REST API vagy a .NET SDK használatával. Beállíthatja, hogy az indexelő futtatása rendszeres ütemezés szerint.

Bizonyos helyzetekben, ahol az indexelő ütemezés hasznos:

* Idővel változni fognak a forrásadatokat, és azt szeretné, hogy az Azure Search indexelők automatikusan fel a módosított adatokat.
* Az index több adatforrásból kitöltődnek, és ellenőrizze, hogy az indexelő ütközések csökkentése érdekében különböző időpontokban futtatni szeretné.
* A forrásadatok nagyon nagy, és szeretné elosztani az indexelő idővel feldolgozása. Nagy mennyiségű adatot indexelő kapcsolatos további információkért lásd: [indexelése az Azure Search nagy méretű adatkészleteket](search-howto-large-index.md).

A scheduler az Azure Search egy beépített funkciója. Nem használhat egy külső ütemezőn search-indexelők szabályozására.

## <a name="define-schedule-properties"></a>Ütemezési tulajdonságok meghatározása

Az indexelő ütemezés két tulajdonsággal rendelkezik:
* **Intervallum**, amely megadja, mennyi ideig köztes ütemezett indexelő végrehajtások. A legkisebb megengedett időköz 5 perc, és a legnagyobb 24 óra.
* **Indítsa el a világidő (UTC)** , ami azt jelenti, hogy az első alkalommal, amikor az indexelő kell futtatni.

Megadhat egy ütemezést az indexelő első létrehozásakor vagy frissítésekor az indexelő-tulajdonságokat később. Az indexelő ütemezések lze nastavit pomocí a [portál](#portal), a [REST API-t](#restApi), vagy a [.NET SDK-val](#dotNetSdk).

Az indexelő csak egy végrehajtásának futtatható egyszerre. Ha az indexelő már fut a következő végrehajtási ütemezése, a végrehajtási van elhalaszthatók, amíg a következő ütemezett időpontban.

Vegyünk egy példát, ez részletesebb legyen. Tegyük fel, hogy úgy konfiguráljuk, hogy az indexelő ütemezés egy **időköz** óránként történik, és a egy **kezdő időpont** 2019. június 1. 8:00:00-kor (UTC): a. Íme, mi akkor fordulhat elő, amikor az indexelők futtatása egy óránál hosszabb ideig tart:

* Az első indexelő végrehajtás, vagy 2019. június 1. 8:00-kor (UTC): elindul. Tegyük fel, a végrehajtási vesz igénybe, 20 perc (vagy bármely idő kevesebb mint 1 óra).
* A második végrehajtása, vagy 2019. június 1 9:00-kor kezdődik (UTC). Tegyük fel, hogy a végrehajtási perc 70 - több mint egy óra –, és 10:10-kor (UTC) amíg nem fejeződik.
* A harmadik végrehajtását, 10:00-kor (UTC) történő futásra van ütemezve, de ekkor még mindig fut. az előző végrehajtás. Ez az ütemezett végrehajtási majd a rendszer kihagyta. Az indexelő végrehajtásának tovább nem fog elindulni, amíg 11:00-kor (UTC).

> [!NOTE]
> Ha az indexelő értéke egy meghatározott ütemezés, de ismételten sikertelen lesz, ugyanazon dokumentum és újra minden alkalommal, amikor azt fut, az indexelő megkezdődik futtatása kevésbé gyakori időközönként (akár a legfeljebb 24 óránként legalább egyszer), amíg sikeresen teszi folyamatban aga a.  Ha úgy véli, hogy bármilyen kijavítása, amely egy bizonyos ponton elakadt, az indexelő okozta a problémát, egy az igény szerinti futtatása az indexelő is végezhet, hogy sikeresen ez, és folyamatban van, az indexelő vissza fog térni a set-ütemezési időköz újra.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Ütemezés meghatározása a portálon

A portálon az adatok importálása varázsló lehetővé teszi az ütemezés egy indexelő meghatározásához a létrehozás időpontjában. Az alapértelmezett ütemezési beállítás **óránkénti**, ami azt jelenti, hogy az indexelőt egyszer fut, miután jön létre, és óránként fut le újra később.

Az ütemezési beállítást módosíthatja **után** Ha nem szeretné, hogy újra automatikusan fusson az indexelő vagy **napi** naponta egyszer futtatni. Állítsa be **egyéni** Ha azt szeretné, adja meg egy másik időközt vagy egy adott későbbi kezdő időpont.

Ha az ütemezés beállítása **egyéni**, mező jelenik meg, hogy adja meg a **időköz** és a **Start világidő (UTC)** . A legrövidebb megengedett időköz 5 perc és a leghosszabb ideje 1440 perc (24 óra).

   ![Adatok importálása varázsló beállítás indexelő ütemezés](media/search-howto-schedule-indexers/schedule-import-data.png "beállítás indexelő ütemezés az adatok importálása varázsló")

Az indexelők létrehozása után módosíthatja az ütemezési beállításokat, az indexelő szerkesztése panel használatával. Az ütemezés mezők ugyanazok, mint az adatok importálása varázsló.

   ![Az ütemezés beállítása az indexelő szerkesztése panel](media/search-howto-schedule-indexers/schedule-edit.png "indexelő szerkesztése panelen az ütemezés beállítása")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>A REST API-val ütemezés meghatározása

Meghatározhatja az ütemezésben az indexelők REST API használatával. Ehhez közé tartozik a **ütemezés** létrehozásakor vagy frissítésekor az indexelő tulajdonság. Az alábbi példában látható egy PUT kérelmet egy meglévő indexelő frissítése:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

A **időköz** paraméter megadása kötelező. Az intervallum hivatkozik a két egymást követő indexelő végrehajtásának kezdete közötti idő. A legkisebb megengedett intervallum értéke 5 perc; a leghosszabb érték egy nap. Egy XSD "nyelv szerinti dayTimeDuration" értékként kell formázni (korlátozott részhalmaza olyan [ISO 8601 időtartama](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). A minta: `P(nD)(T(nH)(nM))`. Példák: `PT15M` 15 percenként, a `PT2H` 2 óránként számára.

A választható **startTime** azt jelzi, hogy mikor kell elkezdeni az ütemezett végrehajtás. Ha ezt kihagyja, az aktuális UTC időpontig szolgál. Lehet, hogy az idő, amelyben az első végrehajtás eset van ütemezve, mintha az indexelő futása során folyamatosan óta az eredeti korábban **startTime**.

Az indexelő igény szerint bármely szoftvertermékére, az indexelő futtatása hívás használatával is futtathatja. Az indexelők futtatása és az indexelő ütemezések beállítása kapcsolatos további információkért lásd: [indexelő futtatása](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [első indexelő](https://docs.microsoft.com/rest/api/searchservice/get-indexer), és [frissítés indexelő](https://docs.microsoft.com/rest/api/searchservice/update-indexer) a REST API-referencia a.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>A .NET SDK használatával ütemezés meghatározása

Megadhatja az ütemezés egy indexelő az Azure Search .NET SDK használatával. Ehhez közé tartozik a **ütemezés** létrehozásakor vagy frissítésekor az indexelő tulajdonság.

A következő C# példa létrehoz egy előre definiált adatforrás és az index, indexelő, és beállítja az ütemezett kezdési 30 perc múlva minden naponta egyszer futtatni:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Ha a **ütemezés** paraméter nincs megadva, az indexelő csak akkor fog futni egyszer a létrehozást követően azonnal.

A **startTime** paraméter értékre lehet beállítani egyszerre a múltban. Ebben az esetben az első végrehajtás van ütemezve, mintha az indexelő futása során folyamatosan óta az adott **startTime**.

Az ütemezés használatával van definiálva a [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) osztály. A **IndexingSchedule** konstruktor van szükség egy **időköz** használatával megadott paraméter egy **TimeSpan** objektum. A legkisebb időköz érték 5 perc, és a legnagyobb 24 óra. A második **startTime** paramétert, mint a megadott egy **DateTimeOffset** objektumazonosító, nem kötelező.

A .NET SDK használatával vezérlő indexelőműveletek teszi lehetővé a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) osztály és annak [indexelők](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) tulajdonság, amely megvalósítja a metódusok a **IIndexersOperations**felületet. 

Az indexelő segítségével bármikor futtathatja a [futtatása](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), vagy [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) módszereket.

Létrehozásával kapcsolatos további információkért frissítése, és futtatja az indexelők, lásd: [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
