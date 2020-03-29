---
title: Tömeges importálási és frissítési műveletek végrehajtása az Azure Cosmos DB tömeges végrehajtó Jázmán-kódtárjával
description: Az Azure Cosmos DB-dokumentumok tömeges importálása és frissítése a tömeges végrehajtó Java-kódtár használatával
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: bf2a2385b3129ddf24ede7f6d851701186b0e33c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445709"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Műveletek tömeges végrehajtása Azure Cosmos DB-adatokon a tömeges végrehajtási Java-kódtárral

Ez az oktatóanyag az Azure Cosmos DB tömeges végrehajtó Jáp-kódtárának importálásához és frissítéséhez való használatával kapcsolatos utasításokat tartalmaz. A tömeges végrehajtó könyvtár, és hogyan segít a masszív átviteli és tárolási tőkeáttétel, [tömeges végrehajtó könyvtár áttekintése](bulk-executor-overview.md) cikket. Ebben az oktatóanyagban egy Java-alkalmazást hoz létre, amely véletlenszerű dokumentumokat hoz létre, és tömegesen importálva egy Azure Cosmos-tárolóba. Importálás után tömegesen frissíti a dokumentum egyes tulajdonságait. 

Jelenleg a tömeges végrehajtó könyvtár csak az Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatja. Ez a cikk ismerteti, hogyan használhatja a tömeges végrehajtó Java-kódtár SQL API-fiókok használatával. A tömeges végrehajtó .NET-kódtár Gremlin API-val való használatáról az [Azure Cosmos DB Gremlin API tömeges műveleteinek végrehajtásáról](bulk-executor-graph-dotnet.md)olvashat.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.  

* Az [Azure Cosmos DB-t ingyenesen,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen és kötelezettségvállalások nélkül is kipróbálhatja. Vagy használhatja az [Azure Cosmos DB-emulátort](https://docs.microsoft.com/azure/cosmos-db/local-emulator) a `https://localhost:8081` végponttal. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.  

* [Java Fejlesztő készlet (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.  

  - Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.

* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és [telepítése](https://maven.apache.org/)  
  
  - Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a Java gyorsútmutató cikk [adatbázis-fiók létrehozása](create-sql-api-java.md#create-a-database-account) című szakaszában ismertetett lépésekkel.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most váltsunk át a kódokkal való munkára egy minta Java-alkalmazás letöltésével a GitHubról. Ez az alkalmazás tömeges műveleteket hajt végre az Azure Cosmos DB-adatokon. Az alkalmazás klónozásához nyisson meg egy parancssort, keresse meg azt a könyvtárat, amelybe az alkalmazást másolni szeretné, és futtassa a következő parancsot:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

A klónozott tárház két "bulkimport" és "bulkupdate" mintát tartalmaz az "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" mappához képest. A "bulkimport" alkalmazás véletlenszerű dokumentumokat hoz létre, és importálja őket az Azure Cosmos DB-be. A "bulkupdate" alkalmazás frissíti az Azure Cosmos DB egyes dokumentumait. A következő szakaszokban áttekintjük a kódot az egyes mintaalkalmazásokban. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Adatok tömeges importálása az Azure Cosmos DB-be

1. Az Azure Cosmos DB kapcsolati karakterláncai argumentumként vannak felolvasva, és a CmdLineConfiguration.java fájlban definiált változókhoz vannak rendelve.  

2. Ezután a DocumentClient objektuminicial inicializálódik a következő utasításokkal:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. A DocumentBulkExecutor objektum inicializálása magas újrapróbálkozási értékekkel történik a várakozási idő és a szabályozott kérelmek esetében. Ezután 0-ra vannak állítva, hogy a torlódások ellenőrzését a DocumentBulkExecutor élettartama alatt átadják.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Hívja meg az importAll API-t, amely véletlenszerű dokumentumokat hoz létre az Azure Cosmos-tárolóba történő tömeges importáláshoz. A parancssori konfigurációk at a CmdLineConfiguration.java fájlban konfigurálhatja.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   A tömeges importálási API json-szerializált dokumentumok gyűjteményét fogadja el, és a következő szintaxissal rendelkezik, további részletekért olvassa el az [API dokumentációját:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Az importAll metódus a következő paramétereket fogadja el:
 
   |**Paraméter**  |**Leírás**  |
   |---------|---------|
   |isUpsert    |   A dokumentum upsert-jét lehetővé tevő zászló. Ha egy adott azonosítóval rendelkező dokumentum már létezik, akkor az frissül.  |
   |disableAutomaticIdGeneration     |   Az azonosító automatikus létrehozásának letiltására figyelmeztető. Alapértelmezés szerint igaz értékre van állítva.   |
   |maxConcurrencyPerPartitionRange    |  A partíciókulcs-tartományonkénti egyidejűség maximális mértéke. Az alapértelmezett érték 20.  |

   **Tömeges importálási válaszobjektum-definíciója** A tömeges importálási API-hívás eredménye a következő beválasztási módszereket tartalmazza:

   |**Paraméter**  |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   A tömeges importálási API-híváshoz sikeresen importált dokumentumok teljes száma.      |
   |dupla getTotalRequestUnitsConsumed()   |  A tömeges importálási API-hívás által felhasznált teljes kérelemegységek (RU).       |
   |Időtartam getTotalTimeTaken()   |    A tömeges importálási API-hívás által a végrehajtás befejezéséhez szükséges teljes idő.     |
   |A\<geterrors()> |  Lekéri a hibák listáját, ha a tömeges importálási API-híváshoz megadott kötegből néhány dokumentum nem sikerült beszúrni.       |
   |Objektum\<> GetBadInputDocuments() listázása  |    A nem formátumos dokumentumok listája, amelyeket nem sikerült importálni a tömeges importálási API-hívásban. A felhasználónak ki kell javítania a visszaadott dokumentumokat, és újra meg kell próbálnia az importálást. A hibásan formázott dokumentumok olyan dokumentumokat tartalmaznak, amelyek azonosítóértéke nem karakterlánc (null vagy bármely más adattípus érvénytelennek minősül).     |

5. Miután a tömeges importálási alkalmazás készen állt, az "mvn clean package" paranccsal készítse el a parancssori eszközt a forrásból. Ez a parancs jar fájlt hoz létre a célmappában:  

   ```java
   mvn clean package
   ```

6. A célfüggőségek létrehozása után a tömeges importőr alkalmazást a következő paranccsal hívhatja meg:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   A tömeges importőr létrehoz egy új adatbázist és egy gyűjteményt az App.config fájlban megadott adatbázisnévvel, gyűjteménynévvel és átviteli értékekkel. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Tömeges frissítési adatok az Azure Cosmos DB-ben

A bulkUpdateAsync API-val frissítheti a meglévő dokumentumokat. Ebben a példában a Név mezőt új értékre állítja be, és eltávolítja a Leírás mezőt a meglévő bizonylatokból. A támogatott mezőfrissítési műveletek teljes készletét lásd az [API dokumentációjában.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor) 

1. A frissítési elemeket a megfelelő mezőfrissítési műveletekkel együtt határozza meg. Ebben a példában a SetUpdateOperation segítségével frissítheti a Név mezőt, az UnsetUpdateOperation mezőt pedig a Leírás mező t az összes dokumentumból. Más műveleteket is végrehajthat, például egy dokumentummezőt egy adott értékkel növelve, adott értékeket lenyomhat egy tömbmezőbe, vagy eltávolíthat egy adott értéket egy tömbmezőből. A tömeges frissítési API által biztosított különböző módszerekről az [API dokumentációjában](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)olvashat.  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Hívja meg az updateAll API-t, amely véletlenszerű dokumentumokat hoz létre, majd tömegesen importálható egy Azure Cosmos-tárolóba. Beállíthatja, hogy a parancssori konfigurációk átadhatók legyenek a CmdLineConfiguration.java fájlban.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A tömeges frissítési API elfogadja a frissítendő elemek gyűjteményét. Minden frissítési elem meghatározza az azonosítóval és partíciókulcs-értékkel azonosított dokumentumon végrehajtandó mezőfrissítési műveletek listáját. További részletekért tekintse meg az [API dokumentációját:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Az updateAll metódus a következő paramétereket fogadja el:

   |**Paraméter** |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  A partíciókulcs-tartományonkénti egyidejűség maximális mértéke. Az alapértelmezett érték 20.  |
 
   **Tömeges importálási válaszobjektum-definíciója** A tömeges importálási API-hívás eredménye a következő beválasztási módszereket tartalmazza:

   |**Paraméter** |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   A tömeges frissítési API-híváshoz sikeresen frissített dokumentumok teljes száma.      |
   |dupla getTotalRequestUnitsConsumed() |  A tömeges frissítési API-hívás által felhasznált teljes kérelemegységek (RU).       |
   |Időtartam getTotalTimeTaken()  |   A teljes idő által a tömeges frissítési API-hívás a végrehajtás befejezéséhez.      |
   |A\<geterrors()>   |    Lekéri a hibák listáját, ha a tömeges frissítési API-híváshoz megadott kötegből néhány dokumentum beszúrása nem sikerült.      |

3. Miután a tömeges frissítési alkalmazás készen áll, az "mvn clean package" paranccsal készítse el a parancssori eszközt a forrásból. Ez a parancs jar fájlt hoz létre a célmappában:  

   ```
   mvn clean package
   ```

4. A célfüggőségek létrehozása után a tömeges frissítési alkalmazást a következő paranccsal hívhatja meg:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A tömeges végrehajtó könyvtár használatakor vegye figyelembe a következő szempontokat a jobb teljesítmény érdekében:

* A legjobb teljesítmény érdekében futtassa az alkalmazást egy Azure virtuális gép ugyanabban a régióban, mint a Cosmos DB-fiók írási régióban.  
* A nagyobb átviteli érték eléréséhez:  

   * Állítsa a JVM halomméretét elég nagy számra, hogy elkerülje a memóriaproblémákat a nagy számú dokumentum kezelése során. Javasolt halommemória-méret: max(3GB, 3 * sizeof(minden dokumentum átadott tömeges importálási API egy kötegben)).  
   * Van egy előfeldolgozási idő, ami miatt nagyobb átviteli teljesítményt kap, ha nagy számú dokumentumot hajt végre tömeges műveletek et. Így ha 10 000 000 dokumentumot szeretne importálni, a tömeges importálás futtatása 10-szer 10 nagy méretű, 1 000 000 méretű dokumentumon előnyösebb, mint a tömeges importálás futtatása 100-szor 100 000 méretű dokumentumokon.  

* Javasoljuk, hogy egyetlen DocumentBulkExecutor objektumot példányosítson ki a teljes alkalmazás egyetlen virtuális gépen belül, amely egy adott Azure Cosmos-tárolónak felel meg.  

* Mivel egyetlen tömeges művelet API-végrehajtás fogyaszt egy nagy darab az ügyfélgép CPU és hálózati IO.Since a single bulk operation API execution consumes a large chunk of the client machine's CPU and network IO. Ez úgy történik, hogy több feladatot hoz létre belsőleg, elkerülheti, hogy több egyidejű feladatot hozzon létre az alkalmazásfolyamaton belül minden egyes tömeges művelet API-hívása. Ha egy virtuális gépen futó egyetlen tömeges művelet API-hívása nem tudja felhasználni a teljes tároló átviteli-áteresztőidejét (ha a tároló átviteli > 1 millió RU/s), célszerű külön virtuális gépeket létrehozni a tömeges működésű API-hívások egyidejű végrehajtásához.

    
## <a name="next-steps"></a>További lépések
* Ha többet szeretne megtudni a maven csomag részleteiről és a tömeges végrehajtó Java könyvtárának kiadási feljegyzéseiről, olvassa el[a tömeges végrehajtó SDK részletei .](sql-api-sdk-bulk-executor-java.md)


