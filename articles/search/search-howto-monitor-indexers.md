---
title: Indexelő állapotának és eredményeinek figyelése
titleSuffix: Azure Cognitive Search
description: Figyelje az Azure Cognitive Search indexelők állapotát, állapotát és eredményeit az Azure Portalon a REST API vagy a .NET SDK használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112979"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Az Azure Cognitive Search indexelő állapotának és eredményeinek figyelése

Az Azure Cognitive Search állapot- és figyelési információkat biztosít az egyes indexelők aktuális és korábbi futtatásairól.

Az indexelő figyelése akkor hasznos, ha a következőket szeretné:

* Egy indexelő folyamatának nyomon követése egy folyamatban lévő futtatás során.
* Tekintse át a folyamatban lévő vagy az előző indexelő futtatás eredményeit.
* Azonosítsa a legfelső szintű indexelő hibákat, valamint az indexelt dokumentumokra vonatkozó hibákat vagy figyelmeztetéseket.

## <a name="get-status-and-history"></a>Állapot és előzmények beszerezni

Az indexelő figyelési információihoz különböző módokon férhet hozzá, többek között:

* Az [Azure portalon](#portal)
* A [REST API](#restapi) használata
* A [.NET SDK](#dotnetsdk) használata

A rendelkezésre álló indexelő figyelési információk tartalmazzák az alábbiakat (bár az adatformátumok az alkalmazott hozzáférési módszertől függően különböznek):

* Állapotinformáció magáról az indexelőről
* Az indexelő legutóbbi futtatásával kapcsolatos információk, beleértve az állapotát, a kezdési és befejezési időpontokat, valamint a részletes hibákat és figyelmeztetéseket.
* Az előzményindexelő kitolásának listája, valamint állapotuk, eredményeik, hibáik és figyelmeztetéseik.

A nagy mennyiségű adatot feldolgozó indexelők futtatása hosszú időt vehet igénybe. Például a több millió forrásdokumentumot kezelő indexelők 24 órán keresztül futtathatók, majd szinte azonnal újraindulhatnak. A nagy mennyiségű indexelők állapota mindig azt **mondhatja, hogy folyamatban van** a portálon. Még akkor is, ha egy indexelő fut, részletek érhetők el a folyamatban lévő folyamatban lévő és a korábbi futtatások.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitor a portál használatával

Az összes indexelő aktuális állapotát a keresési szolgáltatás áttekintése lapon lévő **Indexelők** listában tekintheti meg.

   ![Indexelők listája](media/search-monitor-indexers/indexers-list.png "Indexelők listája")

Az indexelő végrehajtásakor a listában látható a **Folyamatban**állapot, a **Sikeres dokumentumok** érték pedig az eddig feldolgozott dokumentumok száma látható. Eltarthat néhány percig, amíg a portál frissíti az indexelő állapotértékeit és a dokumentumok számát.

Az indexelő, amelynek legutóbbi futtatása sikeres volt, a **Success -t**mutatja. Az indexelő futtatása akkor is sikeres lehet, ha az egyes dokumentumok hibásak, ha a hibák száma kisebb, mint az indexelő **Sikertelen elemek** beállítása.

Ha a legutóbbi futtatás hibával ért véget, az állapot a **Sikertelen**állapot látható. **Az Alaphelyzetbe állítás** állapota azt jelenti, hogy az indexelő változáskövetési állapota alaphelyzetbe lett állítva.

Kattintson egy indexelő a listában, hogy további részleteket az indexelő aktuális és legutóbbi fut.

   ![Indexelő összefoglaló és végrehajtási előzményei](media/search-monitor-indexers/indexer-summary.png "Indexelő összefoglaló és végrehajtási előzményei")

Az **Indexelő összesítő** diagramja grafikont jelenít meg a legutóbbi futtatások során feldolgozott dokumentumok számáról.

A **Végrehajtás részletei** lista a legutóbbi végrehajtási eredmények közül legfeljebb 50-et jelenít meg.

Kattintson egy végrehajtási eredményre a listában a futtatással kapcsolatos részletek megtekintéséhez. Ez magában foglalja a kezdési és befejezési időpontokat, valamint a bekövetkezett hibákat és figyelmeztetéseket.

   ![Indexelő végrehajtási részletei](media/search-monitor-indexers/indexer-execution.png "Indexelő végrehajtási részletei")

Ha a futtatás során dokumentumspecifikus problémák merültek fel, azok a Hibák és figyelmeztetések mezőkben jelennek meg.

   ![Indexelő részletek hibákkal](media/search-monitor-indexers/indexer-execution-error.png "Indexelő részletek hibákkal")

A figyelmeztetések gyakoriak bizonyos típusú indexelőknél, és nem mindig jeleznek problémát. A kognitív szolgáltatásokat használó indexelők például figyelmeztetést jelenthetnek, ha a kép- vagy PDF-fájlok nem tartalmaznak feldolgozandó szöveget.

Az indexelőhibák és -figyelmeztetések vizsgálatáról az [Azure Cognitive Search gyakori indexelő-problémáinak elhárítása című témakörben](search-indexer-troubleshooting.md)olvashat bővebben.

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Figyelő REST API-k használatával

Az indexelő állapotának [beolvasása paranccsal](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)lekérheti az indexelő állapotát és végrehajtási előzményeit:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A válasz tartalmazza az indexelő általános állapotát, az utolsó (vagy folyamatban lévő) indexelő meghívást és a legutóbbi indexelő-meghívások előzményeit.

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

A végrehajtási előzmények legfeljebb 50 legutóbbi futtatást tartalmaznak, amelyek fordított időrendi sorrendben vannak rendezve (a legutóbbi először).

Ne feledje, hogy két különböző állapotérték létezik. A legfelső szintű állapot magához az indexelőhez van. A **futás** indexelő állapota azt jelenti, hogy az indexelő megfelelően van beállítva, és futtatható, de nem, hogy jelenleg fut.

Az indexelő minden egyes futtatása saját állapotú, amely azt jelzi, hogy az adott végrehajtás folyamatban van **(fut),** vagy már befejeződött **a sikeres**, **transientFailure**, vagy **persistentFailure** állapottal. 

Amikor egy indexelő alaphelyzetbe áll a változáskövetési állapot ának frissítéséhez, egy külön végrehajtási előzménybejegyzés kerül hozzáadásra **egy Visszaállítás** állapotú.

Az állapotkódokról és az indexelő figyelési adatairól a [GetIndexerStatus (GetIndexerStatus)](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)témakörben talál további információt.

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitor a .NET SDK használatával

Az indexelő ütemezését az Azure Cognitive Search .NET SDK használatával határozhatja meg. Ehhez adja meg az **ütemezési** tulajdonságot indexelő létrehozásakor vagy frissítésekor.

A következő C# példa az indexelő állapotára és a konzolra való legutóbbi (vagy folyamatban lévő) futtatásának eredményeire vonatkozó információkat írja.

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

A kimenet a konzol fog kinézni valahogy így:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Ne feledje, hogy két különböző állapotérték létezik. A legfelső szintű állapot magának az indexelőnek az állapota. **A Futás** indexelő állapota azt jelenti, hogy az indexelő megfelelően van beállítva, és elérhető a végrehajtáshoz, de nem azt, hogy jelenleg fut.

Az indexelő minden egyes futtatása saját állapotú, függetlenül attól, hogy az adott végrehajtás folyamatban van-e (**Futtatás**), vagy már sikeres **vagy** **tranzienhiba** állapottal fejeződött be. 

Amikor egy indexelő alaphelyzetbe áll a változáskövetési állapot ának frissítéséhez, egy külön előzménybejegyzés kerül hozzáadásra **egy Visszaállítás** állapottal.

Az állapotkódokról és az indexelő figyelési információiról a [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) a REST API-ban című témakörben talál további információt.

A dokumentumspecifikus hibák vagy figyelmeztetések részletei a listák `IndexerExecutionResult.Errors` és `IndexerExecutionResult.Warnings`a számbavételével olvashatók be.

Az indexelők figyelésére használt .NET SDK-osztályokról az [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) és az [IndexerExecutionResult című témakörben talál](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)további információt.
