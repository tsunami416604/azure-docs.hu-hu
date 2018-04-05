---
title: 'Azure Cosmos DB: Konzolalkalmazás létrehozása Javával és MongoDB API-val | Microsoft Docs'
description: Egy Java-kódmintátmutat be, amellyel csatlakozhat az Azure Cosmos DB MongoDB API-hoz és lekérdezheti azt
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 9716bc9a4728a48732cacef7c2568a640ba39ee6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: MongoDB API konzolalkalmazás létrehozása Javával és az Azure Portallal

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB [MongoDB API](mongodb-introduction.md)-fiókot, dokumentum-adatbázist és gyűjteményt. Ezután létrehozhatja és üzembe helyezheti a [MongoDB Java driver-re](https://docs.mongodb.com/ecosystem/drivers/java/) épülő konzolalkalmazást. 

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

Klónozzunk egy MongoDB API-alkalmazást a GitHubról, állítsuk be a kapcsolati karakterláncot, és futtassuk. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a minta tárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Ezután nyissa meg a kódot egy tetszőleges szerkesztőben. 

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `Program.java` fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB erőforrásokat. 

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

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. A Fiók területen válassza ki az **Első lépések**, majd a Java lehetőséget, és másolja a kapcsolati karakterláncot a vágólapra

2. Nyissa meg a `Program.java` fájlt, cserélje le a MongoClientURI konstruktor argumentumát a kapcsolati karakterlánccal. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 
    
## <a name="run-the-console-app"></a>A konzolalkalmazás futtatása

1. Futtassa a `mvn package` parancsot egy terminálban a szükséges npm-modulok telepítéséhez

2. Futtassa a `mvn exec:java -D exec.mainClass=GetStarted.Program` parancsot egy terminálban a Java-alkalmazás elindításához.

Ezentúl a [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) használatával lekérdezheti, módosíthatja, és dolgozhat az új adatokkal.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan hozhat létre Azure Cosmos DB-fiókot, hogyan hozhat létre gyűjteményt az Adatkezelő segítségével, és hogyan futtathat konzolalkalmazást. Így már további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)


