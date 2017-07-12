---
title: "Azure Cosmos DB: Webalkalmazás létrehozása .NET-tel és DocumentDB API-val | Microsoft Docs"
description: "Egy .NET-es kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB DocumentDB API-hoz és lekérdezést lehet végezni vele"
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
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: d17f90d5ed5440dc336d1e3ae890a13077e33c4d
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-and-the-azure-portal" class="xliff"></a>

# Azure Cosmos DB: DocumentDB API-webalkalmazás létrehozása .NET-tel és az Azure Portallal

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB-fiókot, dokumentum-adatbázist és gyűjteményt. Ezután megtudhatja, hogyan hozhat létre és hogyan helyezhet üzembe egy a [DocumentDB .NET API-n](documentdb-sdk-dotnet.md) alapuló teendőlista-kezelő webalkalmazást. (Lásd az alábbi képernyőfelvételen.) 

![Teendőkezelő alkalmazás mintaadatokkal](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>
<a id="create-a-database-account" class="xliff"></a>

## Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection"></a>
<a id="add-a-collection" class="xliff"></a>

## Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
<a id="add-sample-data" class="xliff"></a>

## Mintaadatok hozzáadása

Az Adatkezelő segítségével adatokat adhat hozzá az új gyűjteményhez.

1. Az új adatbázis az Adatkezelőben a Gyűjtemények ablaktáblán jelenik meg. Bontsa ki a **Feladatok** adatbázist, majd az **Elemek** gyűjteményt, végül kattintson a **Dokumentumok**, majd pedig az **Új dokumentumok** lehetőségre. 

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. Adjon hozzá néhány dokumentumot a gyűjteményhez az alábbi struktúra használatával. Az egyes dokumentumok azonosítóinak egyedinek kell lennie, a többi tulajdonság azonban tetszés szerint adható meg. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. A JSON hozzáadása után a **Dokumentumok** lapon kattintson a **Mentés** gombra.

    ![Másolja át a json-adatokat, és kattintson a Mentés gombra az Adatkezelőben az Azure-portálon](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

     Az Adatkezelővel így már lekérdezések használatával lekérheti adatait. Alapértelmezés szerint az Adatkezelő a `SELECT * FROM c` paranccsal kéri le a gyűjteményhez tartozó összes dokumentumot, a `SELECT * FROM c ORDER BY c.name ASC` parancs használatával azonban a Név tulajdonság szerint betűrendbe rendezve is lekérheti a dokumentumokat. 
 
     Az Adatkezelővel létrehozhat tárolt eljárásokat is, felhasználói függvényeket és a kiszolgálóoldali üzleti logikákat végrehajtó eseményindítókat, valamint szabályozhatja az átviteli sebességet. Az Adatkezelő hozzáférhetővé teszi az API-k összes beépített, programozható adatelérési funkcióját, és az Azure Portalon tárolt adataihoz is egyszerű hozzáférést biztosít.

<a id="clone-the-sample-application" class="xliff"></a>

## A mintaalkalmazás klónozása

Most pedig klónozunk egy DocumentDB API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, és futtatni is fogjuk. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `CD` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Ezután nyissa meg a teendő megoldásfájlját a Visual Studióban. 

<a id="review-the-code" class="xliff"></a>

## A kód áttekintése

Tekintsük át, hogy mi is történik az alkalmazásban. Nyissa meg a DocumentDBRepository.cs fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB-erőforrásokat. 

* A DocumentClient inicializálva van a 73. sorban.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* A rendszer létrehozza az új adatbázist a 88. sorban.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* A rendszer létrehozza az új gyűjteményt a 107. sorban.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

<a id="update-your-connection-string" class="xliff"></a>

## A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **Kulcsok** elemre, majd kattintson az **Írási/olvasási kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot a web.config fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok panelén](./media/create-documentdb-dotnet/keys.png)

2. Nyissa meg a web.config fájlt a Visual Studio 2017 alkalmazásban. 

3. A másolási gomb használatával másolja ki az URI érteket a Portalról, és azt adja meg a végpont kulcs értékeként a web.config fájlban. 

    `<add key="endpoint" value="FILLME" />`

4. Ezután másolja ki az ELSŐDLEGES KULCS értékét a Portalról, és azt adja meg az authKey értékeként a web.config fájlban. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `<add key="authKey" value="FILLME" />`
    
<a id="run-the-web-app" class="xliff"></a>

## A webalkalmazás futtatása
1. A Visual Studióban kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre. 

2. A NuGet **Tallózás** mezőjébe írja be a *DocumentDB* szöveget.

3. Az eredmények közül telepítse a **Microsoft.Azure.DocumentDB** kódtárat. Ez telepíti a Microsoft.Azure.DocumentDB csomagot, valamint annak összes függőségét.

4. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. Az alkalmazás megjelenik a böngészőben. 

5. Kattintson a **Új létrehozása** lehetőségre a böngészőben, és hozzon létre néhány új feladatot a teendőkezelő alkalmazásban.

   ![Teendőkezelő alkalmazás mintaadatokkal](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

Lépjen vissza az Adatkezelőbe, ahol lekérdezheti és módosíthatja az új adatokat, valamint dolgozhat azokkal. 

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Ebben a rövid útmutatóban megtudtuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gyűjteményt készíteni, és hogyan lehet futtatni a webalkalmazást. Így már további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)



