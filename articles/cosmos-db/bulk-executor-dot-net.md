---
title: Tömeges végrehajtó .NET-kódtár segítségével hajtsa végre a tömeges importálási és frissítési műveleteket az Azure Cosmos DB-ben
description: Tömeges importálás, és frissítse az Azure Cosmos DB-dokumentumot tömeges végrehajtó .NET-kódtár használatával.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d6fed65dad0e8f12495177b274cef181e9e91879
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044314"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Tömeges végrehajtó .NET-kódtár használatával tömeges műveletek végrehajtása az Azure Cosmos DB-ben

Ebben az oktatóanyagban útmutatás segítségével az Azure Cosmos DB tömeges végrehajtó .NET-kódtár importálása és az Azure Cosmos DB-tárolók dokumentumok frissítéséhez. Tömeges végrehajtó kódtár, és segítséget nyújt a nagy átviteli sebesség és a storage kapcsolatos további információkért lásd: [tömeges végrehajtó ügyfélkódtár áttekintése](bulk-executor-overview.md) cikk. Ebben az oktatóanyagban látni fogja, véletlenszerűen tömeges importálás .NET mintaalkalmazás dokumentumok létrehozott egy Azure Cosmos DB-tárolóba. Az importálás után azt mutatja, hogy kötegelve tölthesse be az importált adatok frissítése javítások végrehajtásához az adott dokumentum mezők műveletként megadásával. 

Tömeges végrehajtó könyvtár jelenleg az Azure Cosmos DB SQL API-t, és csak a Gremlin API-fiókok által támogatott. Ez a cikk ismerteti a tömeges végrehajtó .NET-kódtár használata SQL API-fiókok. Tömeges végrehajtó .NET-kódtár használatával a Gremlin API-val kapcsolatos további információkért lásd: [tömeges műveletek végrehajtása az Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Előfeltételek

* Ha már nincs telepítve a Visual Studio 2017, letöltheti és használhatja a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Győződjön meg arról, hogy engedélyezze az Azure fejlesztői, a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt. 

* Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül. Vagy használhatja a [Azure Cosmos DB Emulatort](https://docs.microsoft.com/azure/cosmos-db/local-emulator) együtt a `https://localhost:8081` URI-t. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot az ismertetett lépéseket követve [adatbázisfiók létrehozása](create-sql-api-dotnet.md#create-a-database-account) .NET rövid cikkének. 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most már váltsunk át kódok használatára néhány mintául szolgáló .NET-alkalmazások letöltése a Githubról. Ezek az alkalmazások tömeges műveletek végrehajtása a Azure Cosmos DB-adatai. Klónozza az alkalmazások, nyisson meg egy parancssort, lépjen abba a könyvtárba, ahová másolni a őket, és futtassa a következő parancsot:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

A klónozott adattár tartalmaz két minta "BulkImportSample" és "BulkUpdateSample." Nyissa meg a mintaalkalmazások egyikét, a kapcsolati karakterláncokkal az App.config fájlban frissítse az Azure Cosmos DB-fiók kapcsolati karakterláncok, a megoldás felépítéséhez és futtathatja. 

A "BulkImportSample" alkalmazás véletlenszerű dokumentumokat hoz létre, és tömeges importálja őket az Azure Cosmos DB-hez. A "BulkUpdateSample" alkalmazás tömeges frissíti az importált dokumentumok javítások végrehajtásához az adott dokumentum mezők műveletként megadásával. A következő szakaszokban a kódot a mintaalkalmazások mindegyike fogja ellenőrizni.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>A tömeges adatok importálása az Azure Cosmos DB-hez

1. Keresse meg a "BulkImportSample" mappát, és nyissa meg a "BulkImportSample.sln" fájlt.  

2. Az Azure Cosmos DB kapcsolati karakterláncok kéri le a az App.config fájlban a következő kódban látható módon:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   A tömeges programu Pro import hoz létre egy új adatbázist és gyűjteményt az adatbázisnév, a gyűjtemény neve és az átviteli sebesség értékeket az App.config fájlban megadott. 

3. Ezután a DocumentClient objektum inicializálása során közvetlen TCP-kapcsolat módja:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. A BulkExecutor objektum inicializálása egy nagy újrapróbálkozási értéket a várakozási idő, és a kérelmek szabályozva. És ezután azok értéke 0 BulkExecutor élettartamuk a torlódásszabályozás átadása.  

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

5. Az alkalmazás a BulkImportAsync API-t hív meg. A .NET-kódtár biztosít két túlterhelésekkel tömeges importálása API –, amely egy szerializált JSON-dokumentumok listáját fogadja el a másik pedig akkor deszerializált POCO dokumentumok listáját. E túlterhelt módszerek meghatározásai kapcsolatos további információkért tekintse meg [API-dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync módszer a következő paramétereket fogadja:**
   
   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |enableUpsert    |   Ahhoz, hogy a dokumentumok upsert jelző. Ha egy dokumentumot a megadott azonosító már létezik, frissül. Alapértelmezés szerint azt értéke hamis.      |
   |disableAutomaticIdGeneration    |    Azt a jelzőt, automatikus generálása azonosítójának letiltása Alapértelmezés szerint az értéke igaz.     |
   |maxConcurrencyPerPartitionKeyRange    | A partíciókulcs-tartományonként párhuzamosság maximális párhuzamossági, NULL beállítás alapértelmezett értéke 20 használandó szalagtárat okozhat. |
   |maxInMemorySortingBatchSize     |  A dokumentum enumeráló modulja, amely az API-nak átadott lekért dokumentumok maximális száma az egyes fázisokban hívja.  A memórián belüli előfeldolgozási rendezési fázisban tömeges importálás előtt beállítás NULL okozhat könyvtár perc (documents.count 1000000) alapértelmezett értéket használja.       |
   |cancellationToken    |    A megszakítás token tömeges importálás szabályosan kilép.     |

   **Tömeges importálás válasz Objektumdefiníció** a tömeges importálás API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |NumberOfDocumentsImported (hosszú)   |  Sikeresen importálva lettek a tömeges megadott dokumentumból dokumentumok száma importálja az API-hívás.       |
   |TotalRequestUnitsConsumed (kétirányú)   |   A tömeges által felhasznált teljes kérelemegység (RU) importálni az API-hívás.      |
   |TotalTimeTaken (időtartam)    |   Teljes idő a tömeges importálás API-hívás végrehajtása befejeződik.      |
   |BadInputDocuments (lista<object>)   |     A lista rossz formátumú dokumentumok importálása nem sikerült a tömeges importálása API-hívás. Felhasználó kell hárítsa el a dokumentumokat ad vissza, és próbálkozzon újra az importálással. Hibás formátumú dokumentumok tartalmazzák a dokumentumok, amelynek azonosító értéke nem egy karakterláncot (NULL értékű vagy bármely más adattípus érvénytelen akkor tekinthető).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Tömeges frissítés adatainak az Azure Cosmos DB

Meglévő dokumentumok frissítheti a BulkUpdateAsync API-val. Ebben a példában egy új értékre állítva a név mező, és a Leírás mező eltávolítása a meglévő dokumentumok. A támogatott mező teljes körű frissítési műveleteket, tekintse meg [API-dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Keresse meg a "BulkUpdateSample" mappát, és nyissa meg a "BulkUpdateSample.sln" fájlt.  

2. Adja meg a frissítés elemeket, és megfelelő mező frissítési műveleteket. Ebben a példában a SetUpdateOperation használandó frissíti a nevét és a Leírás mező eltávolítása az összes dokumentum UnsetUpdateOperation. Végez, és más műveletek növekmény például egy dokumentum mező egy adott érték, adott értékekre leküldése egy tömb mezőt, vagy egy adott érték eltávolítása egy tömb mező. A tömeges frissítés API által biztosított különböző módszerekkel kapcsolatos további információkért tekintse meg a [API-dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Az alkalmazás a BulkUpdateAsync API-t hív meg. A definíció BulkUpdateAsync metódus kapcsolatos további információkért tekintse meg [API-dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync módszer a következő paramétereket fogadja:**

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   A partíciókulcs-tartományonként párhuzamosság maximális párhuzamossági, NULL beállítás alapértelmezett értéke 20 használandó szalagtárat okozhat.   |
   |maxInMemorySortingBatchSize    |    Elemek maximális számát az update-elemek enumerátor lekért átadott memórián belüli előfeldolgozási rendezési fázishoz tömeges frissítése előtt az API-hívás az egyes fázisokban, beállítás NULL okozhat könyvtárat használja alapértelmezett értéket (updateItems.count, Min 1000000).     |
   | cancellationToken|A megszakítás token tömeges frissítés szabályosan kilép. |

   **Tömeges frissítés válasz Objektumdefiníció** a tömeges frissítés API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |NumberOfDocumentsUpdated (hosszú)    |   A sikeresen frissített helyreállításra dokumentumok teljes számát a tömeges frissítés megadott API-hívás.      |
   |TotalRequestUnitsConsumed (kétirányú)   |    A teljes kérelemegység (RU) a tömeges frissítés által használt API-hívás.    |
   |TotalTimeTaken (időtartam)   | A tömeges teljes idő frissítés API-hívás végrehajtása befejeződik. |
    
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A jobb teljesítmény érdekében a következő szempontokat vegye figyelembe, tömeges végrehajtó szalagtár használata esetén:

* A legjobb teljesítmény érdekében futtassa az alkalmazást az Azure virtuális gépből, amely a Cosmos DB-fiók írási régió ugyanabban a régióban.  

* Javasoljuk, hogy a teljes alkalmazáshoz egyetlen virtuális gép megfelelő egy adott Cosmos DB-tárolón belül egyetlen BulkExecutor objektumot hozza létre.  

* Mivel egy egyszeri tömeges API művelet végrehajtása egy nagy szövegrészletet, az ügyfél gépének Processzor- és hálózati i/o használ fel. Több feladat indítja belsőleg szerint ez történik, elkerülheti, hogy az alkalmazás folyamatának minden tömeges műveletet végrehajtó API-hívások belül több egyidejű feladat indítja. Nem lehet felhasználni a teljes tárolót átviteli egyetlen virtuális gépen futó egyetlen tömeges művelet API hívás esetén (Ha a tároló átviteli > 1 millió RU/s), ezért célszerű egyidejűleg hajtsa végre a tömeges különálló virtuális gépet hoz létre a művelet API-hívások száma.  

* Győződjön meg arról, InitializeAsync() meghívása után a BulkExecutor objektum beolvasni a cél Cosmos DB-tároló partíciótérképen hárítható el.  

* Győződjön meg, hogy az alkalmazás App.Config, **gcServer** a jobb teljesítmény érdekében engedélyezve van
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A könyvtár bocsát ki a nyomkövetési adatok gyűjthetők össze egy naplófájlba, vagy a konzolon. Mindkettő engedélyezéséhez adja hozzá a következő, az alkalmazás App.Config.

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
* Kibocsátási megjegyzések tömeges végrehajtó .net könyvtár és Nuget csomag részletes ismertetése:[végrehajtó SDK részletek tömeges](sql-api-sdk-bulk-executor-dot-net.md). 
