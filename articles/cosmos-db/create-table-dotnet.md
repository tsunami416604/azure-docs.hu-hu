---
title: "Gyors üzembe helyezés: Tábla API a .NET - Azure Cosmos DB |} Microsoft Docs"
description: "A gyors üzembe helyezés bemutatja, hogyan hozzon létre egy alkalmazást az Azure portál és a .NET az Azure Cosmos DB tábla API használatával"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/16/2017
ms.author: mimig
ms.openlocfilehash: 4e59c333e14e5e21a02c3160cf6311d1182e5a5e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Gyors üzembe helyezés: Egy tábla .NET és Azure Cosmos DB API-alkalmazás létrehozása 

A gyors üzembe helyezés bemutatja, hogyan használja a Java és az Azure Cosmos DB [tábla API](table-introduction.md) egy példa a Githubról klónozásával az alkalmazás elkészítésére. A gyors üzembe helyezés is bemutatja, hogyan Azure Cosmos DB-fiók létrehozása és adatok kezelővel létrehozása a táblákat és entitásokat a web-alapú Azure-portálon.

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

Az Adatkezelő segítségével adatokat adhat hozzá az új táblához.

1. Az Adatkezelőben bontsa ki a **minta tábla** pontot, és kattintson az **Entitások**, ezután pedig az **Entitás hozzáadása** lehetőségre.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Most adatok hozzáadása a PartitionKey érték mezőhöz, illetve RowKey értéket, és kattintson a **entitás hozzáadása**.

   ![A partíciókulcs és a sorkulcs beállítása új entitások számára](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Most már az Adatkezelővel további entitásokat is hozzá tud adni a táblához, szerkesztheti őket, és lekérdezéseket is indíthat. Az Adatkezelőben továbbá skálázhatja az átviteli sebességet, és tárolt eljárásokat, felhasználói függvényeket és triggereket adhat hozzá a táblához.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és használja a `cd` parancs futtatásával módosíthatja a mintaalkalmazás telepítése mappába. 

    ```bash
    cd "C:\git-samples"
    ```

2. Futtassa a következő parancsot a minta tárház klónozásához. Ezzel a paranccsal létrejön egy mintaalkalmazás példányát a számítógépen. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a TableStorage megoldásfájlt a Visual Studióban. 

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás a szolgáltatott adatbázissal való kommunikációhoz. 

1. Az a [Azure-portálon](http://portal.azure.com/), kattintson a **kapcsolati karakterlánc**. 

    A képernyő jobb oldalán lévő másolás gombok segítségével másolja az elsődleges KAPCSOLATI KARAKTERLÁNCOT.

    ![Megtekintése és másolása az elsődleges kapcsolódási karakterlánc a kapcsolati karakterlánc panelen](./media/create-table-dotnet/connection-string.png)

2. A Visual Studióban nyissa meg az App.config fájlt. 

3. Állítsa vissza a StorageConnectionString sor 8 és a StorageConnectionString sor 7 megjegyzésbe, mivel ez az oktatóanyag nem használ a Storage Emulator. 7. és 8 sor most példához hasonló:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Az elsődleges KAPCSOLATI KARAKTERLÁNCOT a portálról illessze be a StorageConnectionString érték a 8. Illessze be a karakterláncot, az idézőjelek közé foglalt belül. Ha a végponthoz documents.azure.com használ, módosítsa a része, table.cosmosdb.azure.com. 

    Sor 8 most hasonlóan kell kinéznie:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

4. Mentse az App.config fájlt.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="build-and-deploy-the-app"></a>Hozza létre, és az alkalmazás központi telepítése

1. A Visual Studióban, kattintson a jobb gombbal a a **TableStorage** a projekt **Megoldáskezelőben** majd **NuGet-csomagok kezelése**. 

2. A nuget **Tallózás** mezőbe írja be *Microsoft.Azure.CosmosDB.Table*.

3. Az eredmények közül telepítse a **Microsoft.Azure.CosmosDB.Table** könyvtárban. Ez telepíti az Azure Cosmos DB tábla API-csomagot, valamint az összes függősége.

4. Nyissa meg a BasicSamples.cs, majd adja hozzá a töréspont 30 és a sor 52.

5. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

    A konzolablakban a felvenni kívánt Azure Cosmos DB-ben az új táblázat adatbázis adatait jeleníti meg. 
    
    Ha függőségek kapcsolatos hibaüzenetet kap, tekintse meg [hibaelhárítás](table-sdk-dotnet.md#troubleshooting).

    Az első töréspont, lépjen vissza adatkezelő az Azure portálon, és bontsa ki a bemutató * tábla, és kattintson a **entitások**. A **entitások** a jobb oldali lapon láthatók a hozzáadott új entitás, Megjegyzés: Ez a telefonszám, a felhasználó 425-555-0101 érték.
    
6. Zárja be a adatkezelő entitások lapján.
    
7. Továbbra is futtassa az alkalmazást a következő töréspont.

    Elérte a töréspont, amikor visszavált a portálon, kattintson ismét a nyissa meg az entitások lapját, és vegye figyelembe, hogy megújult a telefonszám 425-555-0105 entitások.

8. Vissza a konzolablakban nyomja le a CTRL + C billentyűkombinációt az alkalmazás végrehajtásának befejezése. 

    Akkor is most lépjen vissza adatkezelő hozzáadása vagy módosításához az entitást, és a lekérdezést.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [A tábla API tábla adatok importálása](table-import.md)

