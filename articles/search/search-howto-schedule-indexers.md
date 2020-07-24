---
title: Indexelő végrehajtásának ütemterve
titleSuffix: Azure Cognitive Search
description: Ütemezze az Azure Cognitive Search indexelő programját, hogy rendszeres időközönként vagy adott időpontokban indexelje a tartalmat.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 4a78c85918725533df8c616e598afbd2ad84bdd5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038511"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Indexelő ütemezhetnek az Azure Cognitive Search

Az indexelő általában egyszer fut, közvetlenül a létrehozása után. Igény szerint újra futtathatja a portál, a REST API vagy a .NET SDK használatával. Az indexelő konfigurálható úgy is, hogy rendszeres időközönként fusson az ütemterven.

Bizonyos helyzetekben hasznos lehet az indexelő ütemezése:

* A forrásadatok idővel változnak, és azt szeretné, hogy az Azure Cognitive Search indexelő automatikusan feldolgozzák a módosított adatforrásokat.
* Az index több adatforrásból lesz feltöltve, és biztosítani szeretné, hogy az indexelő a konfliktusok csökkentése érdekében különböző időpontokban fussanak.
* A forrásadatok nagyon nagy méretűek, és az indexelő feldolgozását az idő múlásával szeretné elosztani. A nagy mennyiségű adat indexelésével kapcsolatos további információkért lásd: [nagyméretű adatkészletek indexelése az Azure Cognitive Searchban](search-howto-large-index.md).

Az ütemező az Azure Cognitive Search beépített funkciója. A keresési indexelő vezérléséhez nem használhat külső ütemező funkciót.

## <a name="define-schedule-properties"></a>Ütemterv tulajdonságainak megadása

Az indexelő ütemtervének két tulajdonsága van:
* **Intervallum**, amely meghatározza az ütemezett indexelő végrehajtások közötti időt. A legkisebb megengedett intervallum 5 perc, a legnagyobb pedig 24 óra.
* **Kezdési időpont (UTC)**, amely azt jelzi, hogy az indexelő Mikor fusson először.

Az indexelő első létrehozásakor megadhat egy ütemtervet, vagy később is frissítheti az indexelő tulajdonságait. Az indexelő-ütemtervek a [portál](#portal), a [REST API](#restApi)vagy a [.net SDK](#dotNetSdk)használatával állíthatók be.

Egyszerre csak egy indexelő végrehajtása futhat. Ha egy indexelő már fut a következő végrehajtás ütemezésekor, a végrehajtás a következő ütemezett időpontig Elhalasztva lesz.

Vegyünk egy példát erre a konkrétra. Tegyük fel, hogy az indexelő-ütemtervet **óránként, a** 2019-as és a 8:00:00-es UTC-kor számított 1. június 1-től **kezdődően** konfiguráljuk. Ez akkor fordulhat elő, ha egy indexelő futtatása egy óránál hosszabb időt vesz igénybe:

* Az első indexelő végrehajtása a 2019-as vagy az 8:00-os UTC-kor kezdődik. Feltételezzük, hogy ez a végrehajtás 20 percet vesz igénybe (vagy 1 óránál rövidebb ideig).
* A második végrehajtás 2019 9:00 AM UTC vagy újabb időpontban kezdődik. Tegyük fel, hogy ez a végrehajtás 70 percet vesz igénybe – több mint egy óra –, és a 10:10-es UTC-ig nem fog elindulni.
* A harmadik végrehajtás a következő időpontban indul: 10:00 UTC, de az előző végrehajtás még mindig fut. Ezt követően az ütemezett végrehajtás ki lesz hagyva. Az indexelő következő végrehajtása nem indul el a 11:00 UTC-ig.

> [!NOTE]
> Ha egy indexelő egy bizonyos ütemezésre van beállítva, de többször is meghibásodik ugyanazon a dokumentumon, és minden egyes futtatásakor újra leáll, az indexelő egy ritkábban (legfeljebb legalább 24 óránként) fog futni, amíg a folyamat újból el nem végzi a műveletet.  Ha úgy gondolja, hogy rögzítette a problémát, amely miatt az indexelő egy bizonyos ponton elakadt, igény szerint futtathatja az Indexelő szolgáltatást, és ha a folyamat sikeresen elvégezte az előrehaladást, az indexelő az ütemezési intervallumba ismét visszatér.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Ütemterv a portálon

A portálon az adatimportálás varázsló lehetővé teszi az indexelő ütemezésének definiálását a létrehozási időpontnál. Az alapértelmezett ütemterv-beállítás **óránként**történik, ami azt jelenti, hogy az indexelő a létrehozása után egyszer fut, és ezt követően minden órában fut.

**Ha nem** szeretné, hogy az indexelő automatikusan fusson, vagy **naponta** egyszer fusson, módosítsa az ütemterv beállítását. Állítsa be az **Egyéni** értékre, ha más intervallumot vagy adott jövőbeli kezdési időt szeretne megadni.

Ha az ütemtervet **Egyéni**értékre állítja, a mezők úgy jelennek meg, hogy megadják az **intervallumot** és a **kezdési időpontot (UTC)**. A legrövidebb időtartam 5 perc, a leghosszabb pedig 1440 perc (24 óra).

   ![Az indexelő ütemtervének beállítása az adatimportálás varázslóban](media/search-howto-schedule-indexers/schedule-import-data.png "Az indexelő ütemtervének beállítása az adatimportálás varázslóban")

Az indexelő létrehozása után az indexelő szerkesztési paneljén módosíthatja az ütemterv beállításait. Az ütemterv mezői megegyeznek az adat importálása varázslóban.

   ![Az ütemterv beállítása az indexelő szerkesztési paneljén](media/search-howto-schedule-indexers/schedule-edit.png "Az ütemterv beállítása az indexelő szerkesztési paneljén")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Ütemezett REST API-k használatával

Az indexelő ütemtervét az REST API használatával adhatja meg. Ehhez adja meg a **Schedule** tulajdonságot az indexelő létrehozásakor vagy frissítésekor. Az alábbi példa egy PUT-kérelmet mutat be egy meglévő indexelő frissítéséhez:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Az **intervallum** paraméter megadása kötelező. Az intervallum a két egymást követő indexelő végrehajtásának kezdete közötti időpontra utal. A legkisebb megengedett intervallum 5 perc; a leghosszabb egy nap. A fájlnak XSD "dayTimeDuration" értéknek kell lennie (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) értékének korlátozott részhalmaza). A minta ehhez a következő: `P(nD)(T(nH)(nM))` . Példák: `PT15M` 15 percenként, `PT2H` minden 2 órában.

Az opcionális kezdési **időpont** azt jelzi, hogy mikor kell megkezdeni az ütemezett végrehajtást. Ha nincs megadva, a rendszer az aktuális UTC-időt használja. Ez az idő lehet a múltban, amely esetben az első végrehajtás ütemezve van, mintha az indexelő az eredeti **kezdő időpont**óta folyamatosan fut.

Az indexelő igény szerint bármikor futtatható a Run indexelő hívásával is. További információ az indexelő futtatásáról és az indexelő-ütemtervek beállításáról: [Indexelő futtatása](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Indexelő letöltése](https://docs.microsoft.com/rest/api/searchservice/get-indexer)és az [Indexelő frissítése](https://docs.microsoft.com/rest/api/searchservice/update-indexer) a REST API-referenciában.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Ütemterv a .NET SDK használatával

Az indexelő ütemtervét az Azure Cognitive Search .NET SDK használatával határozhatja meg. Ehhez az indexelő létrehozásakor vagy frissítésekor adja meg az **Schedule** tulajdonságot.

A következő C#-példa létrehoz egy indexelő egy előre definiált adatforrás és index használatával, és az ütemtervet úgy állítja be, hogy minden nap 30 perc múlva fusson:

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
Ha az **ütemterv** paraméter nincs megadva, az indexelő csak a létrehozás után azonnal fut.

A **kezdő időpont** paraméter a múltbeli időpontra állítható be. Ebben az esetben az első végrehajtás úgy van ütemezve, hogy az indexelő a megadott **kezdő időpont**óta folyamatosan fusson.

Az ütemterv a [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) osztály használatával van definiálva. A **IndexingSchedule** konstruktorhoz egy **TimeSpan** objektum használatával megadott **intervallum** -paraméter szükséges. A legkisebb megengedett érték 5 perc, a legnagyobb pedig 24 óra. A **DateTimeOffset** objektumként megadott második **kezdő időpont** paraméter nem kötelező.

A .NET SDK lehetővé teszi az indexelő műveletek vezérlését a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) osztály és az [Indexelő](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) tulajdonság használatával, amely metódusokat valósít meg a **IIndexersOperations** felületéről. 

Az indexelő igény szerint bármikor futtatható a [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), a [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)vagy a [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) metódusok egyikének használatával.

Az indexelő létrehozásáról, frissítéséről és futtatásáról további információt a következő témakörben talál: [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
