---
title: Tömeges importálási és frissítési műveletek végrehajtása a tömeges végrehajtó .NET-kódtár használatával Azure Cosmos DB
description: A Azure Cosmos DB dokumentumok tömeges importálása és frissítése a tömeges végrehajtó .NET-kódtár használatával.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d76426e738d78391b92b008e821672017520b7d2
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218401"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Tömeges műveletek végrehajtása a tömeges végrehajtó .NET-kódtár használatával Azure Cosmos DB

Ez az oktatóanyag útmutatást nyújt a tömeges végrehajtó .NET-függvénytár használatáról a dokumentumok Azure Cosmos-tárolóba történő importálásához és frissítéséhez. Ha többet szeretne megtudni a tömeges végrehajtó függvénytárról, valamint arról, hogy miként segíti a nagy átviteli sebesség és a tárterület kihasználását, tekintse meg a [tömeges végrehajtó függvénytár áttekintését](bulk-executor-overview.md) ismertető cikket. Ebben az oktatóanyagban egy minta .NET-alkalmazást fog látni, amely a véletlenszerűen generált dokumentumokat egy Azure Cosmos-tárolóba importálja. Az importálást követően bemutatjuk, hogyan frissítheti az importált adatok tömeges frissítését úgy, hogy az adott dokumentum mezőin végrehajtandó műveletekként megadhatja a javításokat.

Jelenleg a tömeges végrehajtó függvénytárat csak a Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatják. Ez a cikk a tömeges végrehajtó .NET-függvénytár SQL API-fiókokkal való használatát ismerteti. A Gremlin API-fiókokkal rendelkező tömeges végrehajtó .NET-függvénytár használatáról további információt a következő témakörben talál: [tömeges műveletek végrehajtása a Azure Cosmos db GREMLIN API-ban](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Előfeltételek

* Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja a [Visual studio 2019 Community Editiont](https://www.visualstudio.com/downloads/). Győződjön meg arról, hogy engedélyezi az "Azure-fejlesztést" a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

* Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül. Vagy használhatja a [Azure Cosmos db emulátort](https://docs.microsoft.com/azure/cosmos-db/local-emulator) a `https://localhost:8081` végponttal. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a .NET gyors üzembe helyezési cikk [adatbázis-fiók létrehozása](create-sql-api-dotnet.md#create-account) című szakaszában ismertetett lépések segítségével.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most váltson a kóddal való használatra egy minta .NET-alkalmazás letöltésével a GitHubról. Ez az alkalmazás tömeges műveleteket hajt végre az Azure Cosmos-fiókjában tárolt adatokon. Az alkalmazás klónozásához nyisson meg egy parancssort, Navigáljon arra a könyvtárra, ahová másolni szeretné, majd futtassa a következő parancsot:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

A klónozott tárház két mintát tartalmaz: "BulkImportSample" és "BulkUpdateSample". A minta alkalmazások közül bármelyiket megnyitva frissítheti az app. config fájlban található kapcsolatok karakterláncait a Azure Cosmos DB fiókja kapcsolatainak karakterláncait, felépítheti a megoldást, és futtathatja.

A "BulkImportSample" alkalmazás véletlenszerű dokumentumokat hoz létre, és tömegesen importálja őket az Azure Cosmos-fiókjába. A "BulkUpdateSample" alkalmazás az importált dokumentumokat úgy frissíti, hogy a javításokat az adott dokumentum mezőin végrehajtandó műveletekként határozza meg. A következő részekben ezeket a példákat fogja áttekinteni a kódban.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Az Azure Cosmos-fiókba való tömeges importálás

1. Navigáljon a "BulkImportSample" mappára, és nyissa meg a "BulkImportSample. SLN" fájlt.  

2. A Azure Cosmos DB a kapcsolatok karakterláncait az app. config fájlból kéri le, ahogy az a következő kódban látható:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   A tömeges importáló létrehoz egy új adatbázist és egy tárolót, amelynek az adatbázis neve, a tároló neve, valamint az app. config fájlban megadott átviteli sebesség.

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

5. Az alkalmazás meghívja a BulkImportAsync API-t. A .NET-függvénytár két túlterhelést biztosít a tömeges importálási API-nak, amely elfogadja a szerializált JSON-dokumentumok listáját, valamint a másikat, amely elfogadja a deszerializált POCO-dokumentumok listáját. Ha többet szeretne megtudni ezekről a túlterhelt módszerekről, tekintse meg az [API dokumentációját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   |enableUpsert    |   Egy jelző, amely engedélyezi a upsert műveleteket a dokumentumokon. Ha a megadott AZONOSÍTÓJÚ dokumentum már létezik, frissül. Alapértelmezés szerint hamis értékre van állítva.      |
   |disableAutomaticIdGeneration    |    Azt a jelzőt, automatikus generálása azonosítójának letiltása Alapértelmezés szerint az értéke igaz.     |
   |maxConcurrencyPerPartitionKeyRange    | A partíciós kulcs tartományának maximális párhuzamossági foka, a NULL értékre állítás a függvénytár alapértelmezett értékét fogja használni. |
   |maxInMemorySortingBatchSize     |  A dokumentum-enumerálásból lekért dokumentumok maximális száma, amely minden fázisban az API-hívásnak lesz átadva. A tömeges importálás előtt megjelenő memóriabeli rendezési fázisban a paraméter null értékre állítása esetén a könyvtár az alapértelmezett minimális értéket fogja használni (Documents. Count, 1000000).       |
   |cancellationToken    |    A lemondási token, amely szabályosan kilép a tömeges importálási műveletből.     |

   **Tömeges importálási válasz objektumának definíciója** A tömeges importálási API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |NumberOfDocumentsImported (hosszú)   |  Azon dokumentumok teljes száma, amelyek importálása sikeresen megtörtént a tömeges importálási API-híváshoz megadott összes dokumentumból.       |
   |TotalRequestUnitsConsumed (dupla)   |   A tömeges által felhasznált teljes kérelemegység (RU) importálni az API-hívás.      |
   |TotalTimeTaken (TimeSpan)    |   A tömeges importálási API hívásának teljes ideje a végrehajtás befejezéséhez.      |
   |BadInputDocuments (objektum\<listázása >)   |     A lista rossz formátumú dokumentumok importálása nem sikerült a tömeges importálása API-hívás. Javítsa ki a visszaadott dokumentumokat, és próbálkozzon újra az importálással. Hibás formátumú dokumentumok tartalmazzák a dokumentumok, amelynek azonosító értéke nem egy karakterláncot (NULL értékű vagy bármely más adattípus érvénytelen akkor tekinthető).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Az Azure Cosmos-fiókban tárolt adatmennyiségek tömeges frissítése

Meglévő dokumentumok frissítheti a BulkUpdateAsync API-val. Ebben a példában a `Name` mezőt egy új értékre állítja be, és eltávolítja a `Description` mezőt a meglévő dokumentumokból. A támogatott frissítési műveletek teljes készletét az [API dokumentációjában](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)találja.

1. Navigáljon a "BulkUpdateSample" mappára, és nyissa meg a "BulkUpdateSample. SLN" fájlt.  

2. A frissítési elemek meghatározása a megfelelő mező-frissítési műveletekkel együtt. Ebben a `SetUpdateOperation` példában a használatával frissíti a `Name` mezőt, és `UnsetUpdateOperation` eltávolítja a `Description` mezőt az összes dokumentumból. Végez, és más műveletek növekmény például egy dokumentum mező egy adott érték, adott értékekre leküldése egy tömb mezőt, vagy egy adott érték eltávolítása egy tömb mező. Ha többet szeretne megtudni a tömeges frissítési API által nyújtott különböző módszerekről, tekintse meg az [API dokumentációját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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
   |maxConcurrencyPerPartitionKeyRange    |   A partíciós kulcs tartományának maximális párhuzamossági foka, ha ezt a paramétert NULL értékre állítja, a függvénytár az alapértelmezett értéket fogja használni (20).   |
   |maxInMemorySortingBatchSize    |    Az egyes szakaszok API-hívása által a frissítési elemek enumerálása által lekért frissítési elemek maximális száma. A tömeges frissítés előtti, memóriabeli rendezési fázisban a paraméter null értékre állításával a könyvtár az alapértelmezett minimális értéket fogja használni (updateItems. Count, 1000000).     |
   | cancellationToken|A lemondási token, amely szabályosan kilép a tömeges frissítési műveletből. |

   **Tömeges frissítés válasz objektumának definíciója** A tömeges frissítési API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |NumberOfDocumentsUpdated (hosszú)    |   Azon dokumentumok száma, amelyek sikeresen frissítve lettek a tömeges frissítési API-híváshoz megadott összes dokumentumból.      |
   |TotalRequestUnitsConsumed (dupla)   |    A tömeges frissítési API-hívás által felhasznált összes kérési egység (RUs).    |
   |TotalTimeTaken (TimeSpan)   | A tömeges frissítési API-hívás által a végrehajtás befejezéséhez szükséges teljes idő. |
    
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A tömeges végrehajtó függvénytár használata esetén vegye figyelembe a következő szempontokat a jobb teljesítmény érdekében:

* A legjobb teljesítmény érdekében az alkalmazást egy olyan Azure-beli virtuális gépről futtassa, amely ugyanabban a régióban található, mint az Azure Cosmos-fiók írási régiója.  

* Azt javasoljuk, hogy egyetlen, egy adott Azure `BulkExecutor` Cosmos-tárolónak megfelelő virtuális gépen belül egyetlen objektumot hozza létre a teljes alkalmazáshoz.  

* Mivel egyetlen tömeges művelet API-végrehajtása nagy mennyiségű adatrészletet használ az ügyfélszámítógép processzor-és hálózati IO-jával (ez a több feladat belső elindításával történik). Ne indítson el egyszerre több egyidejű feladatot az alkalmazási folyamaton belül, amely tömeges műveleti API-hívásokat hajt végre. Ha egyetlen virtuális gépen futó egyetlen tömeges művelet API-hívása nem tudja felhasználni a teljes tároló átviteli sebességét (ha a tároló átviteli sebessége > 1 000 000 RU/s), akkor érdemes külön virtuális gépeket létrehozni az egyidejű végrehajtáshoz a tömeges művelet API-hívásai.  

* Győződjön meg `InitializeAsync()` arról, hogy a metódus meghívása egy BulkExecutor-objektum példányának beolvasását követően a cél Cosmos-tároló partíciós térképének beolvasása céljából  

* Az alkalmazás app. config fájljában ellenőrizze, hogy a **gcServer** engedélyezve van-e a jobb teljesítmény érdekében
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A könyvtár olyan nyomkövetéseket bocsát ki, amelyek egy naplófájlba vagy a konzolon gyűjthetők össze. Mindkét beállítás engedélyezéséhez adja hozzá a következő kódot az alkalmazás app. config fájljához.

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

* A Nuget-csomag részleteivel és a kibocsátási megjegyzésekkel kapcsolatos további információkért tekintse meg a [tömeges végrehajtó SDK részleteit](sql-api-sdk-bulk-executor-dot-net.md).
