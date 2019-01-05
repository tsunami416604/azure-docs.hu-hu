---
title: Azure Cosmos DB API használatával a MongoDB-hez és a Java SDK Konzolalkalmazás létrehozása
description: Használatával csatlakoznak és kérnek az Azure Cosmos DB API használatával a mongodb-hez a Java-kódmintát mutat be.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2017
ms.author: rimman
ms.openlocfilehash: 36e07fa7d0dadd93f44e1e1ebd13d171fbd6e2a9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033740"
---
# <a name="build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>Azure Cosmos DB API használatával a MongoDB-hez és a Java SDK webes alkalmazás készítése

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyors létrehozása és lekérdezése a dokumentum, kulcs/érték és gráf adatbázisokat, amelyek mindegyike globális elosztási és horizontális skálázhatósági képességeket Cosmos DB középpontjában. 

Ez a rövid útmutató azt ismerteti, hogyan hozzon létre egy Cosmos-fiókot [Azure Cosmos DB MongoDB API-](mongodb-introduction.md). Ezután hozhat létre és üzembe helyezése egy konzolalkalmazás használatával jönnek létre a [MongoDB Java Driver-re](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
* JDK 1.7+ (ha még nem rendelkezik a JDK-val, futtassa az `apt-get install default-jdk` parancsot)
* Maven (ha nem rendelkezik Maven-nel, futtassa az `apt-get install maven` parancsot)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

Az új adatbázis neve legyen **db**, az új gyűjteményé pedig **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most tekintsük klónozza az alkalmazást a Githubról, beállítjuk a kapcsolati karakterláncot, és futtassa azt. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Ezután nyissa meg a kódot egy tetszőleges szerkesztőben. 

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Az alábbi kódrészletek mind a Program.java fájlból származnak.

* A DocumentClient inicializálva van.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* A rendszer létrehozza az új adatbázist és a gyűjteményt.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* A rendszer beilleszt néhány dokumentumot a `MongoCollection.insertOne` használatával

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* A rendszer végrehajt néhány lekérdezést a `MongoCollection.find` használatával

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. A Fiók területen válassza ki az **Első lépések**, majd a Java lehetőséget, és másolja a kapcsolati sztringet a vágólapra

2. Nyissa meg a `Program.java` fájlt, cserélje le a MongoClientURI konstruktor argumentumát a kapcsolati sztringgel. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 
    
## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. Futtassa a `mvn package` parancsot egy terminálban a szükséges npm-modulok telepítéséhez

2. Futtassa a `mvn exec:java -D exec.mainClass=GetStarted.Program` parancsot egy terminálban a Java-alkalmazás elindításához.

Ezentúl a [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) használatával lekérdezheti, módosíthatja, és dolgozhat az új adatokkal.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk hogyan hozzon létre egy Cosmos-fiókot, hozzon létre egy gyűjteményt, és futtathat egy konzolalkalmazást. További adatok már importálhat a Cosmos database.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
