---
title: 'Gyors útmutató: Webalkalmazás létrehozása a Mongo DB-hez és a Java SDK-hoz készült Azure Cosmos DB API-val'
description: Megtudhatja, hogyan hozhat létre egy Java-kódrészletet, amellyel csatlakozhat a Azure Cosmos DB API-MongoDB való kapcsolódáshoz és lekérdezéshez.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 05a796e5bf197bf9ea4f8f47adfbf30851b300ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445512"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Gyors útmutató: konzolos alkalmazás létrehozása Javával és a MongoDB API-val Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Ebben a rövid útmutatóban a Mongo DB és a Java SDK Azure Cosmos DB API-ját fogja használni egy konzol-webalkalmazás létrehozásához. Azure Cosmos DB lehetővé teszi a dokumentum-, kulcs/érték-és Graph-adatbázisok gyors létrehozását és lekérdezését, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Cosmos DB középpontjában.

Ez a rövid útmutató bemutatja, hogyan hozhat létre Cosmos-fiókot [Azure Cosmos db API-MongoDB](mongodb-introduction.md). Ezután létrehozhatja és üzembe helyezheti a [MongoDB Java-illesztőprogram](https://docs.mongodb.com/ecosystem/drivers/java/)használatával létrehozott konzolos alkalmazást. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
* [Az Azure-hoz készült JDK telepítése és a JDK 8-as verziójának Azure Stack](https://aka.ms/azure-jdks)
* Maven (ha nem rendelkezik Maven-nel, futtassa az `apt-get install maven` parancsot)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

Az új adatbázis neve legyen **db**, az új gyűjteményé pedig **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Ezután nyissa meg a kódot egy tetszőleges szerkesztőben. 

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Az alábbi kódrészletek mind a Program.java fájlból származnak.

* A rendszer inicializálja a DocumentClient ügyfelet.

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

1. A fiókban válassza a **gyorskonfigurálás**lehetőséget, válassza a **Java**lehetőséget, majd másolja a kapcsolódási karakterláncot a vágólapra.

2. Nyissa meg a `Program.java` fájlt, cserélje le a MongoClientURI konstruktor argumentumát a kapcsolati sztringgel. Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 
    
## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. Futtassa a `mvn package` parancsot egy terminálban a szükséges npm-modulok telepítéséhez

2. Futtassa a `mvn exec:java -D exec.mainClass=GetStarted.Program` parancsot egy terminálban a Java-alkalmazás elindításához.

Ezentúl a [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) használatával lekérdezheti, módosíthatja, és dolgozhat az új adatokkal.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Cosmos-fiókot, hogyan hozhat létre gyűjteményt, és hogyan futtathat egy konzolos alkalmazást. Mostantól további adatait is importálhatja a Cosmos-adatbázisba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
