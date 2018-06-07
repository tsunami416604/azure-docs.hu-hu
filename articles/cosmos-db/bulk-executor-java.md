---
title: Tömeges műveleteinek elvégzéséhez Azure Cosmos DB BulkExecutor Java kódtár használatával |} Microsoft Docs
description: Azure Cosmos DB BulkExecutor Java kódtár segítségével tömeges importálás és frissítés a dokumentumok Azure Cosmos DB gyűjtemények.
keywords: Java tömeges végrehajtó
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 77135ec5f62636d1dd634361da345b00d98ad918
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610242"
---
# <a name="use-bulkexecutor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Azure Cosmos DB adatok tömeges műveletek végrehajtásához használja a BulkExecutor Java kódtár

Ez az oktatóanyag útmutatás az Azure Cosmos DB tömeges végrehajtó Java kódtár segítségével importálja, és az Azure Cosmos DB dokumentumok frissítéséhez. BulkExecutor könyvtárban, és hogyan nyújt segítséget a kihasználja a nagy átviteli sebesség és tárterület kapcsolatos további tudnivalókért lásd: [BulkExecutor könyvtárának](bulk-executor-overview.md) cikk. Ebben az oktatóanyagban hoz létre egy Java-alkalmazást, amely véletlenszerű dokumentumok hoz létre, és importálja az Azure Cosmos DB gyűjteményt tömeges. Tömeges lesz, importálása után frissíteni néhány tulajdonságát egy dokumentumot. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.  

* Az [Azure Cosmos DB-t kipróbálhatja ingyenesen](https://azure.microsoft.com/try/cosmosdb/), Azure-előfizetés, díjfizetés és elköteleződés nélkül. Másik lehetőségként használhatja a [Azure Cosmos DB emulátor](https://docs.microsoft.com/azure/cosmos-db/local-emulator) rendelkező a `https://localhost:8081` URI. Az elsődleges kulcs megtalálható [intézett hitelesítési kérésekhez](local-emulator.md#authenticating-requests).  

* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
  - Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.  

  - Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.

* [Maven](http://maven.apache.org/download.cgi) bináris archívum [letöltése](http://maven.apache.org/install.html) és [telepítése](http://maven.apache.org/)  
  
  - Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.

* Azure Cosmos DB SQL API-fiók létrehozása az ismertetett lépéseket követve [adatbázisfiók létrehozása](create-sql-api-java.md#create-a-database-account) Java gyors üzembe helyezés cikkének.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most tegyük váltani a Githubról Java mintaalkalmazás letöltésével kód használata. Ez az alkalmazás Azure Cosmos DB adatok tömeges műveleteket végez. Az alkalmazás klónozza, nyisson meg egy parancssort, keresse meg azt a könyvtárat, ahová az alkalmazás másolja, majd futtassa a következő parancsot:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

A klónozott tárház két minták "bulkimport" és "bulkupdate" a "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" mappa alapján értelmezett relatív tartalmazza. A "bulkimport" alkalmazás véletlenszerű dokumentumok hoz létre, és importálja azokat az Azure Cosmos-Adatbázishoz. A "bulkupdate" alkalmazás bizonyos Azure Cosmos DB dokumentumainak frissíti. A következő szakaszokban lévő azt rendszer tekintse át a kódot minden minta alkalmazásokat. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>A tömeges adatok importálása az Azure Cosmos-Adatbázishoz

1. Az Azure Cosmos DB kapcsolati karakterláncok beolvasása argumentumként és rendelt CmdLineConfiguration.java fájlban definiált változókat.  

2. Ezután a DocumentClient objektum inicializálása a következő utasítások használatával:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. A DocumentBulkExecutor objektum a várakozási idő inicializálva van a magas újrapróbálkozási értéket, és szabályozva kérelmeket. És majd a beállítás a 0 DocumentBulkExecutor élettartamuk a torlódásszabályozás átadása.  

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
     offerThroughput) // throughput you want to allocate for bulk import out of the collection's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. A paraméter értéke importAll importálása egy Cosmos-DB Azure gyűjteménybe tömeges véletlenszerű dokumentumok előállított API hívása. Beállíthatja, hogy a parancssor konfigurációk CmdLineConfiguration.java fájlon belül.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   A tömeges importálási API JSON-szerializált dokumentumok gyűjteménye fogad el, és van-e további részleteket a következő szintaxist, tekintse meg a [API-JÁNAK dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   A paraméter értéke importAll metódus a következő paraméterek fogadja el:
 
   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |isUpsert    |   Ahhoz, hogy a dokumentumok upsert jelző. Ha egy dokumentumot a megadott azonosító már létezik, frissül.  |
   |disableAutomaticIdGeneration     |   Egy jelzőjét, hogy tiltsa le az automatikus előállításához azonosítóját. Alapértelmezés szerint az értéke igaz.   |
   |maxConcurrencyPerPartitionRange    |  A maximális feldolgozási kulcs partíciótartomány / mértékét. Az alapértelmezett érték 20.  |

   **Válasz objektum importdefiníció tömeges** a tömeges importálással API-hívás eredménye a következő get metódust tartalmaz:

   |**A paraméter**  |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   A tömeges megadott dokumentumból sikeresen importált dokumentumok száma importálja az API-hívás.      |
   |kettős getTotalRequestUnitsConsumed()   |  A tömeges által felhasznált teljes kérelemegység (RU) importálása az API-hívás.       |
   |Időtartam getTotalTimeTaken()   |    A teljes ideje a tömeges importálással API-hívás végrehajtása befejeződik.     |
   |Lista<Exception> getErrors() |  Lekérdezi a hibák listáját, ha egyes dokumentumok kívül a kötegben tömeges számára megadott importálása nem sikerült beszúrni beszerzése API-hívás.       |
   |Lista<Object> getBadInputDocuments()  |    Importálása nem sikerült a tömeges hibás formátumú dokumentumok listájának importálja az API-hívás. Felhasználó a kell hárítsa el a visszaadott dokumentumok, majd próbálja megismételni a importálása. Hibás formátumú dokumentumok tartalmazzák a dokumentumok, amelyek azonosító értéke nem egy karakterláncot (null vagy bármely más adattípus tekinthető érvénytelen).     |

5. Miután a tömeges importálásához az alkalmazás készen áll, létrehozása a parancssori eszköz forrásból a 'tiszta csomag mvn' parancs használatával. A parancs létrehoz egy jar-fájlra a célmappában található:  

   ```java
   mvn clean package
   ```

6. Miután a cél függőségek jönnek létre, a tömeges importáló alkalmazás hívhat meg a következő paranccsal:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   A tömeges importáló hoz létre egy új adatbázist és egy gyűjtemény az adatbázis neve, gyűjtemény nevét, és átviteli értékeket az App.config fájlban megadott. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Tömeges frissítés adatok az Azure Cosmos-Adatbázisba

A BulkUpdateAsync API használatával frissítheti a meglévő dokumentumokat. Ebben a példában a név mező új értékre beállítva, és távolítsa el a meglévő dokumentumok a Leírás mezőben. A támogatott mező teljes készletének frissítési műveletek című [API-JÁNAK dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Határozza meg a frissítés elemeket találhat, valamint a megfelelő mező frissítési műveleteket. Ebben a példában a SetUpdateOperation használandó frissíti a név és Leírás mezőben eltávolítása a dokumentumok UnsetUpdateOperation. Műveleteket más növekmény például egy dokumentum mező által megadott értékkel, adott értékekre leküldéses egy tömb mezőbe, vagy egy adott érték eltávolítása egy tömb mező. A tömeges frissítés API által biztosított különböző módszerekkel kapcsolatos további tudnivalókért lásd: a [API-JÁNAK dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Hívja meg a updateAll API, amely véletlenszerű dokumentumokat kell majd tömeges importálja az Azure Cosmos DB gyűjteményt hoz létre. Beállíthatja, hogy a parancssori konfigurációk CmdLineConfiguration.java fájl átadni.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A tömeges frissítés API elfogadja a frissítendő elemek gyűjteménye. Minden egyes frissítés cikk felsorolja a mező frissítési műveleteket kell elvégezni egy dokumentum azonosítása Azonosítóval és a partíciókulcs-értékkel. További részletekért lásd: a [API-JÁNAK dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   A updateAll metódus a következő paraméterek fogadja el:

   |**A paraméter** |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  A maximális feldolgozási kulcs partíciótartomány / mértékét. Az alapértelmezett érték 20.  |
 
   **Válasz objektum importdefiníció tömeges** a tömeges importálással API-hívás eredménye a következő get metódust tartalmaz:

   |**A paraméter** |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   A tömeges frissítés API-hívás megadott kívül a dokumentumok sikeresen frissített dokumentumok teljes száma.      |
   |kettős getTotalRequestUnitsConsumed() |  A teljes kérelemegység (RU) a tömeges frissítés által használt API-hívás.       |
   |Időtartam getTotalTimeTaken()  |   A teljes ideje a tömeges frissítése API-hívás végrehajtása befejeződik.      |
   |Lista<Exception> getErrors()   |     A tömeges frissítés API hívása nem sikerült beolvasni beszúrni megadott kívül a köteg bizonyos dokumentumok, lekérdezi a hibák listája.      |

3. Miután a tömeges frissítése az alkalmazás készen áll, létrehozása a parancssori eszköz forrásból származó a 'tiszta csomag mvn' parancs használatával. A parancs létrehoz egy jar-fájlra a célmappában található:  

   ```
   mvn clean package
   ```

4. Miután a cél függőségek jönnek létre, hívhat meg a tömeges frissítés alkalmazása a következő paranccsal:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

Vegye figyelembe a következő szempontokat a jobb teljesítmény, BulkExecutor szalagtár használata esetén:

* A legjobb teljesítmény érdekében futtassa az alkalmazást egy Azure virtuális gépről a Cosmos DB írási régióját ugyanabban a régióban.  
* A nagyobb teljesítmény elérése érdekében:  

   * Állítsa be a JVM-et halommemória mérete elég sok nagy számú dokumentumok kezelése bármely memória probléma elkerülése érdekében. Javasolt halommemória mérete: maximális (3GB, 3 * "sizeof" operátor (tömeges átadott összes dokumentumot importálása API egy kötegben)).  
   * Nincs előfeldolgozási egyszerre, amelyek miatt elérhetővé válik nagyobb átviteli sebesség a dokumentumok sok tömeges műveletek végrehajtása során. Igen 10,000,000 dokumentumok importálni kívánt, ha futó tömeges importálással 10-szer dokumentumok 10 tömeges egyes mérete 1 000 000 esetén előnyösebb mint futó tömeges importálással 100 alkalommal dokumentumok 100 tömeges egyes mérete 100 000 dokumentumokat.  

* Javasoljuk, hogy a teljes alkalmazás egyetlen virtuális gépen, amely megfelel egy adott Azure Cosmos DB gyűjtemény esetében egyetlen DocumentBulkExecutor objektum hozható létre.  

* Mivel egy egyetlen tömeges API művelet végrehajtása egy nagy darabjának az ügyfélszámítógépen Processzor- és a hálózati IO igényel. Több feladat belsőleg származtatását szerint ez történik, elkerülheti a minden egyes végrehajtott tömeges művelet API-hívások alkalmazás folyamaton belül több egyidejű feladatok származtatását. Ha egyetlen virtuális gépen futó egyetlen tömeges művelet API hívása nem tudja használni a teljes gyűjteményt átviteli sebesség (Ha a gyűjtemény átviteli > 1 millió RU/mp), érdemes külön virtuális gépek tömeges egyidejűleg végrehajtásához létrehozása a művelet API-hívások száma.

    
## <a name="next-steps"></a>További lépések
* Tudnivalók a maven csomag részletei és a kibocsátási megjegyzésekben BulkExecutor Java-könyvtár:[BulkExecutor SDK részletek](sql-api-sdk-bulk-executor-java.md).


