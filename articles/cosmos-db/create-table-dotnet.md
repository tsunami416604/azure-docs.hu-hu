---
title: 'Rövid útmutató: Table API a .NET -kel – Azure Cosmos DB'
description: Ez a rövid útmutató ismerteti, hogyan használható az Azure Cosmos DB Table API alkalmazások létrehozására az Azure Portal és a .NET segítségével
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240169"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Rövid útmutató: Table API-alkalmazás létrehozása .NET SDK és Azure Cosmos DB használatával 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Ez a rövid útmutató ismerteti, hogyan használható a .NET és az Azure Cosmos DB [Table API](table-introduction.md) egy alkalmazás létrehozására egy, a GitHubról származó példa klónozásával. Ez a gyors útmutató emellett azt is bemutatja, hogyan hozható létre egy Azure Cosmos DB-fiók, és hogyan használható az Adatkezelő táblák és entitások létrehozására a webes alapú Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

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
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

## <a name="open-the-sample-application-in-visual-studio"></a>A mintaalkalmazás megnyitása a Visual Studióban

1. A Visual Studio **Fájl** menüjében válassza a **Megnyitás**, majd a **Projekt/Megoldás** elemet. 

   ![A megoldás megnyitása](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Nyissa meg azt a mappát, amelyben klónozta a mintaalkalmazást, és nyissa meg a TableStorage.sln fájlt.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Kapcsolati sztring** elemre. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   ![Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.](./media/create-table-dotnet/connection-string.png)

2. A Visual Studióban nyissa meg a **Settings.json** fájlt. 

3. Illessze be az **elsődleges kapcsolati karakterláncot** a portálról a StorageConnectionString értékbe. Illessze be a sztringet az idézőjelek közé.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. A **Settings.json** fájl mentéséhez nyomja le a CTRL+S billentyűkombinációt.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése

1. A Visual Studio jobb gombbal kattintson a **CosmosTableSamples** projektre a **Megoldáskezelőben,** majd kattintson **a NuGet-csomagok kezelése parancsra.** 

   ![NuGet-csomagok kezelése](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. A NuGet **Tallózás** mezőbe írja be a Microsoft.Azure.Cosmos.Table beírását. Ezzel a Cosmos DB Table API-ügyfélkódtárát keresi meg. Ne feledje, hogy ez a tár jelenleg a . 
   
   ![A NuGet Tallózás lapja](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Kattintson a **Telepítés** gombra a **Microsoft.Azure.Cosmos.Table** tár telepítéséhez. Ez telepíti az Azure Cosmos DB Table API csomagot és annak összes függőségét.

4. A teljes alkalmazás futtatásakor a mintaadatok bekerülnek a táblaentitásba, és a végén törlődnek, így nem jelennek meg a beszúrt adatok, ha a teljes mintát futtatja. Az adatok megtekintéséhez azonban beszúrhat néhány töréspontot. Nyissa meg BasicSamples.cs fájlt, és kattintson **Breakpoint**a jobb gombbal az 52. **Insert Breakpoint** Szúrjon be egy másik töréspontot az 55. sorban.

   ![Töréspont hozzáadása](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Az alkalmazás futtatásához nyomja le az F5 billentyűt. A konzolablak az új tábla-adatbázis (ebben az esetben demoa13b1) nevét jeleníti meg az Azure Cosmos DB-ben. 
    
   ![Konzolkimenet](media/create-table-dotnet/azure-cosmosdb-console.png)

   Az első töréspont elérésekor lépjen vissza az Adatkezelőbe az Azure Portalon. Kattintson a **Frissítés** gombra, bontsa ki a demo* táblát, majd kattintson az **Entitások** elemre. A jobb oldali **Entitások** lapon látható a Walter Harp számára újonnan hozzáadott entitás. Láthatja, hogy az új entitáshoz tartozó telefonszám 425-555-0101.

   ![Új entitás](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Ha olyan hibaüzenetet kap, amely szerint a Settings.json fájl nem található a projekt futtatásakor, a projekt beállításaihoz a következő XML-bejegyzés hozzáadásával oldhatja meg. Kattintson a jobb gombbal cosmosTableSamples, válassza Edit CosmosTableSamples.csproj és adjuk hozzá a következő itemGroup: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Zárja be az **Entitások** lapot az Adatkezelőben.
    
7. Az F5 billentyű lenyomásával futtassa az alkalmazást a következő töréspontig. 

    A töréspont elérésekor váltson vissza az Azure Portalra, majd kattintson újból az **Entitások** elemre az **Entitások** lap megnyitásához, ahol láthatja, hogy a telefonszám a következőre frissült: 425-555-0105.

8. Az alkalmazás futtatásához nyomja le az F5 billentyűt. 
 
   Az alkalmazás által hozzáadott entitásokat egy fejlett mintaalkalmazás használja, amelyet a Table API jelenleg még nem támogat. Az alkalmazás ezután törli a mintaalkalmazás által létrehozott táblát.

9. A konzolablakban nyomja le az Enter billentyűt az alkalmazás futásának megszakításához. 
  

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Táblaadatok importálása a Table API-ba](table-import.md)

