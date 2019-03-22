---
title: 'Gyors útmutató: TABLE API .NET-tel – Azure Cosmos DB használatával'
description: Ez a rövid útmutató ismerteti, hogyan használható az Azure Cosmos DB Table API alkalmazások létrehozására az Azure Portal és a .NET segítségével
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 7ca51b176c17f33b4779a0129c5dc57b220c0097
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877632"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Gyors útmutató: Table API-alkalmazás a .NET SDK-t és az Azure Cosmos DB létrehozása 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Ez a rövid útmutató ismerteti, hogyan használható a .NET és az Azure Cosmos DB [Table API](table-introduction.md) egy alkalmazás létrehozására egy, a GitHubról származó példa klónozásával. Ez a gyors útmutató emellett azt is bemutatja, hogyan hozható létre egy Azure Cosmos DB-fiók, és hogyan használható az Adatkezelő táblák és entitások létrehozására a webes alapú Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

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
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```
   > [!NOTE]
   > A .NET standard minta ebben a dokumentumban ismertetett Azure Cosmos DB Table API és az Azure Table Storage is működik. Ha érdekli, amely a minta futtatása a .NET-keretrendszer 4.5 együttműködik, tekintse meg a [storage-table-dotnet-getting-started](https://github.com/Azure-Samples/storage-table-dotnet-getting-started) minta. 


## <a name="open-the-sample-application-in-visual-studio"></a>A mintaalkalmazás megnyitása a Visual Studióban

1. A Visual Studio **Fájl** menüjében válassza a **Megnyitás**, majd a **Projekt/Megoldás** elemet. 

   ![A megoldás megnyitása](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Lépjen abba a mappába, amelybe a mintaalkalmazást klónozta, és nyissa meg TableStorage.sln fájlt.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Kapcsolati sztring** elemre. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   ![Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.](./media/create-table-dotnet/connection-string.png)

2. A Visual Studióban nyissa meg a **Settings.json** fájlt. 

3. Illessze be a **elsődleges KAPCSOLATI karakterlánc** a portálról StorageConnectionString értékébe. Illessze be a sztringet az idézőjelek közé.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```
   Ha használja a [.NET-keretrendszer](https://github.com/Azure-Samples/storage-table-dotnet-getting-started) mintát, frissítse a kapcsolati karakterlánc található a **App.config** fájlt.

4. Nyomja le a CTRL + S menteni a **Settings.json** fájlt.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése

1. A Visual Studióban kattintson a jobb gombbal a a **CosmosTableSamples** projektre a **Megoldáskezelőben** majd **NuGet-csomagok kezelése**. 

   ![NuGet-csomagok kezelése](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. A nuget **Tallózás** mezőbe írja be a Microsoft.Azure.Cosmos.Table. Ezzel a Cosmos DB Table API-ügyfélkódtárát keresi meg. Vegye figyelembe, hogy a könyvtár a .NET-keretrendszer és a .NET Standard jelenleg elérhető. 

   > [!NOTE]
   > Ha a .NET-keretrendszer használó mintákat használ, telepítenie kell a *Microsoft.Azure.CosmosDB.Table*, NuGet-csomagot. Ha az Azure Table Storage a .NET-keretrendszer mintát használja, a Cosmos DB adott NuGet-csomag mellett is szüksége van a *Microsoft.Azure.Storage.Common* NuGet-csomagot. 
   
   ![A NuGet Tallózás lapja](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Kattintson a **telepítése** telepítéséhez a **Microsoft.Azure.Cosmos.Table** könyvtár. Ez telepíti az Azure Cosmos DB Table API csomagot és annak összes függőségét.

4. A teljes alkalmazás futtatásakor a mintaadatok tábla entitásba beszúrásakor és törlésekor a végén, így adatokat beszúrni, ha a teljes minta futtatása nem jelenik meg. Azonban beszúrhat néhány töréspontokat a kiválasztott adatok megtekintéséhez. Nyissa meg a BasicSamples.cs fájlt, és kattintson a jobb gombbal az 52, jelölje be **töréspontot**, majd **Töréspont beszúrása**. Szúrjon be egy másik töréspontot az 55. sorban.

   ![Töréspont hozzáadása](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Az alkalmazás futtatásához nyomja le az F5 billentyűt. A konzolablakban az Azure Cosmos DB új táblaadatbázisához (ebben az esetben az demoa13b1) nevét megjeleníti. 
    
   ![Konzolkimenet](media/create-table-dotnet/azure-cosmosdb-console.png)

   Az első töréspont elérésekor lépjen vissza az Adatkezelőbe az Azure Portalon. Kattintson a **Frissítés** gombra, bontsa ki a demo* táblát, majd kattintson az **Entitások** elemre. A jobb oldali **Entitások** lapon látható a Walter Harp számára újonnan hozzáadott entitás. Láthatja, hogy az új entitáshoz tartozó telefonszám 425-555-0101.

   ![Új entitás](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Ha hibaüzenet arról, hogy a Settings.json fájl nem található, ha futtatja a projektet, megoldhatja a Projektbeállítások között a következő XML-bejegyzés hozzáadásával. A jobb gombbal a CosmosTableSamples, CosmosTableSamples.csproj szerkesztéséhez válassza ki, és adja hozzá a következő itemGroup: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Zárja be az **Entitások** fület az Adatkezelőben.
    
7. Az F5 billentyű lenyomásával futtassa az alkalmazást a következő töréspontig. 

    A töréspont elérésekor váltson vissza az Azure Portalra, majd kattintson újból az **Entitások** elemre az **Entitások** lap megnyitásához, ahol láthatja, hogy a telefonszám a következőre frissült: 425-555-0105.

8. Az alkalmazás futtatásához nyomja le az F5 billentyűt. 
 
   Az alkalmazás által hozzáadott entitásokat egy fejlett mintaalkalmazás használja, amelyet a Table API jelenleg még nem támogat. Az alkalmazás ezután törli a mintaalkalmazás által létrehozott táblát.

9. A konzolablakban nyomja le az Enter billentyűt az alkalmazás futásának megszakításához. 
  

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Táblaadatok importálása a Table API-ba](table-import.md)

