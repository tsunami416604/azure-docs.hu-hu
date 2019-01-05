---
title: Tömeges végrehajtó Java-kódtár segítségével hajtsa végre a tömeges importálási és frissítési műveleteket az Azure Cosmos DB-ben
description: Tömeges importálás, és frissítse az Azure Cosmos DB-dokumentumot tömeges végrehajtó Java-kódtár használatával.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 8031c8810d9916c3f6e02e1f2474c1ca9cdd528d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038755"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Tömeges végrehajtó Java-kódtár használatával tömeges műveletek végrehajtása az Azure Cosmos DB-adatai

Ebben az oktatóanyagban ez útmutatást nyújt az Azure Cosmos DB tömeges végrehajtó Java-kódtár használatával importálása és frissítése az Azure Cosmos DB-dokumentumot. Tömeges végrehajtó kódtár, és segítséget nyújt a nagy átviteli sebesség és a storage kapcsolatos további információkért lásd: [végrehajtó Ügyfélkódtár áttekintése tömeges](bulk-executor-overview.md) cikk. Ebben az oktatóanyagban a Java-alkalmazás által generált, véletlenszerű dokumentumok létrehozása és tömegesen importálni egy Azure Cosmos DB-tárolóhoz. Az importálás után lesz tömeges frissítése néhány tulajdonságát egy dokumentumot. 

Tömeges végrehajtó könyvtár jelenleg az Azure Cosmos DB SQL API-t, és csak a Gremlin API-fiókok által támogatott. Ez a cikk ismerteti a tömeges végrehajtó .net-kódtár használata SQL API-fiókok. Tömeges végrehajtó .NET-kódtár használatával a Gremlin API-val kapcsolatos további információkért lásd: [tömeges műveletek végrehajtása az Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.  

* Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül. Vagy használhatja a [Azure Cosmos DB Emulatort](https://docs.microsoft.com/azure/cosmos-db/local-emulator) együtt a `https://localhost:8081` URI-t. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticating-requests) című részben található.  

* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.  

  - Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.

* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és [telepítése](https://maven.apache.org/)  
  
  - Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot az ismertetett lépéseket követve [adatbázisfiók létrehozása](create-sql-api-java.md#create-a-database-account) Java rövid cikkének.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most már váltsunk át kódok használatára egy Java-mintaalkalmazás letöltése a Githubról. Ez az alkalmazás Azure Cosmos DB-adatai tömeges műveleteket végez. Klónozza az alkalmazást, nyisson meg egy parancssort, lépjen abba a könyvtárba, ahol szeretné másolni az alkalmazás, és futtassa a következő parancsot:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

A klónozott adattár tartalmazza a két minta "bulkimport" és "bulkupdate" a "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" mappa viszonyítva. A "bulkimport" alkalmazás véletlenszerű dokumentumokat hoz létre, és importálja őket az Azure Cosmos DB-hez. A "bulkupdate" alkalmazás frissíti az Azure Cosmos DB néhány dokumentumot. A következő szakaszokban át fogjuk tekinteni a mintaalkalmazások mindegyike a kódot. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>A tömeges adatok importálása az Azure Cosmos DB-hez

1. Az Azure Cosmos DB kapcsolati karakterláncok beolvasása argumentumként és CmdLineConfiguration.java fájlban definiált változókat rendelve.  

2. Ezután a DocumentClient objektum inicializálva van a következő utasítások használatával:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. A DocumentBulkExecutor objektum inicializálása egy nagy újrapróbálkozási értéket a várakozási idő, és a kérelmek szabályozva. És ezután azok értéke 0 DocumentBulkExecutor élettartamuk a torlódásszabályozás átadása.  

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

4. Hívja meg a paraméter értéke importAll API, amely létrehoz egy Azure Cosmos DB-tárolón történő tömeges véletlenszerű dokumentumok. Beállíthatja, hogy a parancssor konfigurációk CmdLineConfiguration.java fájlon belül.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   A tömeges importálási API elfogadja a JSON-szerializált dokumentumok gyűjteménye, és rendelkezik a következő szintaxist, további részletekért, lásd: a [API-dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   A paraméter értéke importAll módszer a következő paramétereket fogadja:
 
   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |isUpsert    |   Ahhoz, hogy a dokumentumok upsert jelző. Ha egy dokumentumot a megadott azonosító már létezik, frissül.  |
   |disableAutomaticIdGeneration     |   Azt a jelzőt, automatikus generálása azonosítójának letiltása Alapértelmezés szerint az értéke igaz.   |
   |maxConcurrencyPerPartitionRange    |  Partíciókulcs-tartományonként párhuzamosság maximális foka. Az alapértelmezett értéke 20.  |

   **Tömeges importálás válasz Objektumdefiníció** a tömeges importálás API-hívás eredménye a következő get metódust tartalmaz:

   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Sikeresen importálva lettek a tömeges megadott dokumentumból dokumentumok száma importálja az API-hívás.      |
   |dupla getTotalRequestUnitsConsumed()   |  A tömeges által felhasznált teljes kérelemegység (RU) importálni az API-hívás.       |
   |Időtartam getTotalTimeTaken()   |    Teljes idő a tömeges importálás API-hívás végrehajtása befejeződik.     |
   |Lista<Exception> getErrors() |  Hibák listáját olvassa be, ha néhány dokumentumot a köteg megadott tömeges importálása nem sikerült beszúrni első API-hívás.       |
   |Lista<Object> getBadInputDocuments()  |    A lista rossz formátumú dokumentumok importálása nem sikerült a tömeges importálása API-hívás. Felhasználó kell hárítsa el a dokumentumokat ad vissza, és próbálkozzon újra az importálással. Hibás formátumú dokumentumok tartalmazzák a dokumentumok, amelynek azonosító értéke nem egy karakterláncot (NULL értékű vagy bármely más adattípus érvénytelen akkor tekinthető).     |

5. Miután a tömeges importálása alkalmazást kész, a 'tiszta csomag mvn' parancs használatával hozhat létre a parancssori eszköz forrásból. Ez a parancs létrehoz egy jar-fájlt a cél mappában:  

   ```java
   mvn clean package
   ```

6. Után jönnek létre a cél függőségeket, a tömeges programu Pro import alkalmazás hívhatja meg a következő paranccsal:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   A tömeges programu Pro import hoz létre egy új adatbázist és gyűjteményt az adatbázisnév, a gyűjtemény neve és az átviteli sebesség értékeket az App.config fájlban megadott. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Tömeges frissítés adatainak az Azure Cosmos DB

Meglévő dokumentumok frissítheti a BulkUpdateAsync API-val. Ebben a példában egy új értékre állítva a név mező, és a Leírás mező eltávolítása a meglévő dokumentumok. A támogatott mező teljes körű frissítési műveletek című témakörben talál [API-dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Határozza meg a frissítés elemeket, és megfelelő mező frissítési műveleteket. Ebben a példában a SetUpdateOperation használandó frissíti a nevét és a Leírás mező eltávolítása az összes dokumentum UnsetUpdateOperation. Végez, és más műveletek növekmény például egy dokumentum mező egy adott érték, adott értékekre leküldése egy tömb mezőt, vagy egy adott érték eltávolítása egy tömb mező. A tömeges frissítés API által biztosított különböző módszerekkel kapcsolatos további információkért tekintse meg a [API-dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Hívja meg az API-t kell majd importálja az Azure Cosmos DB-tárolók tömeges véletlenszerű dokumentumokat létrehozó updateAll. Konfigurálhatja a CmdLineConfiguration.java fájlban átadandó parancssori konfigurációkat.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A tömeges frissítés API elfogadja a frissítendő elemek egy gyűjteménye. Minden elem frissítése listáját adja meg a mező frissítési műveleteket kell végrehajtani egy Azonosítót és a partíciókulcs-értékkel által azonosított dokumentumot. További részletekért tekintse meg a [API-dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   A updateAll metódus a következő paramétereket fogadja:

   |**A paraméter** |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Partíciókulcs-tartományonként párhuzamosság maximális foka. Az alapértelmezett értéke 20.  |
 
   **Tömeges importálás válasz Objektumdefiníció** a tömeges importálás API-hívás eredménye a következő get metódust tartalmaz:

   |**A paraméter** |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   A sikeresen frissített a dokumentumból dokumentumok teljes számát a tömeges frissítés megadott API-hívás.      |
   |dupla getTotalRequestUnitsConsumed() |  A teljes kérelemegység (RU) a tömeges frissítés által használt API-hívás.       |
   |Időtartam getTotalTimeTaken()  |   A tömeges teljes idő frissítés API-hívás végrehajtása befejeződik.      |
   |Lista<Exception> getErrors()   |     Hibák listáját olvassa be, ha olyan néhány dokumentumot a batch a tömeges frissítés API-hívás nem sikerült beszúrni beolvasása.      |

3. Miután a tömeges frissítése alkalmazást kész, a 'tiszta csomag mvn' parancs használatával hozhat létre forrásból a parancssori eszköz. Ez a parancs létrehoz egy jar-fájlt a cél mappában:  

   ```
   mvn clean package
   ```

4. Miután a cél függőségek jönnek létre, hajthatók tömeges frissítés alkalmazása a következő paranccsal:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A jobb teljesítmény érdekében a következő szempontokat vegye figyelembe, tömeges végrehajtó szalagtár használata esetén:

* A legjobb teljesítmény érdekében futtassa az alkalmazást és a Cosmos DB-fiók írási régió ugyanabban a régióban az Azure virtuális gépből.  
* Nagyobb átviteli sebesség eléréséhez:  

   * Állítsa be a JVM-halommemória mérete elég nagy számú, nagy számú dokumentumot kezelése bármely memória probléma elkerülése érdekében. Javasolt halommemória mérete: maximális (3GB, 3 * (minden dokumentum tömeges átadott API importálása egy kötegben) sizeof).  
   * Nincs egy előfeldolgozási idő, ami kap magasabb átviteli sebesség tömeges műveletek nagy számú dokumentumot a végrehajtása során. Tehát 10,000,000 dokumentumok importálni kívánt, ha futó tömeges importálás 10 alkalommal dokumentumok 10 tömeges mérete 1 000 000 mindegyike célszerű a futó tömeges importálás 100-szor dokumentumok 100 tömeges mérete 100 000 dokumentumok mindegyike-nál.  

* Javasoljuk, hogy a teljes alkalmazás egyetlen virtuális gép, amely megfelel egy adott Azure Cosmos DB-tárolón belül egyetlen DocumentBulkExecutor objektumot hozza létre.  

* Mivel egy egyszeri tömeges API művelet végrehajtása egy nagy szövegrészletet, az ügyfél gépének Processzor- és hálózati i/o használ fel. Több feladat indítja belsőleg szerint ez történik, elkerülheti, hogy az alkalmazás folyamatának minden tömeges műveletet végrehajtó API-hívások belül több egyidejű feladat indítja. Nem lehet felhasználni a teljes tárolót átviteli egyetlen virtuális gépen futó egyetlen tömeges művelet API hívás esetén (Ha a tároló átviteli > 1 millió RU/s), ezért célszerű egyidejűleg hajtsa végre a tömeges különálló virtuális gépet hoz létre a művelet API-hívások száma.

    
## <a name="next-steps"></a>További lépések
* További információ a maven csomag részletei és kibocsátási megjegyzések tömeges végrehajtó Java kódtár:[végrehajtó SDK részletek tömeges](sql-api-sdk-bulk-executor-java.md).


