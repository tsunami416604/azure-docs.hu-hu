---
title: "Azure Cosmos DB .NET-alkalmazás létrehozása a Table API-val | Microsoft Docs"
description: "Bevezetés az Azure Cosmos DB Table API-jának .NET-alapú használatába"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: cba0b278d84e25876a8b73cedb7e35f84500fc5e
ms.contentlocale: hu-hu
ms.lasthandoff: 05/11/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: .NET-alkalmazás létrehozása a Table API-val

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével egy Azure Cosmos DB-fiókot, majd abban egy táblát. Ezután megírhatja a kódot az entitások beillesztéséhez, frissítéséhez és törléséhez, és futtathat néhány lekérdezést. Az [Azure Storage SDK-jának előzetes verzióját](https://aka.ms/premiumtablenuget) a NuGetről töltheti le. Az előzetes verzió osztályai és metódusainak aláírásai megegyeznek a nyilvános [Azure Storage SDK-ban](https://www.nuget.org/packages/WindowsAzure.Storage) elérhetőkkel, de ez a verzió ezenfelül képes Azure Cosmos DB-fiókokhoz is kapcsolódni a [Table API](table-introduction.md) előzetes verziójának használatával. 

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

Az Adatkezelő segítségével adatokat adhat hozzá az új táblához.

1. Az Adatkezelőben bontsa ki a **sample-database** és a **sample-table** pontot, és kattintson az **Entitások**, ezután pedig az **Entitás hozzáadása** lehetőségre.
2. Adja meg a PartitionKey és a RowKey mező adatait és kattintson az **Entitás hozzáadása** lehetőségre.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    Most már az Adatkezelővel további entitásokat is hozzá tud adni a táblához, szerkesztheti őket, és lekérdezéseket is indíthat. Az Adatkezelőben továbbá skálázhatja az átviteli sebességet, és tárolt eljárásokat, felhasználói függvényeket és triggereket adhat hozzá a táblához.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy DocumentDB API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, és futtatjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a Program.cs fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB erőforrásokat. 

* A rendszer inicializálja a DocumentClient ügyfelet.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* Új táblázat jön létre, ha még nem létezik.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Létrejön egy új táblázattároló. Láthatja, hogy ez a kód nagyon hasonló a normál Azure Table Storage SDK-hoz 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiók bal oldali oldalsávján kattintson a **Kulcsok** elemre, majd kattintson az **Írási/olvasási kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot az app.config fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén](./media/create-documentdb-dotnet-core/keys.png)

2. Nyissa meg az app.config fájlt a Visual Studióban. 

3. Másolja az Azure Cosmos DB-fiók nevét a Portalról, és az app.config fájlban a PremiumStorageConnection karakterlánc értéknél adja meg a nevet az AccountName paraméter értékeként. A fenti képen a fiók neve: cosmos-db-quickstart. A fiók neve a Portalon legfelül látható.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. Ezután másolja az elsődleges kulcs értékét a Portalról, és adja meg a PremiumStorageConnectionString sztring AccountKey paraméterének értékeként. 

    `AccountKey=AUTHKEY`

5. Végezetül másolja az URI értékét a Portal Kulcsok lapjáról (a Másolás gombbal), és adja meg a PremiumStorageConnectionString sztring TableEndpoint paraméterének értékeként.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    A StandardStorageConnectionString értékét nem szükséges megváltoztatnia.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. A Visual Studióban kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre. 

2. A NuGet **Browse** (Tallózás) mezőjébe írja be *WindowsAzure.Storage* kifejezést, és jelölje be az **Include prerelease** (Előzetes verzió is) jelölőnégyzetet. 

3. Az eredmények közül telepítse a **WindowsAzure.Storage** függvénytárat. Ez telepíti az Azure Cosmos DB Table API csomag előzetes verzióját és annak összes függőségét.

4. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

    A konzolablakban láthatja, hogy a rendszer adatokat ad a táblához. Miután a szkript futtatása befejeződött, bezárhatja a konzolablakot. 

Ezután visszaléphet az Adatkezelőbe, ahol lekérdezheti és módosíthatja az új adatokat, és megkezdheti velük a munkát. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon: 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Lekérdezés a Table API használatával](tutorial-query-table.md)


