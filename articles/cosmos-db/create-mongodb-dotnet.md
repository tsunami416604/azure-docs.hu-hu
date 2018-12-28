---
title: -Webalkalmazás létrehozása Azure Cosmos DB API a mongodb-hez és a .NET SDK használatával
description: Használatával csatlakoznak és kérnek a mongodb-hez készült Azure Cosmos DB API használatával a .NET-kódmintát mutat be.
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: c6ae9fa46e9dd3abc1a526d5c7121c86d6e9a08c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791217"
---
# <a name="build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-net-sdk"></a>-Webalkalmazás létrehozása Azure Cosmos DB API a mongodb-hez és a .NET SDK használatával

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyors létrehozása és lekérdezése a dokumentum, kulcs/érték és gráf adatbázisokat, amelyek mindegyike globális elosztási és horizontális skálázhatósági képességeket Cosmos DB középpontjában. 

Ez a rövid útmutató azt ismerteti, hogyan hozzon létre egy Cosmos-fiókot [Azure Cosmos DB MongoDB API-](mongodb-introduction.md). Ezután hozhat létre és használatával létrehozott feladatok listája webalkalmazás üzembe helyezése a [MongoDB .NET Driver-re](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>A mintaalkalmazás futtatásának előfeltételei

A minta futtatásához szüksége [Visual Studio](https://www.visualstudio.com/downloads/) és a egy érvényes Azure Cosmos DB-fiókot.

Ha még nem rendelkezik a Visual Studio alkalmazással, töltse le a [Visual Studio 2017 Community Edition kiadását](https://www.visualstudio.com/downloads/) az **ASP.NET és webfejlesztési** számítási feladattal együtt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

A cikkben leírt minta kompatibilis a MongoDB.Driver 2.6.1-es verziójával.

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Először töltse le a mintaalkalmazást a Githubról. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Ha nem szeretné a gitet használni, [letöltheti a projektet ZIP-fájlként](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Az alábbi kódrészletek mind a DAL könyvtárban lévő Dal.cs fájlból származnak.

* Az ügyfél inicializálása.

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

Hozzon létre egy feladatot, és szúrja be a gyűjteménybe

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

1. Az a [az Azure portal](https://portal.azure.com/), a Cosmos-fiókban a bal oldalsávon kattintson **kapcsolati karakterlánc**, és kattintson a **írható és olvasható kulcsok**. A következő lépésben használja a képernyő jobb oldalán lévő másolási gombokat a felhasználónév, a jelszó és a gazdagép másolásához a Dal.cs fájlba.

2. Nyissa meg a **DAL** könyvtárban található **Dal.cs** fájlt. 

3. Másolja ki a **felhasználónév** érteket a Portalról (a másolási gomb használatával), és ezt adja meg a **felhasználónév** értékeként a **Dal.cs** fájlban. 

4. Ezután másolja ki a **gazdagép** értékét a Portalról, és adja meg a **gazdagép** értékeként a **Dal.cs** fájlban. 

5. Végezetül másolja ki a **jelszó** értékét a Portalról, és azt adja meg a **jelszó** értékeként a **Dal.cs** fájlban. 

Az alkalmazás frissítése megtörtént, és a Cosmos DB közötti kommunikációhoz szükséges összes információval. 
    
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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk hogyan hozzon létre egy Cosmos-fiókot, hozzon létre egy gyűjteményt, és futtathat egy konzolalkalmazást. További adatok már importálhat a Cosmos database. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
