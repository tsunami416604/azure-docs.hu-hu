---
title: Az indexelő állapotának és eredményeinek – Azure Search figyelése
description: Állapota, folyamatban van, és az Azure Portalon, a REST API vagy a .NET SDK használatával az Azure Search indexelők eredményeinek figyelése.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486284"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Azure Search-indexelő állapotának és eredményeinek figyelése

Az Azure Search állapota és összes indexelő aktuális és előzménynézeteket futtatások monitoringadatokat biztosít.

Az indexelő figyelés akkor hasznos, ha szeretné:

* Futtatás során egy folyamatban lévő az indexelő állapotának nyomon követése.
* Tekintse át az eredményeket a folyamatban lévő vagy az előző indexelő futtatása.
* Azonosítsa a legfelső szintű indexelő hibákat, és a hibák vagy figyelmeztetések, egyes dokumentumok indexelése.

## <a name="find-indexer-status-and-history-details"></a>Az indexelő állapota és előzményei talál

Az indexelő monitorozási adatait a különféle módokon, például érheti el:

* Az [Azure Portalon](#portal)
* Használatával a [REST API-val](#restapi)
* Használatával a [.NET SDK-val](#dotnetsdk)

Figyelési információkat elérhető indexelő minden az alábbiak (bár az adatok formátumok eltérőek a használt hozzáférési módszer alapján):

* Az indexelő maga vonatkozó állapotinformáció
* Az indexelő, beleértve az állapot, kezdő és záró időponttal, és a részletes hibaüzenetek és figyelmeztetések futtatása a legfrissebb információkat.
* Korábbi indexelő fut le, és azok állapotának, eredményeket, hibák és figyelmeztetések listáját.

Nagy mennyiségű adatot feldolgozó indexelők futtatásához hosszú időt vehet igénybe. Például, hogy a forrás dokumentumok millió indexelők is 24 órán keresztül futtat, és indítsa szinte azonnal. Előfordulhat, hogy mindig azt a nagy mennyiségű indexelők állapota **folyamatban lévő** a portálon. Akkor is, ha az indexelő futása folyamatban lévő folyamat és a korábbi közvetítésekből részletei érhetők el.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>A figyelő az indexelők a portálon

Az indexelők az összes aktuális állapotát megtekintheti a **indexelők** a keresési szolgáltatás áttekintés oldalán a listában.

   ![Indexelők listájának](media/search-monitor-indexers/indexers-list.png "indexelők listájának")

Amikor egy indexelő végrehajtása, a lista megjeleníti a állapotát **folyamatban lévő**, és a **Docs sikeres** értéket, amennyiben feldolgozott dokumentumok számát jeleníti meg. A portálon az indexelő állapotának értékek frissítése néhány percet vesz igénybe, és a dokumentumok számát.

Az indexelő, amelynek legutóbbi futtatás sikeres látható volt **sikeres**. Az indexelők futtatása lehet sikeres akkor is, ha az egyes dokumentumok hibásak, ha a hibák száma nem éri el az indexelő **sikertelen elemek max.** beállítás.

A legutóbbi futtatás befejeződött a hibát, ha az állapot mutatja **sikertelen**. Az állapot **alaphelyzetbe** azt jelenti, hogy az indexelő a change tracking állapot alaphelyzetbe állt.

Kattintson a listában bővebb információt az indexelő aktuális és közelmúltbeli az indexelő a futtatja.

   ![Indexelő összegzése és végrehajtási előzmények](media/search-monitor-indexers/indexer-summary.png "indexelő összegzése és végrehajtási előzmények")

A **indexelő összegzése** diagram a legutóbbi futtatás a feldolgozott dokumentumok száma grafikonon jeleníti meg.

A **végrehajtásának részletei** lista mutatja azokat az utolsó végrehajtási eredmények legfeljebb 50.

Kattintson a listában megtekintheti a konkrét részleteket, amely egy végrehajtás eredménye. Ez magában foglalja a kezdő és záró időponttal, és el a hibákat és figyelmeztetést, történt.

   ![Az indexelő végrehajtásának részletei](media/search-monitor-indexers/indexer-execution.png "indexelő végrehajtásának részletei")

Ha a futtatás során dokumentum kapcsolatos problémák, azok a hibák és figyelmeztetések mezőket megjelenik.

   ![Az indexelő részletek hibákkal](media/search-monitor-indexers/indexer-execution-error.png "hibákkal indexelő részletei")

Figyelmeztetések közös, az indexelők bizonyos típusú, és nem mindig jelez hibát. Például a cognitive services használó indexelőktől jelenthetik figyelmeztetések, kép- vagy PDF-fájlok nem tartalmaznak a szöveg feldolgozása során.

Az indexelő hibák és figyelmeztetések vizsgálata kapcsolatos további információkért lásd: [kapcsolatos hibák elhárítása közös indexelő az Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>A figyelő az indexelők REST API használatával

Az állapot és végrehajtási előzményei egy indexelő segítségével lekérheti a [indexelő állapotának beolvasása parancs](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A válasz teljes indexelő állapot, az utolsó (vagy a folyamatban lévő) az indexelő meghívása és az indexelő legutóbbi indítások előzményeit tartalmazza.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Végrehajtási előzmények tartalmazza, akár az 50 legutóbbi fut, amely fordított időrendben (legutóbbi van legelöl) vannak rendezve.

Vegye figyelembe, hogy két különböző állapot-értékek. A legfelső szintű állapota az indexelő magát. Az indexelő állapotának **futó** azt jelenti, hogy az indexelő helyesen van beállítva, és futtatható, de ez nem a jelenleg futó.

Minden egyes futtatásához az indexelő is a saját állapotban van, amely jelzi, hogy az adott végrehajtás folyamatban lévő (**futó**), vagy már befejeződött, egy **sikeres**, **transientFailure**, vagy **persistentFailure** állapotát. 

Az indexelő alaphelyzetbe áll, a változáskövetési állapot frissítéséhez, ha egy külön végrehajtási Feladatelőzmény-bejegyzést a bekerül egy **alaphelyzetbe** állapotát.

Állapot- és monitorozási adatok indexelő kapcsolatos további információkért lásd: [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>A figyelő az indexelők a .NET SDK használatával

Megadhatja az ütemezés egy indexelő az Azure Search .NET SDK használatával. Ehhez közé tartozik a **ütemezés** létrehozásakor vagy frissítésekor az indexelő tulajdonság.

A következő C# példa az indexelő állapotával kapcsolatos adatokat ír az adatbázis és az eredményeket a legújabb (vagy folyamatban lévő), a konzol futtatásához.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

A kimenetet a konzolon a következőhöz hasonlóan fog kinézni:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Vegye figyelembe, hogy két különböző állapot-értékek. A legfelső szintű állapota az indexelő magát az állapotát. Az indexelő állapotának **futó** azt jelenti, hogy az indexelő helyesen van beállítva, és előfordulhat, hogy végrehajtási, de azok nem végrehajtása folyamatban van.

Minden egyes futtatásához az indexelő is rendelkezik e adott végrehajtás folyamatban a saját állapota (**futó**), vagy már befejeződött az egy **sikeres** vagy **TransientError** állapot. 

Az indexelő alaphelyzetbe áll, a változáskövetési állapot frissítéséhez, ha egy külön Feladatelőzmény-bejegyzést a bekerül egy **alaphelyzetbe** állapotát.

Állapot- és figyelési információkat indexelő kapcsolatos további információkért lásd: [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) a REST API-ban.

Dokumentum-specifikus hibákat vagy figyelmeztetéseket adatait lekérheti a listák enumerálása `IndexerExecutionResult.Errors` és `IndexerExecutionResult.Warnings`.

A .NET SDK-osztályok indexelők figyelésére használható kapcsolatos további információkért lásd: [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) és [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
