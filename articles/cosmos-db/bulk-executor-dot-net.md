---
title: Tömeges végrehajtó .NET könyvtár használatával tömeges műveleteinek elvégzéséhez Azure Cosmos DB |} Microsoft Docs
description: Azure Cosmos DB tömeges végrehajtó .NET könyvtár használatával tömeges importálás és frissítés a dokumentumok Azure Cosmos DB gyűjtemények.
keywords: .NET tömeges végrehajtó
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: b09fd415c442c1e605987a6b25fd938ce04ce5c1
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300771"
---
# <a name="using-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Tömeges végrehajtó .NET kódtár tömeges műveleteinek elvégzéséhez Azure Cosmos DB használatával

Ez az oktatóanyag útmutatás segítségével az Azure Cosmos DB tömeges végrehajtó .NET könyvtár importálása és frissítése a dokumentumok Azure Cosmos DB gyűjteményekhez. Tömeges végrehajtó könyvtárban, és hogyan nyújt segítséget a kihasználja a nagy átviteli sebesség és tárterület kapcsolatos további tudnivalókért lásd: [tömeges végrehajtó kódtárának ismertetése](bulk-executor-overview.md) cikk. Ez az oktatóanyag végigvezeti .NET mintaalkalmazás, amely importálja véletlenszerűen generált dokumentumok tömeges egy Azure Cosmos DB gyűjteménybe. Importálás, után megmutatja, hogyan tömegesen frissíti az importált adatok megadásával a javítások, műveletek végrehajtását az adott dokumentum mező.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nincs telepítve a Visual Studio 2017, töltse le és használja a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Győződjön meg arról, hogy engedélyezi-e a Visual Studio telepítése során az Azure fejlesztési.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt. 

* Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül. Másik lehetőségként használhatja a [Azure Cosmos DB emulátor](https://docs.microsoft.com/azure/cosmos-db/local-emulator) rendelkező a `https://localhost:8081` URI. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.

* Azure Cosmos DB SQL API-fiók létrehozása az ismertetett lépéseket követve [adatbázisfiók létrehozása](create-sql-api-dotnet.md#create-a-database-account) .NET gyors üzembe helyezés cikkének. 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most tegyük váltson kóddal úgy, hogy egyes minta .NET alkalmazások letölti a Githubról. Ezek az alkalmazások Azure Cosmos DB adatok tömeges műveletek végrehajtása Az alkalmazások klónozását, nyisson meg egy parancssort, keresse meg azt a könyvtárat, ahová másolja a őket, és futtassa a következő parancsot:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

A klónozott tárház tartalmaz két minta "BulkImportSample" és "BulkUpdateSample." Nyissa meg a minta-alkalmazások, a kapcsolati karakterláncokkal az App.config fájlban frissítse Azure Cosmos DB fiókja kapcsolati karakterláncok, a megoldás felépítéséhez, és futtassa. 

A "BulkImportSample" alkalmazás véletlenszerű dokumentumok hoz létre, és tömeges Azure Cosmos DB importálja azokat. A "BulkUpdateSample" alkalmazás tömeges javítások, műveletek végrehajtását az adott dokumentum mező megadása az importált dokumentumok frissítéséhez. A következő szakaszokban lévő rendszer tekintse át a kódot minden minta alkalmazásokat.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>A tömeges adatok importálása az Azure Cosmos-Adatbázishoz

1. Keresse meg a "BulkImportSample" mappát, és nyissa meg a "BulkImportSample.sln" fájlt.  

2. Az Azure Cosmos DB kapcsolati karakterláncok olvassa be az App.config fájlt az alábbi kódban látható módon:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   A tömeges importáló hoz létre egy új adatbázist és egy gyűjtemény az adatbázis neve, gyűjtemény nevét, és átviteli értékeket az App.config fájlban megadott. 

3. Ezután a DocumentClient objektum inicializálása közvetlen TCP-kapcsolati mód:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. A BulkExecutor objektum a várakozási idő inicializálva van a magas újrapróbálkozási értéket, és szabályozva kérelmeket. És majd a beállítás a 0 BulkExecutor élettartamuk a torlódásszabályozás átadása.  

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

5. Az alkalmazás a BulkImportAsync API hív meg. A .NET könyvtár biztosít két túlterhelések tömeges importálásához API - egy szerializált JSON-dokumentumok listáját fogadja el, és a másik deszerializált POCO dokumentumok listáját fogadja el. A fenti túlterhelt módszerek meghatározásai kapcsolatos információkért tekintse meg [API-JÁNAK dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync metódus a következő paraméterek fogadja el:**
   
   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |enableUpsert    |   Ahhoz, hogy a dokumentumok upsert jelző. Ha egy dokumentumot a megadott azonosító már létezik, frissül. Alapértelmezés szerint érték hamis.      |
   |disableAutomaticIdGeneration    |    Egy jelzőjét, hogy tiltsa le az automatikus előállításához azonosítóját. Alapértelmezés szerint az értéke igaz.     |
   |maxConcurrencyPerPartitionKeyRange    | A partíciós kulcs tartomány / feldolgozási maximális mértékét, NULL beállítás alapértelmezett értéke 20 használni kívánt szalagtárat okozhat. |
   |maxInMemorySortingBatchSize     |  A dokumentum-enumerátort az API-nak átadott lekért dokumentumok maximális száma az egyes fázisokban hívja.  A memóriában előre feldolgozásra rendezési lépésekhez tömeges importálás előtt beállítás NULL alapértelmezett érték perc (documents.count, 1000000) használni kívánt szalagtárat okozhat.       |
   |cancellationToken    |    A megszakítási token tömeges importálással szabályosan kilép.     |

   **Válasz objektum importdefiníció tömeges** a tömeges importálással API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |NumberOfDocumentsImported (nagy)   |  A tömeges megadott dokumentumból sikeresen importált dokumentumok száma importálja az API-hívás.       |
   |TotalRequestUnitsConsumed (kétirányú)   |   A tömeges által felhasznált teljes kérelemegység (RU) importálása az API-hívás.      |
   |TotalTimeTaken (időtartam)    |   A teljes ideje a tömeges importálással API-hívás végrehajtása befejeződik.      |
   |BadInputDocuments (lista<object>)   |     Importálása nem sikerült a tömeges hibás formátumú dokumentumok listájának importálja az API-hívás. Felhasználó a kell hárítsa el a visszaadott dokumentumok, majd próbálja megismételni a importálása. Hibás formátumú dokumentumok tartalmazzák a dokumentumok, amelyek azonosító értéke nem egy karakterláncot (null vagy bármely más adattípus tekinthető érvénytelen).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Tömeges frissítés adatok az Azure Cosmos-Adatbázisba

A BulkUpdateAsync API használatával frissítheti a meglévő dokumentumokat. Ebben a példában a név mező új értékre beállítva, és távolítsa el a meglévő dokumentumok a Leírás mezőben. A támogatott mező teljes készletének a frissítési műveleteket, hogy [API-JÁNAK dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Keresse meg a "BulkUpdateSample" mappát, és nyissa meg a "BulkUpdateSample.sln" fájlt.  

2. Határozzák meg a frissítés megfelelő mező frissítési műveletek együtt. Ebben a példában a SetUpdateOperation használandó frissíti a név és Leírás mezőben eltávolítása a dokumentumok UnsetUpdateOperation. Műveleteket más növekmény például egy dokumentum mező által megadott értékkel, adott értékekre leküldéses egy tömb mezőbe, vagy egy adott érték eltávolítása egy tömb mező. A tömeges frissítés API által biztosított különböző módszerekkel kapcsolatos további tudnivalókért tekintse meg a [API-JÁNAK dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Az alkalmazás a BulkUpdateAsync API hív meg. A BulkUpdateAsync metódus definíciója kapcsolatos információkért tekintse meg [API-JÁNAK dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync metódus a következő paraméterek fogadja el:**

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   A partíciós kulcs tartomány / feldolgozási maximális mértékét, NULL beállítás alapértelmezett értéke 20 használni kívánt szalagtárat okozhat.   |
   |maxInMemorySortingBatchSize    |    A frissítés elemek számbavevő lekért elemek maximális számát az egyes fázisokban API-hívás átadott tömeges frissítése előtt rendezési lépésekhez előtti feldolgozásra a memóriában, NULL beállítás okozhat használt alapértelmezett érték perc (updateItems.count, könyvtár 1000000).     |
   | cancellationToken|A megszakítási token tömeges frissítés szabályosan kilép. |

   **Tömeges frissítés válasz Objektumdefiníció** a tömeges frissítés API-hívás eredménye a következő attribútumokat tartalmazza:

   |**A paraméter**  |**Leírás** |
   |---------|---------|
   |NumberOfDocumentsUpdated (nagy)    |   A tömeges frissítés API-hívás megadott helyreállításra sikeresen frissített dokumentumok teljes száma.      |
   |TotalRequestUnitsConsumed (kétirányú)   |    A teljes kérelemegység (RU) a tömeges frissítés által használt API-hívás.    |
   |TotalTimeTaken (időtartam)   | A teljes ideje a tömeges frissítése API-hívás végrehajtása befejeződik. |
    
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

Vegye figyelembe a következő szempontokat a jobb teljesítmény, tömeges végrehajtó szalagtár használata esetén:

* A legjobb teljesítmény érdekében futtassa az alkalmazást egy Azure virtuális gépen, amely ugyanabban a régióban, mint a Cosmos DB fiók írási terület.  

* Javasoljuk, hogy a teljes alkalmazáshoz belül egy megadott Cosmos DB gyűjtemény megfelelő egyetlen virtuális gép egyetlen BulkExecutor objektum hozható létre.  

* Mivel egy egyetlen tömeges API művelet végrehajtása egy nagy darabjának az ügyfélszámítógépen Processzor- és a hálózati IO igényel. Több feladat belsőleg származtatását szerint ez történik, elkerülheti a minden egyes végrehajtott tömeges művelet API-hívások alkalmazás folyamaton belül több egyidejű feladatok származtatását. Ha egyetlen virtuális gépen futó egyetlen tömeges művelet API hívása nem tudja használni a teljes gyűjteményt átviteli sebesség (Ha a gyűjtemény átviteli > 1 millió RU/mp), érdemes külön virtuális gépek tömeges egyidejűleg végrehajtásához létrehozása a művelet API-hívások száma.  

* Róla InitializeAsync() után egy BulkExecutor objektum beolvasása a célként megadott Cosmos DB gyűjtemény partíciótérképen példányának.  

* A az alkalmazás App.Config, ellenőrizze, hogy **gcServer** engedélyezve van a teljesítmény növelése érdekében
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A könyvtár bocsát ki a nyomkövetési adatok gyűjthetők a fájlba, vagy a konzolon. Mindkét engedélyezéséhez vegye fel a következő az alkalmazás App.Config.

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
* Tudnivalók a Nuget csomag részleteit és a kibocsátási megjegyzésekben tömeges végrehajtó .net könyvtár:[végrehajtó SDK részletek tömeges](sql-api-sdk-bulk-executor-dot-net.md). 
