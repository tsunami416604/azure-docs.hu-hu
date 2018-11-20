---
title: Java-alkalmazás létrehozása aszinkron Java SDK-val az Azure Cosmos DB SQL API-adatok kezeléséhez | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan tárolhatja és érheti el az adatokat egy aszinkron Java-alkalmazás használatával Azure Cosmos DB SQL API-fiókok segítségével.
keywords: nosql-oktatóanyag, online adatbázis, java konzolalkalmazás
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: 0c458892c56634c97eec92a9439059c087a32144
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165475"
---
# <a name="tutorial-build-a-java-app-with-async-java-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Oktatóanyag: Java-alkalmazás létrehozása aszinkron Java SDK-val az Azure Cosmos DB SQL API-adatok kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Java-alkalmazást aszinkron Java SDK-val az Azure Cosmos DB SQL API-adatok tárolásához és eléréséhez.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása és csatlakoztatása
> * A megoldás konfigurálása
> * Gyűjtemény létrehozása
> * JSON-dokumentumok létrehozása
> * A gyűjtemény lekérdezése

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java fejlesztői készlet (JDK) 8+](https://aka.ms/azure-jdks).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A GitHub-projekt klónozása](#GitClone) című lépéssel. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon [A GitHub-projekt klónozása](#GitClone) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>2. lépés: A GitHub-adattár klónozása

A GitHub-adattár klónozásával kezdheti meg [az Azure Cosmos DB és a Java használatának első lépéseit](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Futtassa például egy helyi könyvtárból az alábbi parancsot a mintaprojekt helyi lekéréséhez.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

A könyvtár tartalmaz egy `pom.xml` és egy `src/main/java/com/microsoft/azure/cosmosdb/sample` mappát, amelyek Java-forráskódot tartalmaznak, például a `Main.java` fájlt is. A projekt tartalmazza a kódot, amely olyan műveletek Azure Cosmos DB-vel történő végrehajtásához szükséges, mint például a dokumentumok létrehozása és az adatok lekérdezése egy gyűjteményen belül. A `pom.xml` fájl tartalmaz egy [Maven Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)-függőséget.

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>3. lépés: Csatlakozás egy Azure Cosmos DB-fiókhoz

Ezután lépjen vissza az [Azure Portalra](https://portal.azure.com) a végponti és az elsődleges főkulcs beszerzéséért. Az Azure Cosmos DB végpont és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban. Az `AccountSettings.java` fájl tárolja az elsődleges kulcs és az URI értékeit. 

Az Azure Portalon lépjen az Azure Cosmos DB-fiókra, majd kattintson a **Kulcsok** elemre. Ezután másolja ki az URI és az ELSŐDLEGES KULCS értékét a portálról, és illessze be az `AccountSettings.java` fájlba. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Kulcsok lekérése a Portalról képernyőkép][keys]

## <a name="step-4-initialize-the-client-object"></a>4. lépés: Az ügyfélobjektum inicializálása

Inicializálja az ügyfélobjektumot a gazdagép az „AccountSettings.java” fájlban meghatározott URI és elsődleges kulcs értékeivel.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>5. lépés: Adatbázis létrehozása

Az Azure Cosmos DB-[adatbázis](databases-containers-items.md#azure-cosmos-databases) a DocumentClient osztály createDatabaseIfNotExists() metódusának használatával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>6. lépés: Gyűjtemény létrehozása

> [!WARNING]
> A **createCollection** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
A gyűjtemények a DocumentClient osztály createDocumentCollectionIfNotExists() metódusának használatával hozhatók létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>7. lépés: JSON-dokumentumok létrehozása

A dokumentum a Documentclient módszer a DocumentClient osztály használatával hozható létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Az „src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java” fájl határozza meg a család JSON-dokumentumait. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>8. lépés: Az Azure Cosmos DB-erőforrások lekérdezése

Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](how-to-sql-query.md). Az alábbi mintakód bemutatja, hogyan kérdezheti le a dokumentumokat az Azure Cosmos DB-ben az SQL-szintaxis és a [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments) metódus együttes használatával.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>9. lépés: A Java-konzolalkalmazás futtatása

Az alkalmazás konzolról való futtatásához lépjen a projektmappába, és fordítsa le az alkalmazást Mavennel:

```bash
mvn package
```

A `mvn package` futtatása letölti a legújabb Azure Cosmos DB-erőforrástárat a Mavenről, és létrehozza a `GetStarted-0.0.1-SNAPSHOT.jar` fájlt. Ezután futtassa az alkalmazást az alábbi paranccsal:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Gratulálunk! Elvégezte a NoSQL-oktatóanyagot, és egy működőképes Java konzolalkalmazással rendelkezik!

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerhette, hogyan hozhat létre egy Java-alkalmazást aszinkron Java SDK-val az Azure Cosmos DB SQL API-adatok kezeléséhez. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Node.js-konzolalkalmazás létrehozása a JavaScript SDK-val és az Azure Cosmos DB-vel](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
