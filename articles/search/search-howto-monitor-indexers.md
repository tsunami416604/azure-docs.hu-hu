---
title: Az indexelő állapotának és eredményeinek figyelése
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexek állapotának, előrehaladásának és eredményének figyelése a Azure Portal, a REST API vagy a .NET SDK használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: cf7ae504e57d0b7947f4ff7dc48d50b2e0141eea
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936639"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Az Azure Cognitive Search indexelő állapotának és eredményeinek figyelése

Az Azure Cognitive Search minden indexelő aktuális és korábbi futtatásával kapcsolatos állapot-és figyelési információkat biztosít.

Az indexelő figyelése a következő esetekben hasznos:

* Egy indexelő előrehaladásának nyomon követése egy folyamatban lévő futtatás során.
* Tekintse át a folyamatban vagy az előző indexelő futtatásának eredményét.
* Azonosíthatja a legfelső szintű indexelő hibákat, valamint az indexelt dokumentumok egyes dokumentumaival kapcsolatos hibákat és figyelmeztetéseket.

## <a name="get-status-and-history"></a>Állapot és előzmények beolvasása

Az indexelő figyelési információit többféleképpen is elérheti, többek között:

* A [Azure Portal](#portal)
* A [REST API](#restapi) használata
* A [.net SDK](#dotnetsdk) használata

A rendelkezésre álló indexelő-figyelési információk tartalmazzák a következőket (bár az adatformátumok eltérőek a használt hozzáférési módszer alapján):

* Az indexelő állapotával kapcsolatos információk
* Az indexelő legutóbbi futtatásával kapcsolatos információk, beleértve az állapotát, a kezdési és befejezési időpontokat, valamint a részletes hibákat és figyelmeztetéseket.
* A korábbi indexelő futtatások listája, valamint az állapotuk, az eredmények, a hibák és a figyelmeztetések.

A nagyméretű adatmennyiséget feldolgozó indexelő hosszú időt vehetnek igénybe. A több millió forrásoldali dokumentumot kezelő indexelő például 24 óráig futhatnak, majd a szinte azonnal újraindulnak. Előfordulhat, hogy a nagy mennyiségű indexelő állapota mindig a portálon **van folyamatban** . Még ha egy indexelő is fut, a részletek a folyamatban lévő és a korábbi futtatások esetében is elérhetők.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Figyelés a portál használatával

Az összes indexelő állapotát megtekintheti a keresési szolgáltatás áttekintés lapján lévő **Indexelő** listában.

   ![Indexelő lista](media/search-monitor-indexers/indexers-list.png "Indexelő lista")

Az indexelő végrehajtásakor a listában szereplő állapot megjelenik a **folyamatban**, és a **docs sikeres** értéke az eddig feldolgozott dokumentumok számát mutatja. Eltarthat néhány percig, amíg a portál az indexelő állapotának és a dokumentumok számának frissítésére is képes.

Sikeres **sikert**mutat egy indexelő, amelynek a legutóbbi futtatása sikeres volt. Az indexelő futása akkor is sikeres lehet, ha az egyes dokumentumok hibákkal rendelkeznek, ha a hibák száma kisebb, mint az indexelő **nem megfelelő elemek maximális** beállítása.

Ha a legutóbbi Futtatás hibával fejeződött be, az állapot **nem sikerült**. Az alaphelyzetbe **állítás** állapot azt jelenti, hogy az indexelő változás-követési állapota alaphelyzetbe áll.

Kattintson a listában szereplő indexelő elemre az indexelő jelenlegi és legutóbbi futtatásával kapcsolatos további részletek megtekintéséhez.

   ![Indexelő Összegzés és végrehajtási előzmények](media/search-monitor-indexers/indexer-summary.png "Indexelő Összegzés és végrehajtási előzmények")

Az **Indexelő összegző** diagram a legutóbbi futtatásokban feldolgozott dokumentumok számának gráfját jeleníti meg.

A **végrehajtás részletei** listán legfeljebb 50 a legutóbbi végrehajtás eredményei.

Kattintson egy végrehajtási eredményre a listában, hogy megtekintse a futtatási jellemzőket. Ez magában foglalja az indítási és befejezési időpontokat, valamint az esetleges hibákat és figyelmeztetéseket.

   ![Indexelő végrehajtásának részletei](media/search-monitor-indexers/indexer-execution.png "Indexelő végrehajtásának részletei")

Ha a Futtatás során dokumentált problémák léptek fel, azok a hibák és figyelmeztetések mezőkben jelennek meg.

   ![Indexelő adatai hibákkal](media/search-monitor-indexers/indexer-execution-error.png "Indexelő adatai hibákkal")

A figyelmeztetések gyakran előfordulnak bizonyos típusú indexelő típusokban, és nem mindig jeleznek problémát. A kognitív szolgáltatásokat használó indexelő például figyelmeztetést jelenthetnek, ha a kép-vagy PDF-fájlok nem tartalmaznak feldolgozni kívánt szöveget.

További információ az indexelő hibáiról és a figyelmeztetésekről: [Az Azure Cognitive Search gyakori indexelő problémáinak elhárítása](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Figyelés REST API-k használatával

Az indexelő állapotának és végrehajtásának előzményeit az [Indexelő állapotának lekérése paranccsal](/rest/api/searchservice/get-indexer-status)kérheti le:

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

A válasz általános indexelő állapotot, az utolsó (vagy folyamatban lévő) indexelő meghívást, valamint a legutóbbi indexelő hívás előzményeit tartalmazza.

```output
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
```

A végrehajtási előzmények akár a 50 legutóbbi futtatását is tartalmazzák, amelyek fordított időrendi sorrendben vannak rendezve (a legutóbbi első).

Figyelje meg, hogy két különböző állapotüzenetek vannak. A legfelső szintű állapot az indexelő esetében van. A **futó** indexelő állapota azt jelenti, hogy az indexelő helyesen van beállítva, és elérhető a futtatásához, de nem, hogy éppen fut.

Az indexelő minden futtatása saját állapottal rendelkezik, amely azt jelzi, hogy az adott végrehajtás folyamatban van-e (**fut**), vagy már befejeződött **sikeres**, **transientFailure**vagy **persistentFailure** állapottal. 

Ha egy indexelő visszaáll a Change Tracking állapotának frissítésére, egy külön végrehajtási előzmény kerül a **visszaállítási** állapotba.

Az állapotkódok és az indexelő adatainak figyelésével kapcsolatos további információkért lásd: [GetIndexerStatus](/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Figyelés a .NET SDK használatával

Az indexelő ütemtervét az Azure Cognitive Search .NET SDK használatával határozhatja meg. Ehhez az indexelő létrehozásakor vagy frissítésekor adja meg az **Schedule** tulajdonságot.

A következő C#-példa adatokat ír az indexelő állapotáról, valamint a legutóbbi (vagy folyamatban lévő) eszköznek a konzolra való futtatásának eredményéről.

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

A konzol kimenete a következőképpen fog kinézni:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Figyelje meg, hogy két különböző állapotüzenetek vannak. A legfelső szintű állapot az indexelő állapota. A **futó** indexelő állapota azt jelenti, hogy az indexelő helyesen van beállítva, és végrehajtásra elérhető, de nem az éppen végrehajtás alatt áll.

Az indexelő minden futtatása saját állapottal rendelkezik, hogy az adott végrehajtás folyamatban van-e (**fut**), vagy már befejeződött **sikeres** vagy **TransientError** állapottal. 

Ha egy indexelő visszaáll a változás követési állapotának frissítésére, egy külön előzmény kerül a **visszaállítási** állapotba.

Az állapotkódok és az indexelő figyelési információkkal kapcsolatos további információkért lásd: [GetIndexerStatus](/rest/api/searchservice/get-indexer-status) a REST API.

A dokumentum-specifikus hibákra vagy figyelmeztetésekre vonatkozó részletek a lista és a felsorolás enumerálásával olvashatók be `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings` .

Az indexelő figyeléséhez használt .net SDK-osztályokkal kapcsolatos további információkért lásd: [IndexerExecutionInfo](/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) és [IndexerExecutionResult](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).