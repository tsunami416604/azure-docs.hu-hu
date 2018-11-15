---
title: 'Azure Cosmos DB: Xamarin.Forms-alkalmazás létrehozása .NET-tel és a MongoDB API-val | Microsoft Docs'
description: Egy Xamarin-kódminta bemutatása, amellyel csatlakoztatható és lekérdezhető az Azure Cosmos DB MongoDB API
services: cosmos-db
author: codemillmatt
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart, xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: 45d299e2d5e1f9c9c454e6b2f77df7cdf8295e5f
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568709"
---
# <a name="quickstart-build-a-mongodb-api-xamarinforms-app-with-net-and-the-azure-portal"></a>Rövid útmutató: MongoDB API Xamarin.Forms-alkalmazás létrehozása a .NET-tel az Azure Portalon

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre az Azure Portallal Azure Cosmos DB [MongoDB API](mongodb-introduction.md)-fiókot, dokumentum-adatbázist és gyűjteményt. Az ismeretek birtokában létre fog hozni egy teendőkezelő Xamarin.Forms-alkalmazást a [MongoDB .NET-illesztővel](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>A mintaalkalmazás futtatásának előfeltételei

A mintaalkalmazás futtatásához szüksége lesz a [Visual Studio](https://www.visualstudio.com/downloads/) vagy a [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) alkalmazásra és egy érvényes Azure CosmosDB-fiókra.

Ha még nem rendelkezik a Visual Studióval, töltse le a [Visual Studio 2017 Community Editiont](https://www.visualstudio.com/downloads/), amelynek a telepítésével a **mobilalkalmazások .NET rendszerrel való fejlesztésére** szolgáló számítási feladat is települ.

Ha Mac gépet használ, a [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) alkalmazást töltse le, és futtassa a telepítést.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

A cikkben leírt minta kompatibilis a MongoDB.Driver 2.6.1-es verziójával.

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Először töltse le a MongoDB API-mintaalkalmazást a GitHubról. Ez implementál egy teendőkezelő alkalmazást a MongoDB dokumentumtároló modelljével.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Ha nem szeretné a gitet használni, [a projektet ZIP-fájlként is letöltheti](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip).

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

Az alábbi kódrészletek a rendszer az összes átveszi a `MongoService` osztályhoz, a következő elérési úton található: src/TaskList.Core/Services/MongoService.cs.

* Inicializálja a Mongo Clientet.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Kérje le az adatbázisra és a gyűjteményre mutató hivatkozást. A MongoDB .NET SDK automatikusan létrehozza az adatbázist és a gyűjteményt is, ha még nem léteznek.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Kérje le az összes dokumentumot listaként.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Kérdezzen rá adott dokumentumokra.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Hozzon létre egy feladatot, és szúrja be a MongoDB-gyűjteménybe.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Frissítsen egy feladatot egy MongoDB-gyűjteményben.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Töröljön egy feladatot egy MongoDB-gyűjteményből.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **Kapcsolati sztring** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépések során a képernyő jobb oldalán található másolási gombok használatával másolhatja a vágólapra az elsődleges kapcsolati sztringet.

2. Nyissa meg a **TaskList.Core** projekt **Helpers** könyvtárában lévő **APIKeys.cs** fájlt.

3. Másolja ki az **elsődleges kapcsolati sztring** értekét a portálról (a másolási gombbal), és ezt adja meg a **ConnectionString** mező értékeként az **APIKeys.cs** fájlban.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

## <a name="run-the-app"></a>Az alkalmazás futtatása

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. A Visual Studióban kattintson a jobb gombbal az egyes projektekre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre.
2. Kattintson **az összes NuGet-csomagot visszaállító** lehetőségre.
3. Kattintson a jobb gombbal a **TaskList.Android** elemre, és válassza a **Beállítás kezdőprojektként** lehetőséget.
4. Az alkalmazás hibakereséséhez nyomja le az F5 billentyűt.
5. Ha iOS-rendszeren szeretné futtatni, a készüléket először csatlakoztatnia kell egy Machez (ehhez itt talál [útmutatást](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)).
6. Kattintson a jobb gombbal a **TaskList.iOS** elemre, és válassza a **Beállítás kezdőprojektként** lehetőséget.
7. Az alkalmazás hibakereséséhez nyomja le az F5 billentyűt.

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. A platform legördülő listájából válassza a TaskList.iOS vagy a TaskList.Android elemet attól függően, hogy melyik platformot szeretné használni.
2. Az alkalmazás hibakereséséhez nyomja le a cmd+Enter billentyűkombinációt.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, és Xamarin.Forms-alkalmazást futtatni a MongoDB API-jával. Így már további adatokat importálhat a Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be a MongoDB API-hoz](mongodb-migrate.md)