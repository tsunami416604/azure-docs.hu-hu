---
title: 'Azure Cosmos DB: Teendőkezelő alkalmazás fejlesztése Xamarin használatával | Microsoft Docs'
description: A cikk egy Xamarin-kódmintát mutat be, amellyel csatlakozhat egy Cosmos DB-adatbázishoz, és lekérdezéseket hajthat végre az adatbázisra vonatkozóan.
services: cosmos-db
author: codemillmatt
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/30/2018
ms.author: masoucou
ms.openlocfilehash: c48c5f116ef9c5716533ca28ccfd58b613facd26
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993329"
---
# <a name="azure-cosmos-db-build-a-todo-app-with-xamarin"></a>Azure Cosmos DB: Teendőkezelő alkalmazás fejlesztése Xamarin használatával

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

> [!NOTE]
> A GitHubon [itt](https://github.com/xamarinhq/app-geocontacts) megtalálható egy teljes Canonical Xamarin-mintaalkalmazás mintakódja, amely több Azure-ajánlatot is bemutat (például a CosmosDB-t). Ez az alkalmazás földrajzilag elosztott kapcsolattartók megtekintését mutatja be, és lehetővé teszi e kapcsolattartók számára, hogy frissítsék a tartózkodási helyüket.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB SQL API-fiókot, dokumentum-adatbázist és gyűjteményt. Ezután megtudhatja, hogyan hozhat létre és hogyan helyezhet üzembe egy, az [SQL .NET API](sql-api-sdk-dotnet.md)-n és a [Xamarinon](https://docs.microsoft.com/xamarin/#pivot=platforms&panel=Cross-Platform) alapuló teendőlista-kezelő webalkalmazást a [Xamarin.Forms](https://docs.microsoft.com/xamarin/#pivot=platforms&panel=XamarinForms) és az [MVVM-architektúraminta](https://docs.microsoft.com/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm) használatával.

![iOS rendszeren futó teendőkezelő Xamarin-alkalmazás](./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png)

## <a name="prerequisites"></a>Előfeltételek

Ha Windows rendszeren fejleszt, és nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure-fejlesztési** és a **mobilalkalmazások .NET rendszerrel való fejlesztése** számítási feladatot a Visual Studio telepítése során.

Mac gépek esetében letöltheti az **ingyenes** [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) alkalmazást.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozzuk a Xamarin SQL API-alkalmazást a GitHubról, áttekintjük a kódot, beszerezzük az API-kulcsokat, és futtatjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Ezután nyissa meg a ToDoItems.sln fájlt a Visual Studio samples/xamarin/ToDoItems mappájában.

## <a name="obtain-your-api-keys"></a>Az API-kulcsok beszerzése

Lépjen vissza az Azure Portalra az API-kulccsal kapcsolatos adatokért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB SQL API-fiókban a bal oldali navigációs sávon kattintson a **Kulcsok** elemre, majd kattintson az **Írható és olvasható kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot az APIKeys.cs fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-xamarin-dotnet/keys.png)

2. A Visual Studio 2017 vagy a Visual Studio for Mac alkalmazásban nyissa meg az azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Helpers mappában található APIKeys.cs fájlt.

3. Másolja az URI értékét a portálon (a másolási gomb használatával), és adja meg a `CosmosEndpointUrl` változó értékeként az APIKeys.cs fájlban.

    `public static readonly string CosmosEndpointUrl = "{Azure Cosmos DB account URL}";`

4. Ezután másolja az ELSŐDLEGES KULCS értékét a portálról, és adja meg a `Cosmos Auth Key` értékeként az APIKeys.cs fájlban.

    `public static readonly string CosmosAuthKey = "{Azure Cosmos DB secret}";`

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
* Az azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Models mappában található `ToDoItem` osztály a fent létrehozott **Elemek** gyűjtemény dokumentumait modellezi. Vegye figyelembe, hogy a tulajdonságok elnevezése különbséget tesz a kis- és nagybetűk között.
* Az azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Services mappában található `CosmosDBService` osztály foglalja magában az Azure Cosmos DB-vel folytatott kommunikációt.
* A `CosmosDBService` osztályban egy `DocumentClient` típusú változó található. A `DocumentClient` az Azure Cosmos DB-fiókra irányuló kérések konfigurálására és végrehajtására szolgál, és a 31. sorban van példányosítva:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Amikor dokumentumok gyűjteményét kérdezi le, a `DocumentClient.CreateDocumentQuery<T>` módszert használja, ahogyan az itt a `CosmosDBService.GetToDoItems` függvényben látható:

    ```csharp
    public async static Task<List<ToDoItem>> GetToDoItems()
    {
        var todos = new List<ToDoItem>();

        var todoQuery = docClient.CreateDocumentQuery<ToDoItem>(
                                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                                .Where(todo => todo.Completed == false)
                                .AsDocumentQuery();

        while (todoQuery.HasMoreResults)
        {
            var queryResults = await todoQuery.ExecuteNextAsync<ToDoItem>();

            todos.AddRange(queryResults);
        }

        return todos;
    }
    ```

    A `CreateDocumentQuery<T>` az előző szakaszban létrehozott gyűjteményre mutató URI-t vesz fel. Emellett LINQ-operátorokat is megadhat, például egy `Where` záradékot. Ebben az esetben a rendszer csak a nem elvégzett teendőket adja vissza.

    A `CreateDocumentQuery<T>` függvény végrehajtása szinkron módon történik, és egy `IQueryable<T>` objektumot ad vissza. Az `AsDocumentQuery` metódus azonban a `IQueryable<T>` objektumot `IDocumentQuery<T>` objektummá alakítja át, amely aszinkron módon hajtható végre. Így nem blokkolja a mobilalkalmazások felhasználói felületi szálját.

    Az `IDocumentQuery<T>.ExecuteNextAsync<T>` függvény lekéri az eredmények oldalát az Azure Cosmos DB-ből, amely a `HasMoreResults` használatával ellenőrzi, hogy vannak-e még visszaadandó eredmények.

> [!TIP]
> Az Azure Cosmos DB-gyűjteményekre és -dokumentumokra irányulóan működő számos függvény egy URI-t vesz fel paraméterként, amely megadja a gyűjtemény vagy dokumentum címét. Ezen URI létrehozása az `URIFactory` osztállyal történik. Az adatbázisok, gyűjtemények és dokumentumok URI-jai mind ezzel az osztállyal hozhatók létre.

* A 107. sorban található `ComsmosDBService.InsertToDoItem` függvény bemutatja, hogy hogyan szúrhat be új dokumentumot:

    ```csharp
    public async static Task InsertToDoItem(ToDoItem item)
    {
        ...
        await docClient.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), item);
        ...
    }
    ```

    A dokumentumgyűjtemény URI-ja és a beszúrandó elem is meg van adva.

* A 124. sorban található `CosmosDBService.UpdateToDoItem` függvény bemutatja, hogy hogyan cserélhet le egy meglévő dokumentumot egy újra:

    ```csharp
    public async static Task UpdateToDoItem(ToDoItem item)
    {
        ...
        var docUri = UriFactory.CreateDocumentUri(databaseName, collectionName, item.Id);

        await docClient.ReplaceDocumentAsync(docUri, item);
    }
    ```

    Itt új URI-ra van szükség a lecserélni kívánt dokumentum egyedi azonosításához. Az URI az `UriFactory.CreateDocumentUri` használatával szerezhető be, majd át kell adni a számára az adatbázis és a gyűjtemény nevét, valamint a dokumentum azonosítóját.

    A `DocumentClient.ReplaceDocumentAsync` a paraméterként meghatározott dokumentumra cseréli le az URI által azonosított dokumentumot.

* Az elemek törlését a 115. sorban található `CosmosDBService.DeleteToDoItem` függvény mutatja be:

    ```csharp
    public async static Task DeleteToDoItem(ToDoItem item)
    {
        ...
        var docUri = UriFactory.CreateDocumentUri(databaseName, collectionName, item.Id);

        await docClient.DeleteDocumentAsync(docUri);
    }
    ```

    Ismét felhívjuk figyelmét a létrehozott, és a `DocumentClient.DeleteDocumentAsync` függvénynek átadott egyedi dokumentum URI-ra.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

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

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan hozhat létre Azure Cosmos DB-fiókot, hogyan hozhat létre gyűjteményt az Adatkezelő segítségével, valamint hogyan hozhat lére és helyezhet üzembe Xamarin-alkalmazásokat. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
