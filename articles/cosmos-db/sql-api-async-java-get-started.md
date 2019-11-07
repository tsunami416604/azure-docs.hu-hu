---
title: 'Oktatóanyag: Java-alkalmazás létrehozása az aszinkron Java SDK-val egy SQL API-fiók kezeléséhez Azure Cosmos DB'
description: Ebből az oktatóanyagból megtudhatja, hogyan tárolhat és férhet hozzá az SQL API-fiókban található, Azure Cosmos DB egy aszinkron Java-alkalmazás használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 972328ec5052f5089994efbb940ff00c889ae78f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720874"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Oktatóanyag: Java-alkalmazás létrehozása az aszinkron Java SDK-val az SQL API-fiókokban tárolt adatkezelési szolgáltatásokhoz

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Fejlesztőként rendelkezhet olyan alkalmazásokkal, amelyek NoSQL használnak. A dokumentumok tárolására és elérésére használhatja a Azure Cosmos DBban található SQL API-fiókot. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Java-alkalmazást az aszinkron Java SDK-val a dokumentumok tárolására és kezelésére. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos-fiók létrehozása és csatlakoztatása
> * A megoldás konfigurálása
> * Gyűjtemény létrehozása
> * JSON-dokumentumok létrehozása
> * A gyűjtemény lekérdezése

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java fejlesztői készlet (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos-fiókot az alábbi lépések segítségével:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>A GitHub-adattár klónozása

A GitHub-tárház klónozásával [megkezdheti a Azure Cosmos db és a Java megkezdését](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Például egy helyi könyvtárból futtassa a következő parancsot a minta projekt helyi beolvasásához.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

A könyvtár tartalmaz egy `pom.xml` fájlt és egy `src/main/java/com/microsoft/azure/cosmosdb/sample` mappát, amely Java-forráskódot tartalmaz, beleértve a `Main.java`. A projekt olyan kódot tartalmaz, amely a Azure Cosmos DBokkal végzett műveletek végrehajtásához szükséges, például dokumentumok létrehozásához és adatok lekérdezéséhez egy gyűjteményen belül. A `pom.xml` fájl függőséget tartalmaz a [Azure Cosmos db Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)-ból a mavenen.

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Kapcsolódás Azure Cosmos-fiókhoz

Ezután lépjen vissza az [Azure Portalra](https://portal.azure.com) a végponti és az elsődleges főkulcs beszerzéséért. Az Azure Cosmos DB végpont és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban. Az `AccountSettings.java` fájl tárolja az elsődleges kulcs és az URI értékeit. 

A Azure Portal nyissa meg az Azure Cosmos-fiókját, majd kattintson a **kulcsok**elemre. Ezután másolja ki az URI és az ELSŐDLEGES KULCS értékét a portálról, és illessze be az `AccountSettings.java` fájlba. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Kulcsok lekérése a Portalról képernyőkép][keys]

## <a name="initialize-the-client-object"></a>Az ügyfél-objektum inicializálása

Inicializálja az ügyféloldali objektumot a "AccountSettings. Java" fájlban definiált gazdagép URI és Primary Key értékek használatával.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Adatbázis létrehozása

Hozza létre az Azure Cosmos-adatbázist a DocumentClient osztály `createDatabaseIfNotExists()` metódusának használatával. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

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

## <a id="CreateColl"></a>Gyűjtemény létrehozása

Gyűjtemény létrehozásához használja a DocumentClient osztály `createDocumentCollectionIfNotExists()` metódusát. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

> [!WARNING]
> A **createCollection** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

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

## <a id="CreateDoc"></a>JSON-dokumentumok létrehozása

Hozzon létre egy dokumentumot a DocumentClient osztály createDocument metódusának használatával. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. A "src/Main/Java/com/Microsoft/Azure/cosmosdb/Sample/familys. Java" fájl határozza meg a család JSON-dokumentumait. 

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

## <a id="Query"></a>Azure Cosmos DB erőforrások lekérdezése

Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. Az alábbi mintakód bemutatja, hogyan kérdezheti le a dokumentumokat a Azure Cosmos DB SQL-szintaxissal a `queryDocuments` metódussal.

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

## <a id="Run"></a>A Java-konzol alkalmazás futtatása

Az alkalmazás konzolról való futtatásához lépjen a Project mappára, és fordítsa le a Maven használatával:

```bash
mvn package
```

A `mvn package` futtatása letölti a legújabb Azure Cosmos DB-erőforrástárat a Mavenről, és létrehozza a `GetStarted-0.0.1-SNAPSHOT.jar` fájlt. Ezután futtassa az alkalmazást az alábbi paranccsal:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Ezzel elvégezte ezt a NoSQL-oktatóanyagot, és egy működőképes Java-konzollal rendelkezik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, az Azure Cosmos-fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Java-alkalmazást az aszinkron Java SDK-val az SQL API-beli adatkezelési Azure Cosmos DB. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Node.js-konzolalkalmazás létrehozása a JavaScript SDK-val és az Azure Cosmos DB-vel](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
