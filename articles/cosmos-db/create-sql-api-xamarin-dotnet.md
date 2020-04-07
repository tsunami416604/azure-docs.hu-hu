---
title: 'Azure Cosmos DB: Teendőkezelő alkalmazás fejlesztése Xamarin használatával'
description: A cikk egy Xamarin-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre az adatbázisra vonatkozóan.
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/30/2018
ms.author: masoucou
ms.openlocfilehash: 125cc2516fbd694bbe62545e85d4e14225fb2a6a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756429"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: Todo-alkalmazás létrehozása a Xamarinnal az Azure Cosmos DB SQL API-fiókhasználatával

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

> [!NOTE]
> A GitHubon [itt](https://github.com/xamarinhq/app-geocontacts) megtalálható egy teljes Canonical Xamarin-mintaalkalmazás mintakódja, amely több Azure-ajánlatot is bemutat (például a CosmosDB-t). Ez az alkalmazás földrajzilag elosztott kapcsolattartók megtekintését mutatja be, és lehetővé teszi e kapcsolattartók számára, hogy frissítsék a tartózkodási helyüket.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot, dokumentum-adatbázist és tárolót az Azure Portal segítségével. Ezután létrehoz és telepít egy [sql .NET API-ra](sql-api-sdk-dotnet.md) és [xamarinra](https://docs.microsoft.com/xamarin/) épülő mobilalkalmazást, amely a [Xamarin.Forms](https://docs.microsoft.com/xamarin/) és az [MVVM architekturális mintáját](https://docs.microsoft.com/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)tartalmazza.

![iOS rendszeren futó teendőkezelő Xamarin-alkalmazás](./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png)

## <a name="prerequisites"></a>Előfeltételek

Ha Windows rendszeren fejleszt, és még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure-fejlesztési** és a **mobilalkalmazások .NET rendszerrel való fejlesztése** számítási feladatot a Visual Studio telepítése során.

Mac gépek esetében letöltheti az **ingyenes** [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) alkalmazást.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most klónozzuk a Xamarin SQL API-alkalmazást a GitHubról, tekintse át a kódot, szerezze be az API-kulcsokat, és futtassa azt. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Ezután nyissa meg a ToDoItems.sln fájlt a Visual Studio samples/xamarin/ToDoItems mappájában.

## <a name="obtain-your-api-keys"></a>Az API-kulcsok beszerzése

Lépjen vissza az Azure Portalra az API-kulccsal kapcsolatos adatokért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) az Azure Cosmos DB SQL API-fiókban a bal oldali navigációs sávon kattintson a **Kulcsok** elemre, majd kattintson az **Írható és olvasható kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot az APIKeys.cs fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén](./media/create-sql-api-xamarin-dotnet/keys.png)

2. A Visual Studio 2019-ben vagy a Visual Studio for Mac-ben nyissa meg a APIKeys.cs fájlt az azure-cosmos-db-sql-xamarin-getting-started/src/ToDoItems.Core/Helpers mappában.

3. Másolja az URI értékét a portálon (a másolási gomb használatával), és adja meg a `CosmosEndpointUrl` változó értékeként az APIKeys.cs fájlban.

    `public static readonly string CosmosEndpointUrl = "";`

4. Ezután másolja az ELSŐDLEGES KULCS értékét a portálról, és adja meg a `Cosmos Auth Key` értékeként az APIKeys.cs fájlban.

    `public static readonly string CosmosAuthKey = "";`

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a megoldás bemutatja, hogy hogyan hozhat létre egy teendőkezelő alkalmazást az Azure Cosmos DB SQL API és a Xamarin.Forms segítségével. Az alkalmazás két lappal rendelkezik, az első lap a még el nem végzett teendőket megjelenítő listanézetet tartalmazza. A második lap a már elvégzett teendőket jeleníti meg. Az első lapon található, még el nem végzett teendők megtekintése mellett új teendőket is hozzáadhat, szerkesztheti a meglévőket, és befejezettként jelölheti meg a lista tételeit.

![Másolja át a json-adatokat, és kattintson a Mentés gombra az Adatkezelőben az Azure-portálon](./media/create-sql-api-xamarin-dotnet/android-todo-screen.png)

A ToDoItems megoldásban található kód az alábbiakat tartalmazza:

* ToDoItems.Core: Ez egy Xamarin.Forms-projektet és az Azure Cosmos DB-ben a teendőket kezelő, megosztott alkalmazáslogikai kódot tartalmazó .NET Standard-projekt.
* ToDoItems.Android: Ez a projekt az Android-alkalmazást tartalmazza.
* ToDoItems.iOS: Ez a projekt az iOS-alkalmazást tartalmazza.

Most tekintsük át röviden, hogyan kommunikál az alkalmazás az Azure Cosmos DB-vel.

* A [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) NuGet-csomagot minden projekthez hozzá kell adni.
* Az `ToDoItem` azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Models mappában lévő osztály a fenti **Elemek** tárolóban lévő dokumentumokat modellezi. Vegye figyelembe, hogy a tulajdonságok elnevezése különbséget tesz a kis- és nagybetűk között.
* Az azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Services mappában található `CosmosDBService` osztály foglalja magában az Azure Cosmos DB-vel folytatott kommunikációt.
* A `CosmosDBService` osztályban egy `DocumentClient` típusú változó található. Az `DocumentClient` azure Cosmos DB-fiókkal kapcsolatos kérelmek konfigurálására és végrehajtására szolgál, és példányosodik:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Dokumentumok tárolójának lekérdezésekénél a rendszer a `DocumentClient.CreateDocumentQuery<T>` módszert `CosmosDBService.GetToDoItems` használja, ahogy az itt látható a függvényben:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    A `CreateDocumentQuery<T>` vesz egy URI, amely az előző szakaszban létrehozott tárolóra mutat. Emellett LINQ-operátorokat is megadhat, például egy `Where` záradékot. Ebben az esetben a rendszer csak a nem elvégzett teendőket adja vissza.

    A `CreateDocumentQuery<T>` függvény végrehajtása szinkron módon történik, és egy `IQueryable<T>` objektumot ad vissza. Az `AsDocumentQuery` metódus azonban a `IQueryable<T>` objektumot `IDocumentQuery<T>` objektummá alakítja át, amely aszinkron módon hajtható végre. Így nem blokkolja a mobilalkalmazások felhasználói felületi szálját.

    Az `IDocumentQuery<T>.ExecuteNextAsync<T>` függvény lekéri az eredmények oldalát az Azure Cosmos DB-ből, amely a `HasMoreResults` használatával ellenőrzi, hogy vannak-e még visszaadandó eredmények.

> [!TIP]
> Az Azure Cosmos-tárolókon és dokumentumokon működő számos függvény egy URI-t vesz fel paraméterként, amely megadja a tároló vagy a dokumentum címét. Ezen URI létrehozása az `URIFactory` osztállyal történik. Az adatbázisokhoz, tárolókhoz és dokumentumokhoz használható URI-k mind létrehozhatók ezzel az osztállyal.

* A `ComsmosDBService.InsertToDoItem` funkció bemutatja, hogyan kell beszúrni egy új dokumentumot:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Meg van adva az elem URI-ja, valamint a beszúrandó elem.

* A `CosmosDBService.UpdateToDoItem` funkció bemutatja, hogyan lehet egy meglévő dokumentumot újra cserélni:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Itt egy új URI-ra van szükség a lecserélni `UriFactory.CreateDocumentUri` szükséges dokumentum egyedi azonosításához, és az adatbázis- és tárolónevek, valamint a dokumentum azonosítójának használatával és továbbításával kapjuk meg.

    A `DocumentClient.ReplaceDocumentAsync` a paraméterként meghatározott dokumentumra cseréli le az URI által azonosított dokumentumot.

* Az elem törlése a `CosmosDBService.DeleteToDoItem` következő funkcióval történik:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Ismét vegye figyelembe az egyedi dokumentum `DocumentClient.DeleteDocumentAsync` URI jön létre, és átadta a függvénynek.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

A következő lépések mutatják be, hogyan futtathatja az alkalmazást a Visual Studio for Mac hibakeresőjével.

> [!NOTE]
> Az Android verziójú alkalmazás használata pontosan ugyanilyen, az alábbi lépésekben kiemelünk minden különbséget. Ha Windows rendszeren szeretne hibakeresést végezni a Visual Studióval, ennek dokumentációját [itt találja az iOS](https://docs.microsoft.com/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin), illetve [itt találja az Android rendszerre](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/) vonatkozóan.

1. Először válassza ki a kívánt platformot. Ehhez kattintson a kiemelt legördülő listára, és válassza a ToDoItems.iOS elemet az iOS, vagy a ToDoItems.Android elemet az Android esetén.

    ![A hibakeresés céljaként használni kívánt platform kiválasztása a Visual Studio for Mac alkalmazásban](./media/create-sql-api-xamarin-dotnet/ide-select-platform.png)

2. Az alkalmazás hibakeresésének elkezdéséhez nyomja le a Command+Enter billentyűkombinációt vagy kattintson a Lejátszás gombra.

    ![Hibakeresés elkezdése a Visual Studio for Mac alkalmazásban](./media/create-sql-api-xamarin-dotnet/ide-start-debug.png)

3. Az iOS-szimulátor vagy az Android-emulátor elindítása után az alkalmazás 2 lapot jelenít meg a képernyő alján az iOS, illetve a képernyő tetején az Android esetén. Az első lapon az el nem végzett teendők láthatók, a másodikon pedig a befejezettek.

    ![A teendőkezelő alkalmazás indítóképernyője](./media/create-sql-api-xamarin-dotnet/ios-droid-started.png)

4. Az iOS rendszeren egy teendő elvégzéséhez csúsztassa a teendőt balra > koppintson a **Complete** (Befejezés) gombra. Az Android rendszeren egy teendő elvégzéséhez nyomja le hosszan a teendőt > majd koppintson a Complete (Befejezés) gombra.

    ![Teendő elvégzése](./media/create-sql-api-xamarin-dotnet/simulator-complete.png)

5. Teendő szerkesztéséhez > koppintson az elemre > megjelenik egy új képernyő, ahol új értékeket adhat meg. A Save (Mentés) gombra koppintva menti a módosításokat az Azure Cosmos DB-ben.

    ![Teendő szerkesztése](./media/create-sql-api-xamarin-dotnet/simulator-edit.png)

6. Teendő hozzáadásához > koppintson az **Add** (Hozzáadás) gombra a kezdőképernyő jobb felső sarkában > megjelenik egy új, üres szerkesztési lap.

    ![Teendő hozzáadása](./media/create-sql-api-xamarin-dotnet/simulator-add.png)

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre egy tárolót az Adatkezelővel, és hogyan hozhat létre és helyezhet üzembe egy Xamarin-alkalmazást. Most már importálhat további adatokat az Azure Cosmos-fiókjába.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
