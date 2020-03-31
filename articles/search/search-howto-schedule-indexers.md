---
title: Indexelő végrehajtásának ütemezése
titleSuffix: Azure Cognitive Search
description: Ütemezze az Azure Cognitive Search indexelőit, hogy rendszeresen vagy meghatározott időpontokban indexeljék a tartalmakat.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112948"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Indexelők ütemezése az Azure Cognitive Search szolgáltatásban

Az indexelő általában egyszer fut, közvetlenül a létrehozása után. Igény szerint újra futtathatja a portálon, a REST API-n vagy a .NET SDK-n keresztül. Az indexelőt beállíthatja úgy is, hogy rendszeresidőközönként, ütemezés szerint fusson.

Néhány olyan helyzet, amikor az indexelő ütemezése hasznos:

* A forrásadatok idővel változnak, és azt szeretné, hogy az Azure Cognitive Search indexelői automatikusan feldolgozzák a módosított adatokat.
* Az index több adatforrásból lesz feltöltve, és győződjön meg arról, hogy az indexelők különböző időpontokban futnak az ütközések csökkentése érdekében.
* A forrásadatok nagyon nagyok, és az indexelő feldolgozását az idő múlásával el szeretné terjeszteni. A nagy mennyiségű adat indexeléséről a [Nagy adatkészletek indexelése az Azure Cognitive Search alkalmazásban](search-howto-large-index.md)című témakörben olvashat bővebben.

Az ütemező az Azure Cognitive Search beépített szolgáltatása. Külső ütemező vel nem szabályozhatja a keresési indexelők.

## <a name="define-schedule-properties"></a>Ütemezési tulajdonságok megadása

Az indexelő ütemezése két tulajdonsággal rendelkezik:
* **Intervallum**, amely meghatározza az ütemezett indexelő-végrehajtások közötti időt. A legkisebb engedélyezett intervallum 5 perc, a legnagyobb pedig 24 óra.
* **Kezdési idő (UTC),** amely azt jelzi, hogy először kell futtatni az indexelőt.

Megadhatja az ütemezést az indexelő első létrehozásakor, vagy az indexelő tulajdonságainak későbbi frissítésével. Az indexelő ütemezése a [portálon](#portal), a [REST API-n](#restApi)vagy a [.NET SDK-n](#dotNetSdk)állítható be.

Egyszerre csak egy indexelő végrehajtása futtatható. Ha egy indexelő már fut, amikor a következő végrehajtás van ütemezve, hogy a végrehajtás elhalasztható a következő ütemezett időpontra.

Vegyünk egy példát, hogy ez konkrétabb. Tegyük fel, hogy konfigurálunk egy indexelő **Start Time** ütemezést óránkénti **időközzel** és 2019. A következőképpen történhet, ha egy indexelő futtatása egy óránál tovább tart:

* Az első indexelő végrehajtás 2019. Tegyük fel, hogy ez a végrehajtás 20 percet vesz igénybe (vagy bármikor kevesebb, mint 1 óra).
* A második végrehajtás 2019. június 1-jén vagy környékén kezdődik 9:00 ÓRA UTC. Tegyük fel, hogy ez a végrehajtás 70 percet vesz igénybe – több mint egy órát –, és csak 10:10-kor (UTC) fejeződik be.
* A harmadik végrehajtás a tervek szerint 10:00 UTC-kor kezdődik, de abban az időben az előző végrehajtás még mindig fut. Ezt az ütemezett végrehajtást ezután kihagyja. Az indexelő következő végrehajtása csak 11:00-kor indul UTC szerint.

> [!NOTE]
> Ha egy indexelő egy bizonyos ütemezésre van beállítva, de minden egyes futtatáskor ismételten sikertelen ugyanazon a dokumentumon, az indexelő kevésbé gyakori időközönként (legfeljebb 24 óránként egyszer) kezd futni, amíg sikeresen nem halad előre. Újra.  Ha úgy gondolja, hogy kijavította azt a problémát, amely miatt az indexelő egy bizonyos ponton elakadt, végrehajthatja az indexelő igény szerinti futtatását, és ha ez sikeresen halad előre, az indexelő ismét visszatér a beállított ütemezési időközéhez.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Ütemezés a portálon

A portál Adatok importálása varázslója lehetővé teszi az indexelő ütemezésének meghatározását a létrehozás időpontjában. Az alapértelmezett ütemezési beállítás **az Óránkénti**, ami azt jelenti, hogy az indexelő a létrehozása után egyszer fut, és utána óránként újra fut.

Módosíthatja az Ütemezés beállítást **Egyszer,** ha nem szeretné, hogy az indexelő automatikusan újra fusson, vagy napi egyszer **fusson.** Állítsa **egyénire,** ha más időközt vagy egy adott jövőbeli kezdési időpontot szeretne megadni.

Ha az ütemezést **Egyéni**értékre állítja, a mezőkben megadhatja az **Intervallumot** és a **Kezdési időt (UTC).** A legrövidebb időintervallum 5 perc, a leghosszabb pedig 1440 perc (24 óra).

   ![Indexelő ütemezése beállítása az Adatok importálása varázslóban](media/search-howto-schedule-indexers/schedule-import-data.png "Indexelő ütemezése beállítása az Adatok importálása varázslóban")

Az indexelő létrehozása után módosíthatja az ütemezési beállításokat az indexelő Szerkesztés paneljén. Az Ütemezés mezők megegyeznek az Adatok importálása varázslóban.

   ![Az ütemezés beállítása az indexelő Szerkesztés panelen](media/search-howto-schedule-indexers/schedule-edit.png "Az ütemezés beállítása az indexelő Szerkesztés panelen")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Ütemezés REST API-k használatával

Az indexelő ütemezését a REST API használatával határozhatja meg. Ehhez az indexelő létrehozásakor vagy frissítésekor adja meg az **ütemezési** tulajdonságot. Az alábbi példa egy meglévő indexelő frissítésére vonatkozó PUT-kérelmet mutat be:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Az **intervallum** paraméter megadása kötelező. Az intervallum két egymást követő indexelő végrehajtás kezdete közötti időre utal. A legkisebb megengedett időköz 5 perc; a leghosszabb egy nap. XSD "dayTimeDuration" értékként kell formázni (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték korlátozott részhalmaza). A minta ez: `P(nD)(T(nH)(nM))`. Példák: `PT15M` 15 percenként, `PT2H` 2 óránként.

A választható **startTime** jelzi, hogy mikor kell elkezdeni az ütemezett végrehajtásokat. Ha nincs megadva, a program az aktuális UTC-időt használja. Ez az idő lehet a múltban, ebben az esetben az első végrehajtás van ütemezve, mintha az indexelő folyamatosan fut, mivel az eredeti **startTime**.

Az indexelő t az Indexelő futtatása hívással igény szerint is futtathat. Az indexelők futtatásáról és az indexelő ütemezések beállításáról az [Indexelő futtatása](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [az Indexelő lehívása](https://docs.microsoft.com/rest/api/searchservice/get-indexer)és az [Indexelő frissítése](https://docs.microsoft.com/rest/api/searchservice/update-indexer) a REST API-referencia című témakörben talál további információt.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Ütemezés a .NET SDK használatával

Az indexelő ütemezését az Azure Cognitive Search .NET SDK használatával határozhatja meg. Ehhez adja meg az **ütemezési** tulajdonságot indexelő létrehozásakor vagy frissítésekor.

A következő C# példa létrehoz egy indexelőt egy előre definiált adatforrás és index használatával, és úgy állítja be az ütemezését, hogy naponta egyszer fusson 30 perc múlva:

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
Ha az **ütemezési** paraméter nincs megadva, az indexelő csak a létrehozása után azonnal egyszer fog futni.

A **startTime** paraméter beállítható egy korábbi időpontra. Ebben az esetben az első végrehajtás úgy van ütemezve, mintha az indexelő folyamatosan futna az adott **startTime**óta.

Az ütemezés az [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) osztály használatával van definiálva. Az **IndexingSchedule** konstruktorhoz **timespan** objektum mal megadott **intervallumparaméter** szükséges. A legkisebb engedélyezett időköz érték 5 perc, a legnagyobb pedig 24 óra. A második **startTime** paraméter, amely **DateTimeOffset** objektumként van megadva, nem kötelező.

A .NET SDK lehetővé teszi az indexelő műveletek vezérlését a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) osztály és [indexelők](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) tulajdonságával, amely az **IIndexersOperations felületmetódusait valósítja** meg. 

Az indexelőt igény szerint bármikor futtathatja a [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)vagy [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) metódusok egyikével.

Az indexelők létrehozásáról, frissítéséről és futtatásáról az [IIindexersOperations című](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)témakörben talál további információt.
