---
title: Tömeges importálási és frissítési műveletek végrehajtása a tömeges végrehajtó .NET-kódtár használatával Azure Cosmos DB
description: Azure Cosmos DB dokumentumok tömeges importálása és frissítése tömeges végrehajtó .NET-kódtár használatával.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 0b74c7b178ee4512067de4b8decba0c3c565ccd4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616966"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Tömeges végrehajtás .NET-függvénytár használata a Azure Cosmos DB

Ez az oktatóanyag útmutatást nyújt a Azure Cosmos DB tömeges végrehajtó .NET-függvénytárának használatáról a dokumentumok Azure Cosmos-tárolóba történő importálásához és frissítéséhez. Ha többet szeretne megtudni a tömeges végrehajtó függvénytárról, valamint arról, hogy miként segíti a nagy átviteli sebesség és tárterület kihasználását, tekintse meg a [tömeges végrehajtó függvénytár áttekintését](bulk-executor-overview.md) ismertető cikket. Ebben az oktatóanyagban egy minta .NET-alkalmazást fog látni, amely a véletlenszerűen generált dokumentumokat egy Azure Cosmos-tárolóba importálja. Az importálást követően bemutatjuk, hogyan frissítheti az importált adatok tömeges frissítését úgy, hogy az adott dokumentum mezőin végrehajtandó műveletekként megadhatja a javításokat. 

Tömeges végrehajtó könyvtár jelenleg az Azure Cosmos DB SQL API-t, és csak a Gremlin API-fiókok által támogatott. Ez a cikk a tömeges végrehajtó .NET-függvénytár SQL API-fiókokkal való használatát ismerteti. Tömeges végrehajtó .NET-kódtár használatával a Gremlin API-val kapcsolatos további információkért lásd: [tömeges műveletek végrehajtása az Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Előfeltételek

* Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja a [Visual studio 2019 Community Editiont](https://www.visualstudio.com/downloads/). Győződjön meg arról, hogy engedélyezi az Azure-fejlesztést a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt. 

* Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül. Vagy használhatja a [Azure Cosmos db emulátort](https://docs.microsoft.com/azure/cosmos-db/local-emulator) a `https://localhost:8081` végponttal. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a .NET gyors üzembe helyezési cikk [adatbázis-fiók létrehozása](create-sql-api-dotnet.md#create-account) című szakaszában ismertetett lépések segítségével. 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most váltson a Code-ra, és töltsön le néhány minta .NET-alkalmazást a GitHubról. Ezek az alkalmazások tömeges műveleteket hajtanak végre Azure Cosmos DB adatokon. Az alkalmazások klónozásához nyisson meg egy parancssort, Navigáljon arra a könyvtárra, ahová másolni szeretné őket, és futtassa a következő parancsot:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

A klónozott tárház két mintát tartalmaz: "BulkImportSample" és "BulkUpdateSample". A minta alkalmazások közül bármelyiket megnyitva frissítheti az app. config fájlban található kapcsolatok karakterláncait a Azure Cosmos DB fiókja kapcsolatainak karakterláncait, felépítheti a megoldást, és futtathatja. 

A "BulkImportSample" alkalmazás véletlenszerű dokumentumokat hoz létre, és tömegesen importálja őket a Azure Cosmos DBba. A "BulkUpdateSample" alkalmazás az importált dokumentumokat úgy frissíti, hogy a javításokat az adott dokumentum mezőin végrehajtandó műveletekként határozza meg. A következő részekben ezeket a példákat fogja áttekinteni a kódban.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>A tömeges adatok importálása az Azure Cosmos DB-hez

1. Navigáljon a "BulkImportSample" mappára, és nyissa meg a "BulkImportSample. SLN" fájlt.  

2. A Azure Cosmos DB a kapcsolatok karakterláncait az app. config fájlból kéri le, ahogy az a következő kódban látható:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   A tömeges programu Pro import hoz létre egy új adatbázist és gyűjteményt az adatbázisnév, a gyűjtemény neve és az átviteli sebesség értékeket az App.config fájlban megadott. 

3. A DocumentClient objektum következő közvetlen TCP-kapcsolati móddal van inicializálva:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. A BulkExecutor objektum a várakozási idő és a szabályozott kérelmek esetében magas újrapróbálkozási értékkel van inicializálva. Ezután a 0 értékre vannak állítva, hogy átadja a zsúfoltság-vezérlést a BulkExecutor élettartama érdekében.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Az alkalmazás meghívja a BulkImportAsync API-t. A .NET-függvénytár két olyan túlterhelést biztosít a tömeges importálási API-nak, amely elfogadja a szerializált JSON-dokumentumok listáját, a másik pedig fogadja a deszerializált POCO-dokumentumok listáját. Ha többet szeretne megtudni ezekről a túlterhelt módszerekről, tekintse meg az [API dokumentációját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **A BulkImportAsync metódus a következő paramétereket fogadja el:**
   
   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |enableUpsert    |   A dokumentumok upsert engedélyezésének jelzője. Ha egy dokumentumot a megadott azonosító már létezik, frissül. Alapértelmezés szerint hamis értékre van állítva.      |
   |disableAutomaticIdGeneration    |    Azt a jelzőt, automatikus generálása azonosítójának letiltása Alapértelmezés szerint az értéke igaz.     |
   |maxConcurrencyPerPartitionKeyRange    | A partíciós kulcs tartományának maximális párhuzamossági foka, a NULL értékre állítás a függvénytár alapértelmezett értékét fogja használni. |
   |maxInMemorySortingBatchSize     |  Az egyes fázisokban az API-hívásnak átadott dokumentum-enumerálásból lekért dokumentumok maximális száma.  A tömeges importálást megelőzően a memóriában lévő előzetes feldolgozási fázis esetében a NULL értékre állítása esetén a függvénytár alapértelmezett értéket fog használni (Documents. Count, 1000000).       |
   |cancellationToken    |    A lemondási token, amely szabályosan kizárja a tömeges importálást.     |

   **Tömeges importálási válasz objektumának definíciója** A tömeges importálási API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |NumberOfDocumentsImported (hosszú)   |  Sikeresen importálva lettek a tömeges megadott dokumentumból dokumentumok száma importálja az API-hívás.       |
   |TotalRequestUnitsConsumed (dupla)   |   A tömeges által felhasznált teljes kérelemegység (RU) importálni az API-hívás.      |
   |TotalTimeTaken (TimeSpan)    |   Teljes idő a tömeges importálás API-hívás végrehajtása befejeződik.      |
   |BadInputDocuments (objektum\<listázása >)   |     A lista rossz formátumú dokumentumok importálása nem sikerült a tömeges importálása API-hívás. Felhasználó kell hárítsa el a dokumentumokat ad vissza, és próbálkozzon újra az importálással. Hibás formátumú dokumentumok tartalmazzák a dokumentumok, amelynek azonosító értéke nem egy karakterláncot (NULL értékű vagy bármely más adattípus érvénytelen akkor tekinthető).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Tömeges frissítés adatainak az Azure Cosmos DB

Meglévő dokumentumok frissítheti a BulkUpdateAsync API-val. Ebben a példában egy új értékre állítva a név mező, és a Leírás mező eltávolítása a meglévő dokumentumok. A támogatott mező-frissítési műveletek teljes készletét az API- [dokumentációban](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)találja. 

1. Navigáljon a "BulkUpdateSample" mappára, és nyissa meg a "BulkUpdateSample. SLN" fájlt.  

2. A frissítési elemek megadása a megfelelő mező-frissítési műveletekkel együtt. Ebben a példában a SetUpdateOperation használandó frissíti a nevét és a Leírás mező eltávolítása az összes dokumentum UnsetUpdateOperation. Végez, és más műveletek növekmény például egy dokumentum mező egy adott érték, adott értékekre leküldése egy tömb mezőt, vagy egy adott érték eltávolítása egy tömb mező. Ha többet szeretne megtudni a tömeges frissítési API által nyújtott különböző módszerekről, tekintse meg az [API dokumentációját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Az alkalmazás meghívja a BulkUpdateAsync API-t. Az BulkUpdateAsync metódus definíciójának megismeréséhez tekintse meg az [API dokumentációját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **A BulkUpdateAsync metódus a következő paramétereket fogadja el:**

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   A partíciós kulcs tartományának maximális párhuzamossági foka, a Null érték megadása esetén a könyvtár az alapértelmezett 20 értéket fogja használni.   |
   |maxInMemorySortingBatchSize    |    A frissítési elemek enumerálása által a tömeges frissítés előtt az API-hívásnak átadott frissítési elemek maximális száma, a NULL értékre állítása esetén a könyvtár a minimális érték (updateItems. Count) alapértelmezett értékét fogja használni. 1000000).     |
   | cancellationToken|A lemondási token, amely szabályosan kizárja a tömeges frissítést. |

   **Tömeges frissítés válasz objektumának definíciója** A tömeges frissítési API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |NumberOfDocumentsUpdated (hosszú)    |   Azon dokumentumok teljes száma, amelyek sikeresen frissítve lettek a tömeges frissítési API-híváshoz megadott értékekkel.      |
   |TotalRequestUnitsConsumed (dupla)   |    A teljes kérelemegység (RU) a tömeges frissítés által használt API-hívás.    |
   |TotalTimeTaken (TimeSpan)   | A tömeges teljes idő frissítés API-hívás végrehajtása befejeződik. |
    
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A jobb teljesítmény érdekében a következő szempontokat vegye figyelembe, tömeges végrehajtó szalagtár használata esetén:

* A legjobb teljesítmény érdekében az alkalmazást egy olyan Azure-beli virtuális gépről futtassa, amely ugyanabban a régióban található, mint a Cosmos DB fiók írási régiója.  

* Ajánlott egyetlen BulkExecutor objektumot létrehozni a teljes alkalmazáshoz egy adott Cosmos-tárolóhoz tartozó egyetlen virtuális gépen belül.  

* Mivel egy egyszeri tömeges API művelet végrehajtása egy nagy szövegrészletet, az ügyfél gépének Processzor- és hálózati i/o használ fel. Több feladat indítja belsőleg szerint ez történik, elkerülheti, hogy az alkalmazás folyamatának minden tömeges műveletet végrehajtó API-hívások belül több egyidejű feladat indítja. Ha egyetlen virtuális gépen futó egyetlen tömeges művelet API-hívása nem tudja felhasználni a teljes tároló átviteli sebességét (ha a tároló átviteli sebessége > 1 000 000 RU/s), érdemes lehet külön virtuális gépeket létrehozni az egyidejű végrehajtáshoz tömeges művelet API-hívások.  

* Győződjön meg arról, hogy a InitializeAsync () hívása egy BulkExecutor objektum példányának létrehozása után történt a cél Cosmos-tároló partíciós leképezésének beolvasásához.  

* Az alkalmazás app. config fájljában ellenőrizze, hogy a **gcServer** engedélyezve van-e a jobb teljesítmény érdekében
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A könyvtár olyan nyomkövetéseket bocsát ki, amelyek egy naplófájlba vagy a konzolon gyűjthetők össze. Mindkét beállítás engedélyezéséhez adja hozzá a következőt az alkalmazás app. config fájlhoz.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>További lépések
* További információ a Nuget csomag részleteiről és a tömeges végrehajtó .NET-függvénytár kibocsátási megjegyzéséről:[tömeges végrehajtó SDK – részletek](sql-api-sdk-bulk-executor-dot-net.md). 
