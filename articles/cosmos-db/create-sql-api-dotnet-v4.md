---
title: Az Azure Cosmos DB SQL API-erőforrások kezelése a .Net V4 SDK használatával
description: Gyorsútmutató a .Net V4 SDK használatával az Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77585934"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Rövid útmutató: Hozzon létre egy konzolalkalmazást a .Net V4 SDK használatával az Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ismerkedés az Azure Cosmos DB SQL API-ügyféltár .NET. A .NET V4 (Azure.Cosmos) csomag telepítéséhez kövesse a .NET V4 (Azure.Cosmos) csomagot, hozzon létre egy alkalmazást, és próbálja ki az Azure Cosmos DB-ben tárolt adatok alapvető CRUD-műveleteinek példakódját. 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB segítségével gyorsan hozhat létre és kérdezhet le kulcs-érték, dokumentum- és gráfadatbázisokat. Az Azure Cosmos DB SQL API-ügyféltár használatával:

* Hozzon létre egy Azure Cosmos-adatbázist és egy tárolót
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[Tár forráskódcsomagja](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen,](https://azure.microsoft.com/free/) vagy ingyenesen és kötelezettségvállalások nélkül [ingyenesen kipróbálhatja az Azure Cosmos](https://azure.microsoft.com/try/cosmosdb/) DB-t. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). A futtatásával `dotnet --version`ellenőrizheti, hogy melyik verzió érhető el a környezetében.

## <a name="setting-up"></a>Beállítása

Ez a szakasz bemutatja az Azure Cosmos-fiók létrehozását és az Azure Cosmos DB SQL API-ügyfélkódtár a .NET használatával az erőforrások kezeléséhez. A cikkben ismertetett példakód létrehoz egy adatbázist, `FamilyDatabase` és a családtagok (minden családtag egy elem) az adatbázisban. Minden családtagnak vannak `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`tulajdonságai, például . A `LastName` tulajdonság a tároló partíciókulcsa. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ha a [Try Azure Cosmos DB ingyenes](https://azure.microsoft.com/try/cosmosdb/) lehetőséget használja egy Azure Cosmos-fiók létrehozásához, létre kell hoznia egy **SQL API**típusú Azure Cosmos DB-fiókot. Az Azure Cosmos DB tesztfiók már létre van hozva. Nem kell explicit módon létrehoznia a fiókot, így kihagyhatja ezt a szakaszt, és átléphet a következő szakaszra.

Ha saját Azure-előfizetéssel rendelkezik, vagy ingyenes előfizetést hoz létre, explicit módon létre kell hoznia egy Azure Cosmos-fiókot. A következő kód létrehoz egy Azure Cosmos-fiókot munkamenet-konzisztenciával. A fiók replikálása a és a rendszerbe `South Central US` történik. `North Central US`  

Az Azure Cloud Shell segítségével létrehozhatja az Azure Cosmos-fiókot. Az Azure Cloud Shell interaktív, hitelesített, böngészőben elérhető felület az Azure-erőforrások felügyeletéhez. Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület (akár a Bash, akár a PowerShell) kiválasztásának lehetőségével. Ehhez a rövid útmutatóhoz válassza a **Bash** módot. Az Azure Cloud Shell is szüksége van egy tárfiókot, létrehozhat egyet, amikor a rendszer kéri.

Válassza a **Try It** gombot a következő kód mellett, válassza a **Bash** mód lehetőséget válassza **a tárfiók létrehozása** lehetőséget, és jelentkezzen be a Cloud Shellbe. Következő másolás és beillesztés a következő kódot az Azure felhőbeli rendszerhéjba, és futtassa azt. Az Azure Cosmos-fiók nevének globálisan egyedinek `mysqlapicosmosdb` kell lennie, győződjön meg arról, hogy a parancs futtatása előtt frissítse az értéket.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Az Azure Cosmos-fiók létrehozása egy ideig, ha a művelet sikeres, láthatja a megerősítő kimenetet. Miután a parancs sikeresen befejeződött, jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és ellenőrizze, hogy a megadott nevű Azure Cosmos-fiók létezik-e. Az erőforrás létrehozása után bezárhatja az Azure Cloud Shell ablakot. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Új .NET alkalmazás létrehozása

Hozzon létre egy új .NET alkalmazást a kívánt szerkesztőben vagy IDE-ben. Nyissa meg a Windows parancssort vagy a Terminál ablakot a helyi számítógépről. A parancssorból vagy a terminálból a következő szakaszokban lévő összes parancsot futtatja.  Futtassa a következő dotnet új parancsot `todo`egy új alkalmazás létrehozásához a . A --langVersion paraméter beállítja a LangVersion tulajdonságot a létrehozott projektfájlban.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

   ```bash
   cd todo
   dotnet build
   ```

A build várható kimenetének valahogy így kell kinéznie:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Az Azure Cosmos DB-csomag telepítése

Amíg az alkalmazáskönyvtárban van, telepítse az Azure Cosmos DB ügyfélkönyvtárat a .NET Core-hoz a dotnet add package paranccsal.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Az Azure Cosmos-fiók hitelesítő adatainak másolása az Azure Portalról

A mintaalkalmazás hitelesítésére az Azure Cosmos-fiók. A hitelesítéshez adja át az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatainak beszerezése az alábbi lépésekkel:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Keresse meg az Azure Cosmos-fiókot.

1. Nyissa meg a **Kulcsok** ablaktáblát, és másolja a fiók **URI-** és **ELSŐDLEGES KULCSát.** A következő lépésben hozzáadja az URI- és kulcsértékeket egy környezeti változóhoz.

## <a name="object-model"></a><a id="object-model"></a>Objektummodell

Az alkalmazás létrehozása előtt nézzük meg az erőforrások hierarchiáját az Azure Cosmos DB-ben, valamint az erőforrások létrehozásához és eléréséhez használt objektummodellt. Az Azure Cosmos DB a következő sorrendben hoz létre erőforrásokat:

* Azure Cosmos-fiók 
* Adatbázisok 
* Containers 
* Elemek

Ha többet szeretne megtudni a különböző entitások hierarchiájáról, tekintse meg az [adatbázisok, tárolók és elemek használatával való munkát az Azure Cosmos DB-cikkében.](databases-containers-items.md) A következő .NET osztályokat fogja használni az alábbi erőforrások kal való együttműködéshez:

* CosmosClient – Ez az osztály ügyféloldali logikai ábrázolást biztosít az Azure Cosmos DB szolgáltatáshoz. Az ügyfélobjektum a szolgáltatással kapcsolatos kérelmek konfigurálására és végrehajtására szolgál.
* CreateDatabaseIfNotExistsAsync – Ez a metódus aszinkron műveletként létrehoz (ha nem létezik), vagy leválaszt (ha már létezik) . 
* CreateContainerIfNotExistsAsync – Ez a metódus aszinkron műveletként létrehoz (ha nem létezik), vagy leválaszt (ha már létezik) egy tárolót. Ellenőrizheti az állapotkódot a válaszból annak megállapításához, hogy a tároló újonnan jött létre (201) vagy egy meglévő tárolótért vissza (200). 
* CreateItemAsync – Ez a módszer egy elemet hoz létre a tárolón belül.
* UpsertItemAsync – Ez a módszer létrehoz egy elemet a tárolón belül, ha az még nem létezik, vagy lecseréli az elemet, ha már létezik. 
* GetItemQueryIterator – Ez a módszer egy Azure Cosmos-adatbázis tárolója alatti elemek lekérdezését hozza létre egy paraméteres értékekkel rendelkező SQL-utasítás használatával. 
* DeleteAsync – Törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync`metódus csak az adatbázist törli.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kódpéldák

Az ebben a cikkben ismertetett mintakód létrehoz egy családi adatbázist az Azure Cosmos DB-ben. A családi adatbázis olyan családi adatokat tartalmaz, mint a név, a cím, a hely, a kapcsolódó szülők, gyermekek és háziállatok. Mielőtt feltölti az adatokat az Azure Cosmos-fiókba, határozza meg egy családi elem tulajdonságait. Hozzon létre `Family.cs` egy új osztályt, amelynek neve a mintaalkalmazás gyökérszintjén található, és adja hozzá a következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a használatával direktívákat, & definiálja az ügyfélobjektumot

A projekt könyvtárában `Program.cs` nyissa meg a fájlt a szerkesztőben, és adja hozzá a következőket az alkalmazás tetején található irányelvek használatával:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adja hozzá a következő `Program` globális változókat az osztályban. Ezek közé tartozik a végpont és az engedélyezési kulcsok, az adatbázis nevét, és a tároló, amely létrehoz. Győződjön meg arról, hogy cserélje ki a végpont és az engedélyezési kulcsok értékeit a környezetnek megfelelően. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Végül cserélje `Main` ki a módszert:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Adatbázis létrehozása 

Adja `CreateDatabaseAsync` meg a `program.cs` metódust az osztályon belül. Ez a `FamilyDatabase` módszer létrehozza a ha még nem létezik.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Tároló létrehozása

Adja `CreateContainerAsync` meg a `Program` metódust az osztályon belül. Ez a `FamilyContainer` módszer létrehozza a ha még nem létezik. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Elem létrehozása

Hozzon létre egy `AddItemsToContainerAsync` családi elemet a metódus nak a következő kóddal való hozzáadásával. A vagy `UpsertItemAsync` `CreateItemAsync` metódusokkal létrehozhat egy elemet:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Elemek lekérdezése

Egy elem beszúrása után futtathat egy lekérdezést az "Andersen" család részleteinek lekérdezéséhez. A következő kód bemutatja, hogyan hajtható végre a lekérdezés közvetlenül az SQL-lekérdezés használatával. Az SQL lekérdezés, hogy az "Anderson" család adatait: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Adja `QueryItemsAsync` meg a `Program` metódust az osztályon belül, és adja hozzá a következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Elem cseréje 

Olvassa el a családi elemet, `ReplaceFamilyItemAsync` majd frissítse a metódust a következő kóddal.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Elem törlése 

Családi elem törlése a `DeleteFamilyItemAsync` metódus nak a következő kóddal való hozzáadásával.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Az adatbázis törlése 

Végül törölheti az adatbázist, hozzátéve, hogy a `DeleteDatabaseAndCleanupAsync` metódus a következő kódot:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Miután hozzáadja az összes `Program` szükséges módszert, mentse a fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Következő build és az alkalmazás futtatásához az Azure Cosmos DB-erőforrások létrehozásához.

   ```bash
   dotnet run
   ```

Az alkalmazás futtatásakor a következő kimenet jön létre. Be is jelentkezhet az Azure Portalon, és ellenőrizheti, hogy az erőforrások létrejönnek-e:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Ellenőrizheti, hogy az adatok az Azure Portalon való bejelentkezéssel jönnek-e létre, és tekintse meg a szükséges elemeket az Azure Cosmos-fiókban. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, használhatja az Azure CLI vagy az Azure PowerShell az Azure Cosmos-fiók és a megfelelő erőforráscsoport eltávolításához. A következő parancs bemutatja, hogyan törölheti az erőforráscsoportot az Azure CLI használatával:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre egy adatbázist és egy tárolót egy .NET Core alkalmazás használatával. Most már importálhat további adatokat az Azure Cosmos-fiókba az alábbi cikkben található utasításokkal. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
