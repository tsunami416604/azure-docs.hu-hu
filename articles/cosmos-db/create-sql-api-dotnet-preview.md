---
title: 'Gyors útmutató: Egy ASP.NET-webalkalmazás létrehozása az Azure Cosmos DB SQL API-t és az Azure portal használatával'
description: Ebben a rövid útmutatóban az Azure Cosmos DB SQL API és az Azure Portalon hoz létre használatával ASP.NET-webalkalmazás
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 1589c938e635ccff02f94a176b7fc886dbccb779
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455768"
---
# <a name="quickstart-build-an-aspnet-web-app-using-azure-cosmos-db-sql-api-account"></a>Gyors útmutató: Egy ASP.NET-webalkalmazás létrehozása az Azure Cosmos DB SQL API-fiók használatával

> [!div class="op_single_selector"]
> * [.NET (előzetes verzió)](create-sql-api-dotnet-preview.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Az Azure Cosmos DB a Microsoft globálisan elosztott, magas rendelkezésre állású, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Cosmos DB [SQL API](sql-api-introduction.md) fiókot, adatbázist és a tároló az Azure portal használatával. Ezután hozhat létre és -épülő feladatlista az ASP.NET-WebApp üzembe helyezése a [SQL .NET API](sql-api-sdk-dotnet.md), az alábbi képernyőképen látható módon. 

Ebben a rövid útmutatóban az Azure Cosmos DB .NET SDK 3.0-s + verzióját használja. 

![Teendőkezelő alkalmazás mintaadatokkal](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list-preview.png)

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Győződjön meg arról, hogy engedélyezze a **Azure-fejlesztési** számítási feladatot a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

<a id="create-collection"></a>
## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection-preview](../../includes/cosmos-db-create-collection-preview.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. Klónozunk egy [SQL API-alkalmazást a GitHubról](https://github.com/Azure-Samples/cosmos-dotnet-todo-app), beállítjuk a kapcsolati karakterláncot, és futtatjuk az alkalmazást. 

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
    git clone https://github.com/Azure-Samples/cosmos-dotnet-todo-app.git
    ```

4. Nyissa meg a **todo.sln** megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

Vegye figyelembe, ha ismeri a .NET SDK korábbi verziójával, akkor is használható jelennek meg a feltételek "gyűjtemény" és "dokumentum." Azure Cosmos DB támogatja a több API-modell, mert a .NET SDK 3.0-s + verzióját használja-e az általános feltételek "container" és "cikk". A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. [Ismerje meg, további információk adatbázisok, tárolók és elemek.](databases-containers-items.md)

Az alábbi kódrészletek az ToDoItemService.cs fájlból kerül.

* A CosmosClient inicializálva van a sorok 68-69.

    ```csharp
    CosmosConfiguration config = new CosmosConfiguration(Endpoint, PrimaryKey);
    client = new CosmosClient(config);
    ```

* Sor 71 létrejön egy új adatbázist.

    ```csharp
    CosmosDatabase database = await client.Databases.CreateDatabaseIfNotExistsAsync(DatabaseId);
    ```

* Sor 72 partíciókulccsal rendelkező jön létre egy új tárolót "/ kategória."

    ```csharp
    CosmosContainer container = await database.Containers.CreateContainerIfNotExistsAsync(ContainerId, "/category");
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](https://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon válassza a **Kulcsok** elemet, majd válassza az **Írási/olvasási kulcsok** lehetőséget. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot a web.config fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. A Visual Studio 2017, nyissa meg a **web.config** fájlt. 

3. Másolja az URI érteket a Portalról (a másolási gomb használatával), és adja meg az értékét a ``endpoint`` kulcs értékeként a web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Ezután másolja ki az elsődleges kulcs értékét a Portalról, és győződjön meg arról, hogy értékét a ``primarykey`` a Web.config fájlban. 

    `<add key="primaryKey" value="FILLME" />`
    
5. Frissítse az adatbázist és a tároló érték az adatbázis és a korábban létrehozott tároló neve megfelelően. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `<add key="database" value="Tasks" />`

    `<add key="container" value="Items" />`
    
## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Az a **Megoldáskezelőben**, kattintson az új Konzolalkalmazás-projektet, amely a Visual Studio megoldás alatt található, a jobb gombbal, és kattintson a **NuGet-csomagok kezelése...**
    
    ![A projekt jobb gombos kattintással elérhető menüjének képernyőképe](./media/create-sql-api-dotnet/manage-nuget-package.png)
1. Az a **NuGet** lapra, majd **Tallózás**, és írja be **Microsoft.Azure.Cosmos** kifejezést a keresőmezőbe.
1. A találatok között keresse meg a **Microsoft.Azure.Cosmos** kattintson **telepítése**.
   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

   ![Képernyőfelvétel a NuGet menüről az Azure Cosmos DB ügyféloldali SDK megkereséséhez menü](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

1. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjelenik a böngészőben. 

1. Válassza az **Új létrehozása** lehetőséget a böngészőben, és hozzon létre néhány új feladatot a teendőkezelő alkalmazásban. Is meg kell jelennie a létrehozott feladatok [mintaadatok felvétele](#add-sample-data)

   ![Teendőkezelő alkalmazás mintaadatokkal](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list-preview.png)

Is most visszaléphet az Adatkezelőbe, és tekintse meg, lekérdezése, módosítása és az új adatokkal dolgozni. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk az Azure Cosmos DB-fiók létrehozása, hozzon létre egy tárolót, és elemek hozzáadása az adatkezelő használatával, és webalkalmazás futtatása. Így már további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)


