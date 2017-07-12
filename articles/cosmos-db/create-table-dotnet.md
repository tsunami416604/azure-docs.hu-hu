---
title: "Azure Cosmos DB .NET-alkalmazás létrehozása a Table API-val | Microsoft Docs"
description: "Bevezetés az Azure Cosmos DB Table API-jának .NET-alapú használatába"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/22/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 29e7eebda5177d6e852ef04ad82d9d38a8d30ed8
ms.contentlocale: hu-hu
ms.lasthandoff: 06/23/2017


---
<a id="azure-cosmos-db-build-a-net-application-using-the-table-api" class="xliff"></a>

# Azure Cosmos DB: .NET-alkalmazás létrehozása a Table API-val

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével egy Azure Cosmos DB-fiókot, majd abban egy táblát. Ezután kódot írunk az entitások beillesztésére, frissítésére és törlésére és futtatunk pár lekérdezést az új [Windows Azure Storage prémium tábla](https://aka.ms/premiumtablenuget) NuGet-csomag előzetes verziójának használatával. Ennek a függvénytárnak az osztályai és a metódusainak aláírásai megegyeznek a nyilvános [Windows Azure Storage SDK-ban](https://www.nuget.org/packages/WindowsAzure.Storage) elérhetőkkel, de ez a verzió ezenfelül képes Azure Cosmos DB-fiókokhoz is kapcsolódni a [Table API](table-introduction.md) előzetes verziójának használatával. 

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

<a id="add-a-table" class="xliff"></a>

## Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

<a id="add-sample-data" class="xliff"></a>

## Mintaadatok hozzáadása

Az Adatkezelő előzetes verziójának segítségével adatokat adhat hozzá az új táblához.

1. Az Adatkezelőben bontsa ki a **minta tábla** pontot, és kattintson az **Entitások**, ezután pedig az **Entitás hozzáadása** lehetőségre.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Adja meg a PartitionKey és a RowKey mező adatait és kattintson az **Entitás hozzáadása** lehetőségre.

   ![A partíciókulcs és a sorkulcs beállítása új entitások számára](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Most már az Adatkezelővel további entitásokat is hozzá tud adni a táblához, szerkesztheti őket, és lekérdezéseket is indíthat. Az Adatkezelőben továbbá skálázhatja az átviteli sebességet, és tárolt eljárásokat, felhasználói függvényeket és triggereket adhat hozzá a táblához.

<a id="clone-the-sample-application" class="xliff"></a>

## A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban. 

<a id="review-the-code" class="xliff"></a>

## A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a Program.cs fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB erőforrásokat. 

* A CloudTableClient inicializálásra kerül.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* Új táblázat jön létre, ha még nem létezik.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Létrejön egy új táblázattároló. Láthatja, hogy ez a kód nagyon hasonló a normál Azure Table Storage SDK-hoz. 

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

<a id="update-your-connection-string" class="xliff"></a>

## A kapcsolati karakterlánc frissítése

Most pedig frissítjük a kapcsolati karakterláncot, hogy az alkalmazása kommunikálni tudjon az Azure Cosmos DB-szolgáltatással. 

1. Nyissa meg az app.config fájlt a Visual Studióban. 

2. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB bal oldali navigációs menüjében kattintson a **Kapcsolati karakterlánc** elemre. Ezután az új panelen kattintson a kapcsolati karakterlánc másolás gombjára. 

    ![Keresse meg és másolja ki a Végpont és a Fiókkulcs mezőket a Kapcsolati karakterlánc panelen](./media/create-table-dotnet/keys.png)

3. Az értéket illessze be az app.config fájlba a PremiumStorageConnectionString értékéhez. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    A StandardStorageConnectionString értékét nem szükséges megváltoztatnia.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

<a id="run-the-web-app" class="xliff"></a>

## A webalkalmazás futtatása

1. A Visual Studióban kattintson a jobb gombbal a **PremiumTableGetStarted** projektre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre. 

2. A NuGet **Tallózás** mezőjébe írja be a *WindowsAzure.Storage-PremiumTable* kifejezést.

3. Jelölje be az **Előzetes verzió is** jelölőnégyzetet. 

4. Az eredmények közül telepítse a **WindowsAzure.Storage-PremiumTable** függvénytárat. Ez telepíti az Azure Cosmos DB Table API csomag előzetes verzióját és annak összes függőségét. Vegye figyelembe, hogy ez a NuGet-csomag eltér az Azure Table Storage által használt Windows Azure Storage csomagtól. 

5. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

    A konzolablakban követheti az adatok hozzáadásának, beolvasásának, lekérdezésének, cseréjének és a táblából történő törlésének folyamatait. Miután a parancsfájl futása befejeződött, nyomjon le egy tetszőleges billentyűt a konzolablak bezárásához. 
    
    ![A gyors útmutató lépéseinek konzolkimenete](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Ha meg szeretné tekinteni az új entitásokat az Adatkezelőben, tegye megjegyzésbe a program.cs fájl 188-208. sorait – így azokat nem kell törölni – , majd ezután futtassa újra a mintakódot. 

    Ezután lépjen vissza az Adatkezelőbe, kattintson a **Frissítés** gombra, bontsa ki a **people** táblát, kattintson az **Entitások** elemre és máris munkához láthat az új adatokkal. 

    ![Új entitások az Adatkezelőben](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon: 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Lekérdezés a Table API használatával](tutorial-query-table.md)


