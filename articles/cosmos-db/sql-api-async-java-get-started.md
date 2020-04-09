---
title: 'Oktatóanyag: Java-alkalmazás létrehozása az Azure Cosmos DB Async Java SDK-val az SQL API-fiókban lévő adatok kezeléséhez'
description: Ez az oktatóanyag bemutatja, hogyan tárolhatja és érheti el az adatokat egy SQL API-fiókon belül az Azure Cosmos DB-ben egy Async Java-alkalmazás használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: f81052f68d377603c7148ac2a39da626b426e352
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985235"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Oktatóanyag: Java-alkalmazás létrehozása az Async Java SDK-val az SQL API-fiókban tárolt adatok kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Fejlesztőként előfordulhat, hogy noSQL dokumentumadatokat használó alkalmazások at használ. Az Azure Cosmos DB SQL API-fiókjával tárolhatja és elérheti ezeket a dokumentumadatokat. Ez az oktatóanyag bemutatja, hogyan hozhat létre Java-alkalmazást az Async Java SDK-val a dokumentumadatok tárolásához és kezeléséhez. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos-fiók létrehozása és csatlakozás
> * A megoldás konfigurálása
> * Gyűjtemény létrehozása
> * JSON-dokumentumok létrehozása
> * A gyűjtemény lekérdezése

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java fejlesztői készlet (JDK) 8+](/java/azure/jdk/?view=azure-java-stable).

* [Maven, ez az.](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos-fiókot az alábbi lépésekkel:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>A GitHub-tárház klónozása

Klónozza a GitHub-tárházat az Azure Cosmos DB és a [Java első lépésekhez.](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) Például egy helyi könyvtárból futtassa a következőt a mintaprojekt helyi beolvasásához.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

A könyvtár `pom.xml` java forráskódot tartalmazó fájlt és `src/main/java/com/microsoft/azure/cosmosdb/sample` `Main.java`mappát tartalmaz, beleértve a . A projekt az Azure Cosmos DB-vel végzett műveletek végrehajtásához szükséges kódot tartalmaz, például dokumentumokat hoz létre és adatoklekérdezését egy gyűjteményen belül. A `pom.xml` fájl függőséget tartalmaz az [Azure Cosmos DB Java SDK-tól a Maven.The](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)file includes a dependency on the Azure Cosmos DB Java SDK on Maven.

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>Csatlakozás Azure Cosmos-fiókhoz

Ezután az [Azure Portalon](https://portal.azure.com) lekérheti a végpontot és az elsődleges főkulcsot. Az Azure Cosmos DB végpont és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban. Az `AccountSettings.java` fájl tárolja az elsődleges kulcs és az URI értékeit. 

Az Azure Portalon nyissa meg az Azure Cosmos-fiókot, és kattintson **a Kulcsok gombra.** Ezután másolja ki az URI és az ELSŐDLEGES KULCS értékét a portálról, és illessze be az `AccountSettings.java` fájlba. 

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

## <a name="initialize-the-client-object"></a>Az ügyfélobjektum inicializálása

Inicializálja az ügyfélobjektumot az "AccountSettings.java" fájlban definiált gazdaURI- és elsődleges kulcsértékek használatával.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>Adatbázis létrehozása

Hozza létre az Azure Cosmos-adatbázist a `createDatabaseIfNotExists()` DocumentClient osztály metódusával. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

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

## <a name="create-a-collection"></a><a id="CreateColl"></a>Gyűjtemény létrehozása

Gyűjteményt a `createDocumentCollectionIfNotExists()` DocumentClient osztály metódusával hozhat létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

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

## <a name="create-json-documents"></a><a id="CreateDoc"></a>JSON-dokumentumok létrehozása

Hozzon létre egy dokumentumot a DocumentClient osztály CreateDocument metódusával. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Az "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" fájl határozza meg a családi JSON-dokumentumokat. 

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

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>Azure Cosmos DB-erőforrások lekérdezése

Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. Az alábbi mintakód bemutatja, hogyan lehet lekérdezni a `queryDocuments` dokumentumokat az Azure Cosmos DB SQL szintaxisa a metódushasználatával.

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

## <a name="run-your-java-console-application"></a><a id="Run"></a>A Java konzolalkalmazás futtatása

Az alkalmazás konzolról történő futtatásához nyissa meg a projekt mappát, és a Maven használatával fordítsa le:

```bash
mvn package
```

A `mvn package` futtatása letölti a legújabb Azure Cosmos DB-erőforrástárat a Mavenről, és létrehozza a `GetStarted-0.0.1-SNAPSHOT.jar` fájlt. Ezután futtassa az alkalmazást az alábbi paranccsal:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Ezzel befejezte ezt a NoSQL oktatóanyagot, és rendelkezik egy működő Java konzolalkalmazással.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, az Azure Cosmos-fiókot és az összes kapcsolódó erőforrást. Ehhez jelölje ki a virtuális gép erőforráscsoportját, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Java-alkalmazást az Async Java SDK-val az SQL API-adatok kezeléséhez az Azure Cosmos DB-ben. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Node.js-konzolalkalmazás létrehozása a JavaScript SDK-val és az Azure Cosmos DB-vel](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
