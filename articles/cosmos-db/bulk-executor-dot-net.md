---
title: Tömeges importálási és frissítési műveletek hez használja a tömeges végrehajtási .NET-kódtárat az Azure Cosmos DB-ben
description: Tömeges importálása és frissítése az Azure Cosmos DB-dokumentumok a tömeges végrehajtó .NET könyvtár használatával.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 40ef05107f20a3396f6710f894a2dbad2d7fa6c9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478849"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>A tömeges végrehajtó .NET-kódtár használatával tömeges műveleteket hajthat végre az Azure Cosmos DB-ben

> [!NOTE]
> Ez a cikkben ismertetett tömeges végrehajtókönyvtár a .NET SDK 2.x verziót használó alkalmazások számára marad fenn. Új alkalmazások esetén használhatja a [.NET SDK 3.x-es verziójával](tutorial-sql-api-dotnet-bulk-import.md) közvetlenül elérhető **tömeges támogatást,** és nem igényel külső könyvtárat. 

> Ha jelenleg használja a tömeges végrehajtó könyvtár, és tervezi, hogy áttelepíti a tömeges támogatást az újabb SDK, használja az [áttelepítési útmutató](how-to-migrate-from-bulk-executor-library.md) az alkalmazás áttelepítése lépéseket.

Ez az oktatóanyag utasításokat tartalmaz a tömeges végrehajtó .NET könyvtár használatával dokumentumok importálása és frissítése egy Azure Cosmos-tárolóba. A tömeges végrehajtó könyvtár, és hogyan segít a tömeges átviteli és tárolási tőkeáttétel, tekintse meg a [tömeges végrehajtó könyvtár áttekintése](bulk-executor-overview.md) cikket. Ebben az oktatóanyagban egy minta .NET alkalmazás jelenik meg, amely tömegesen importálja a véletlenszerűen generált dokumentumokat egy Azure Cosmos-tárolóba. Importálás után bemutatja, hogyan frissítheti tömegesen az importált adatokat, ha javításokat ad meg műveletekként, amelyeket bizonyos dokumentummezőkön hajt végre.

Jelenleg a tömeges végrehajtó könyvtár at csak az Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatják. Ez a cikk a .NET tömeges végrehajtó könyvtár SQL API-fiókokkal való használatát ismerteti. A tömeges végrehajtó .NET-kódtár Gremlin API-fiókokkal való használatáról az [Azure Cosmos DB Gremlin API tömeges műveleteinek végrehajtásáról](bulk-executor-graph-dotnet.md)olvashat.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja a [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Győződjön meg arról, hogy engedélyezi az "Azure-fejlesztést" a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.

* Az [Azure Cosmos DB-t ingyenesen,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen és kötelezettségvállalások nélkül is kipróbálhatja. Vagy használhatja az [Azure Cosmos DB-emulátort](https://docs.microsoft.com/azure/cosmos-db/local-emulator) a `https://localhost:8081` végponttal. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a .NET gyorsútmutató cikk [adatbázis-fiók létrehozása](create-sql-api-dotnet.md#create-account) című szakaszában ismertetett lépésekkel.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most váltsunk át a kódokkal való munkára egy minta .NET alkalmazás letöltésével a GitHubról. Ez az alkalmazás tömeges műveleteket hajt végre az Azure Cosmos-fiókban tárolt adatokon. Az alkalmazás klónozásához nyisson meg egy parancssort, keresse meg azt a könyvtárat, amelyet másolni szeretne, és futtassa a következő parancsot:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

A klónozott tárház két "BulkImportSample" és "BulkUpdateSample" mintát tartalmaz. Megnyithatja a mintaalkalmazások bármelyikét, frissítheti a kapcsolati karakterláncokat az App.config fájlban az Azure Cosmos DB-fiók kapcsolati karakterláncaival, létrehozhatja a megoldást, és futtathatja azt.

A "BulkImportSample" alkalmazás véletlenszerű dokumentumokat hoz létre, és tömegesen importálja őket az Azure Cosmos-fiókba. A "BulkUpdateSample" alkalmazás tömegesen frissíti az importált dokumentumokat azáltal, hogy javításokat ad meg műveletekként, amelyeket bizonyos dokumentummezőkön kell végrehajtani. A következő szakaszokban áttekintheti a kódot az egyes mintaalkalmazásokban.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Adatok tömeges importálása Egy Azure Cosmos-fiókba

1. Nyissa meg a "BulkImportSample" mappát, és nyissa meg a "BulkImportSample.sln" fájlt.  

2. Az Azure Cosmos DB kapcsolati karakterláncai az App.config fájlból kerülnek beolvasásra a következő kód szerint:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   A tömeges importőr létrehoz egy új adatbázist és egy tárolót az alkalmazás nevével, a tároló nevével és az App.config fájlban megadott átviteli értékekkel.

3. Ezután a DocumentClient objektum közvetlen TCP-kapcsolati móddal inicializálódik:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. A BulkExecutor objektum inicializálása magas újrapróbálkozási értékkel a várakozási idő és a szabályozott kérelmek. Ezután 0-ra vannak állítva, hogy a torlódásvezérlést a BulkExecutor élettartama alatt átadja.  

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

5. Az alkalmazás meghívja a BulkImportAsync API-t. A .NET könyvtár a tömeges importálási API két túlterhelését biztosítja – az egyik elfogadja a szerializált JSON-dokumentumok listáját, a másik pedig a deszerializált POCO-dokumentumok listáját. Ha többet szeretne megtudni az egyes túlterhelt módszerek definícióiról, olvassa el az [API dokumentációját.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet)

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
   
   |**Paraméter**  |**Leírás** |
   |---------|---------|
   |enableUpsert    |   A dokumentumok upsert műveleteinek engedélyezésére alkalmas jelző. Ha a megadott azonosítóval rendelkező dokumentum már létezik, akkor az frissül. Alapértelmezés szerint hamis értékre van állítva.      |
   |disableAutomaticIdGeneration    |    Az azonosító automatikus létrehozásának letiltására figyelmeztető. Alapértelmezés szerint igaz értékre van állítva.     |
   |maxConcurrencyPerPartitionKeyRange    | A partíciókulcs-tartományonkénti egyidejűség maximális mértéke, amelynull értékre van állítva, a függvénytár alapértelmezett értéke 20. |
   |maxInMemorySortingBatchSize     |  A dokumentumenumertorból lekérő dokumentumok maximális száma, amely et az egyes fázisokban az API-hívásnak ad át. A memórián belüli rendezési fázis, amely a tömeges importálás előtt történik, ha ezt a paramétert null értékre állítja, a tár alapértelmezett minimális értékét (documents.count, 1000000) fogja használni.       |
   |törlésToken    |    A törlési jogkivonat, amely szabályosan kilép a tömeges importálási műveletből.     |

   **Tömeges importálási válaszobjektum-definíciója** A tömeges importálási API-hívás eredménye a következő attribútumokat tartalmazza:

   |**Paraméter**  |**Leírás**  |
   |---------|---------|
   |NumberOfDocumentsImported (hosszú)   |  A tömeges importálási API-híváshoz sikeresen importált dokumentumok teljes száma.       |
   |TotalRequestUnitsConsumed (dupla)   |   A tömeges importálási API-hívás által felhasznált teljes kérelemegységek (RU).      |
   |TotalTimeTaken (TimeSpan)    |   A teljes idő a tömeges importálási API-hívás a végrehajtás befejezéséhez.      |
   |BadInputDocuments (lista\<objektum>)   |     A nem formátumos dokumentumok listája, amelyeket nem sikerült importálni a tömeges importálási API-hívásban. Javítsa ki a visszaadott dokumentumokat, és próbálja meg újra az importálást. A hibásan formázott dokumentumok olyan dokumentumokat tartalmaznak, amelyek azonosítóértéke nem karakterlánc (null vagy bármely más adattípus érvénytelennek minősül).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Tömeges frissítési adatok az Azure Cosmos-fiókban

A bulkUpdateAsync API-val frissítheti a meglévő dokumentumokat. Ebben a példában a `Name` mezőt új értékre `Description` állítja, és eltávolítja a mezőt a meglévő bizonylatokból. A támogatott frissítési műveletek teljes készletét az [API dokumentációjában](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)találja.

1. Nyissa meg a "BulkUpdateSample" mappát, és nyissa meg a "BulkUpdateSample.sln" fájlt.  

2. Adja meg a frissítési elemeket a megfelelő mezőfrissítési műveletekkel együtt. Ebben a példában `SetUpdateOperation` a `Name` mező frissítésére `UnsetUpdateOperation` és `Description` a mező eltávolítására használható az összes bizonylatból. Más műveleteket is végrehajthat, például egy dokumentummezőt egy adott értékkel növelve, adott értékeket lenyomhat egy tömbmezőbe, vagy eltávolíthat egy adott értéket egy tömbmezőből. A tömeges frissítési API által biztosított különböző módszerekről az [API dokumentációjában](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)tájékozódhat.

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

3. Az alkalmazás meghívja a BulkUpdateAsync API-t. A BulkUpdateAsync metódus definíciójáról az API [dokumentációjában](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet)tájékozódhat.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync metódus a következő paramétereket fogadja el:**

   |**Paraméter**  |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   A partíciókulcs-tartományonkénti egyidejűség maximális mértéke, ha ezt a paramétert null értékre állítja, a tár az alapértelmezett értéket(20) használja.   |
   |maxInMemorySortingBatchSize    |    Az egyes fázisokban az API-hívásnak átadott frissítési elemek maximális száma. A tömeges frissítés előtt bekövetkező memórián belüli rendezési fázis esetén a paraméter null értékre állításával a tár az alapértelmezett minimális értéket (updateItems.count, 1000000) használja.     |
   | törlésToken|A törlési jogkivonat, amely szabályosan kilép a tömeges frissítési műveletből. |

   **Tömeges frissítésválasz-objektum definíciója** A tömeges frissítési API-hívás eredménye a következő attribútumokat tartalmazza:

   |**Paraméter**  |**Leírás** |
   |---------|---------|
   |NumberOfDocumentsFrissítve (hosszú)    |   A tömeges frissítési API-híváshoz sikeresen frissített dokumentumok száma.      |
   |TotalRequestUnitsConsumed (dupla)   |    A tömeges frissítési API-hívás által felhasznált összes kérelemegység (RUs).    |
   |TotalTimeTaken (TimeSpan)   | A teljes idő a tömeges frissítési API-hívás a végrehajtás befejezéséhez. |
    
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A tömeges végrehajtó könyvtár használatakor vegye figyelembe a következő szempontokat a jobb teljesítmény érdekében:

* A legjobb teljesítmény érdekében futtassa az alkalmazást egy Azure virtuális gépről, amely ugyanabban a régióban van, mint az Azure Cosmos-fiók írási régiója.  

* Javasoljuk, hogy példányosítson `BulkExecutor` ki egy objektumot a teljes alkalmazás egyetlen virtuális gépen belül, amely megfelel egy adott Azure Cosmos-tároló.  

* Mivel egyetlen tömeges művelet API-végrehajtás fogyaszt egy nagy darab az ügyfélgép CPU és a hálózati IO (Ez történik a ívás több feladat belső). Kerülje a tömeges művelet API-hívásokat végrehajtó alkalmazásfolyamaton belül több egyidejű feladat jön létre. Ha egy virtuális gépen futó egyetlen tömeges művelet API-hívása nem tudja felhasználni a teljes tároló átviteli-áteresztőidejét (ha a tároló átviteli > 1 millió RU/s), akkor előnyös, ha külön virtuális gépeket hoz létre a tömeges művelet API-hívásainak egyidejű végrehajtásához.  

* Győződjön `InitializeAsync()` meg arról, hogy a metódus meghívása után egy BulkExecutor objektum ot a cél Cosmos-tároló partíciótérképének lekérése után.  

* Az alkalmazás App.Config, győződjön meg **arról, gcServer** engedélyezve van a jobb teljesítmény
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A könyvtár olyan nyomkövetéseket bocsát ki, amelyek egy naplófájlba vagy a konzolra gyűjthetők. Mindkettő engedélyezéséhez adja hozzá a következő kódot az alkalmazás App.Config fájljába.

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

* A Nuget csomag részleteiről és a kiadási megjegyzésekről a [tömeges végrehajtó SDK részleteiben](sql-api-sdk-bulk-executor-dot-net.md)olvashat.
