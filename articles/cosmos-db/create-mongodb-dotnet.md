---
title: Webalkalmazás létrehozása a MongoDB és a .NET SDK-hoz készült Azure Cosmos DB API-val
description: Egy .NET-mintakód, amely a Azure Cosmos DB API-MongoDB való kapcsolódáshoz és lekérdezéshez használható.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.openlocfilehash: 55f40e8f22b39caa3527a378f33982276bed597d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755155"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Gyors útmutató: .NET-Webalkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérdezheti a dokumentum-, kulcs/érték-és Graph-adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Cosmos DB középpontjában. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre Cosmos-fiókot [Azure Cosmos db API-MongoDB](mongodb-introduction.md). Ezután létrehozhatja és üzembe helyezheti a [MongoDB .net-illesztőprogram](https://docs.mongodb.com/ecosystem/drivers/csharp/)használatával létrehozott Feladatlista-webalkalmazást.

## <a name="prerequisites-to-run-the-sample-app"></a>A mintaalkalmazás futtatásának előfeltételei

A minta futtatásához szüksége lesz a [Visual studióra](https://www.visualstudio.com/downloads/) és egy érvényes Azure Cosmos db-fiókra.

Ha még nem rendelkezik a Visual Studióval, töltse le a [Visual studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/) a telepítővel telepített **ASP.net és webes fejlesztési** számítási feladattal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

A cikkben leírt minta kompatibilis a MongoDB.Driver 2.6.1-es verziójával.

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Először töltse le a minta alkalmazást a GitHubról. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Ha nem szeretné a gitet használni, [letöltheti a projektet ZIP-fájlként](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Az alábbi kódrészletek mind a DAL könyvtárban lévő Dal.cs fájlból származnak.

* Inicializálja az ügyfelet.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Az adatbázis és a gyűjtemény lekérése.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Az összes dokumentum lekérése.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Feladat létrehozása és beillesztése a gyűjteménybe

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Ugyanígy a [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) és a [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) metódusokkal frissítheti és törölheti dokumentumait. 

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. A [Azure Portal](https://portal.azure.com/)a Cosmos-fiókban a bal oldali navigációs sávon kattintson a **kapcsolatok karakterlánca**elemre, majd kattintson az **írási/olvasási kulcsok**elemre. A következő lépésben használja a képernyő jobb oldalán lévő másolási gombokat a felhasználónév, a jelszó és a gazdagép másolásához a Dal.cs fájlba.

2. Nyissa meg a **DAL** könyvtárban található **Dal.cs** fájlt. 

3. Másolja ki a **felhasználónév** érteket a Portalról (a másolási gomb használatával), és ezt adja meg a **felhasználónév** értékeként a **Dal.cs** fájlban. 

4. Ezután másolja ki a **gazdagép** értékét a Portalról, és adja meg a **gazdagép** értékeként a **Dal.cs** fájlban. 

5. Végezetül másolja ki a **jelszó** értékét a Portalról, és azt adja meg a **jelszó** értékeként a **Dal.cs** fájlban. 

Ezzel frissítette az alkalmazást az Cosmos DBsal való kommunikációhoz szükséges összes információval. 
    
## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. A Visual Studióban kattintson a jobb gombbal a **Megoldáskezelő** projektre, majd kattintson a **NuGet-csomagok kezelése** parancsra. 

2. A NuGet **Tallózás** mezőjébe írja be a *MongoDB.Driver* szöveget.

3. Az eredmények közül telepítse a **MongoDB.Driver** könyvtárat. Ez telepíti a MongoDB.Driver-csomagot és az összes függőségeit.

4. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjelenik a böngészőben. 

5. Kattintson a **Létrehozás** lehetőségre a böngészőben, és hozzon létre néhány új tevékenységet a tevékenységek lista alkalmazásban.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Cosmos-fiókot, hogyan hozhat létre gyűjteményt, és hogyan futtathat egy konzolos alkalmazást. Mostantól további adatait is importálhatja a Cosmos-adatbázisba. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
