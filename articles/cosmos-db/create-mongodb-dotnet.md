---
title: "Azure Cosmos DB: Webalkalmazás létrehozása .NET-tel és MongoDB API-val | Microsoft Docs"
description: "Egy .NET-es kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB MongoDB API-hoz, és lekérdezést lehet végezni vele"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: c92d970783ae0fb36e5761e4f35af7d4d6718121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-mongodb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB: MongoDB API Webalkalmazás létrehozása .NET-tel és az Azure Portallal

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

A rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB-fiókot, dokumentum-adatbázist, és gyűjteményt. Ezután létrehozhatja és üzembe helyezheti a [MongoDB .NET driver-re](https://docs.mongodb.com/ecosystem/drivers/csharp/) épülő feladatlista webalkalmazást. 

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Klónozzunk egy MongoDB API-alkalmazást a GitHubról, állítsuk be a kapcsolati karakterláncot, és futtassuk. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a **Dal.cs** fájlt a **DAL** könyvtárból: az itt található kódsorok hozzák létre az Azure Cosmos DB erőforrásokat. 

* A Mongo Client inicializálása.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credentials = new List<MongoCredential>()
        {
            new MongoCredential("SCRAM-SHA-1", identity, evidence)
        };

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

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **Kapcsolati karakterlánc** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépésben használja a képernyő jobb oldalán lévő másolási gombokat a felhasználónév, a jelszó és a gazdagép másolásához a Dal.cs fájlba.

2. Nyissa meg a **DAL** könyvtárban található **Dal.cs** fájlt. 

3. Másolja ki a **felhasználónév** érteket a Portalról (a másolási gomb használatával), és ezt adja meg a **felhasználónév** értékeként a **Dal.cs** fájlban. 

4. Ezután másolja ki a **gazdagép** értékét a Portalról, és adja meg a **gazdagép** értékeként a **Dal.cs** fájlban. 

5. Végezetül másolja ki a **jelszó** értékét a Portalról, és azt adja meg a **jelszó** értékeként a **Dal.cs** fájlban. 

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 
    
## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. A Visual Studióban kattintson a jobb gombbal a **Megoldáskezelő** projektre, majd kattintson a **NuGet-csomagok kezelése** parancsra. 

2. A NuGet **Tallózás** mezőjébe írja be a *MongoDB.Driver* szöveget.

3. Az eredmények közül telepítse a **MongoDB.Driver** könyvtárat. Ez telepíti a MongoDB.Driver-csomagot és az összes függőségeit.

4. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjelenik a böngészőben. 

5. Kattintson a **Létrehozás** lehetőségre a böngészőben, és hozzon létre néhány új tevékenységet a tevékenységek lista alkalmazásban.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, és a MongoDB-hez tartozó API használatával webalkalmazást futtatni. Most további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be a MongoDB API-hoz](mongodb-migrate.md)

