---
title: "Azure Cosmos DB: Alkalmazás létrehozása Javával és DocumentDB API-val | Microsoft Docs"
description: "Egy Java-kódmintát mutat be, amellyel csatlakozhat az Azure Cosmos DB DocumentDB API-hoz és lekérdezheti azt"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 06/27/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c178646f0ec10cb08e90c1eda544a2488782187f
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="azure-cosmos-db-build-a-documentdb-api-app-with-java-and-the-azure-portal" class="xliff"></a>

# Azure Cosmos DB: DocumentDB API-alkalmazás létrehozása Javával és az Azure Portallal

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

A rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB-fiókot, dokumentum-adatbázist, és gyűjteményt. Ezután létrehozhatja és futtathatja a [DocumentDB Java API-ra](documentdb-sdk-java.md) épülő konzolalkalmazást.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

* Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
   * JDK 1.7+ (ha még nem rendelkezik a JDK-val, futtassa az `apt-get install default-jdk` parancsot)
   * Maven (ha nem rendelkezik Maven-nel, futtassa az `apt-get install maven` parancsot)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="add-a-collection" class="xliff"></a>

## Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="clone-the-sample-application" class="xliff"></a>

## A mintaalkalmazás klónozása

Most pedig klónozunk egy DocumentDB API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, és futtatni is fogjuk. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `CD` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a minta tárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

<a id="review-the-code" class="xliff"></a>

## A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `Program.java` fájlt. Az itt található kódsorok hozzák létre az Azure Cosmos DB-erőforrásokat. 

* A `DocumentClient` inicializálva van.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* A rendszer létrehozza az új adatbázist.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* A rendszer létrehozza az új gyűjteményt.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    // DocumentDB collections can be reserved with throughput
    // specified in request units/second. 1 RU is a normalized
    // request equivalent to the read of a 1KB document. Here we
    // create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* A rendszer létrehoz néhány dokumentumot.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* A egy SQL-lekérdezést hajt végre a JSON-on.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

<a id="update-your-connection-string" class="xliff"></a>

## A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **kulcsok** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot a `Program.java` fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-documentdb-dotnet/keys.png)

2. Nyissa meg a `Program.java` fájlt. 

3. Másolja ki az URI érteket a Portalról (a másolási gomb használatával), és adja meg a DocumentClient konstruktor végpontjának értekeként a `Program.java`-ban. 

    `"https://FILLME.documents.azure.com"`

4. Ezután másolja az ELSŐDLEGES KULCS értéket a Portalról és cserélje ki a „FILL ME” második paramétert a DocumentClient konstruktor kulcsával a Program.java fájlban. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 
    
<a id="run-the-app" class="xliff"></a>

## Az alkalmazás futtatása

1. Futtassa az `mvn package` parancsot egy terminálban a szükséges Java-csomagok telepítéséhez.

2. Futtassa a `mvn exec:java -D exec.mainClass=GetStarted.Program` parancsot egy terminálban a Java-alkalmazás elindításához.

Lépjen vissza az Adatkezelőbe, ahol lekérdezheti, módosíthatja, és dolgozhat az új adattal. 

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gyűjteményt készíteni, és hogyan lehet futtatni az alkalmazást. Most további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)



