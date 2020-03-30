---
title: Rövid útmutató – .NET konzolalkalmazás létrehozása az Azure Cosmos DB SQL API-erőforrások kezeléséhez
description: Ebből a rövid útmutatóból megtudhatja, hogy miként hozhat létre .NET konzolalkalmazást az Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240414"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Rövid útmutató: .NET konzolalkalmazás létrehozása az Azure Cosmos DB SQL API-erőforrások kezeléséhez

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ismerkedés az Azure Cosmos DB SQL API-ügyféltár .NET. A dokumentum lépéseit követve telepítse a .NET csomagot, hozzon létre egy alkalmazást, és próbálja ki az Azure Cosmos DB-ben tárolt adatok alapvető CRUD-műveleteinek példakódját. 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB segítségével gyorsan hozhat létre és kérdezhet le kulcs-érték, dokumentum- és gráfadatbázisokat. Az Azure Cosmos DB SQL API-ügyféltár használatával:

* Hozzon létre egy Azure Cosmos-adatbázist és egy tárolót
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[API-referenciadokumentáció](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | [könyvtár forráskódcsomagja](https://github.com/Azure/azure-cosmos-dotnet-v3) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen,](https://azure.microsoft.com/free/) vagy ingyenesen és kötelezettségvállalások nélkül [ingyenesen kipróbálhatja az Azure Cosmos](https://azure.microsoft.com/try/cosmosdb/) DB-t. 
* A [.NET Core 2.1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core/2.1).

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

```console
dotnet new console --langVersion 7.1 -n todo
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```console
cd todo
dotnet build
```

A build várható kimenetének valahogy így kell kinéznie:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Az Azure Cosmos DB-csomag telepítése

Amíg az alkalmazáskönyvtárban van, telepítse az Azure Cosmos DB ügyfélkönyvtárat a .NET Core-hoz a dotnet add package paranccsal.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Az Azure Cosmos-fiók hitelesítő adatainak másolása az Azure Portalról

A mintaalkalmazás hitelesítésére az Azure Cosmos-fiók. A hitelesítéshez adja át az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatainak beszerezése az alábbi lépésekkel:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Keresse meg az Azure Cosmos-fiókot.

1. Nyissa meg a **Kulcsok** ablaktáblát, és másolja a fiók **URI-** és **ELSŐDLEGES KULCSát.** A következő lépésben hozzáadja az URI- és kulcsértékeket egy környezeti változóhoz.

### <a name="set-the-environment-variables"></a>A környezeti változók beállítása

Miután átmásolta a fiók **URI-ját** és **elsődleges kulcsát,** mentse őket egy új környezeti változóba az alkalmazást futtató helyi számítógépen. A környezeti változó beállításához nyisson meg egy konzolablakot, és futtassa a következő parancsot. Győződjön meg `<Your_Azure_Cosmos_account_URI>` `<Your_Azure_Cosmos_account_PRIMARY_KEY>` róla, hogy cserélje ki és értékeket.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Macos**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objektummodell

Az alkalmazás létrehozása előtt nézzük meg az erőforrások hierarchiáját az Azure Cosmos DB-ben, valamint az erőforrások létrehozásához és eléréséhez használt objektummodellt. Az Azure Cosmos DB a következő sorrendben hoz létre erőforrásokat:

* Azure Cosmos-fiók 
* Adatbázisok 
* Containers 
* Elemek

Ha többet szeretne megtudni a különböző entitások hierarchiájáról, tekintse meg az [adatbázisok, tárolók és elemek használatával való munkát az Azure Cosmos DB-cikkében.](databases-containers-items.md) A következő .NET osztályokat fogja használni az alábbi erőforrások kal való együttműködéshez:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) – Ez az osztály ügyféloldali logikai ábrázolást biztosít az Azure Cosmos DB szolgáltatáshoz. Az ügyfélobjektum a szolgáltatással kapcsolatos kérelmek konfigurálására és végrehajtására szolgál.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) – Ez a metódus aszinkron műveletként létrehoz (ha nem létezik), vagy leválaszt (ha már létezik) . 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)- - Ez a módszer létrehoz (ha nem létezik), vagy leválaszt (ha már létezik) egy tárolót aszinkron műveletként. Ellenőrizheti az állapotkódot a válaszból annak megállapításához, hogy a tároló újonnan jött létre (201) vagy egy meglévő tárolótért vissza (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) – Ez a módszer egy elemet hoz létre a tárolón belül. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) – Ez a módszer létrehoz egy elemet a tárolón belül, ha az még nem létezik, vagy lecseréli az elemet, ha már létezik. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) – Ez a módszer egy Azure Cosmos-adatbázis tárolója alatti elemek lekérdezését hozza létre egy paraméteres értékekkel rendelkező SQL-utasítás használatával. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) – Törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync`metódus csak az adatbázist törli. A `Cosmosclient` példány ártalmatlanításának külön kell történnie (amit a DeleteDatabaseAndCleanupAsync metódusban végez. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Kódpéldák

Az ebben a cikkben ismertetett mintakód létrehoz egy családi adatbázist az Azure Cosmos DB-ben. A családi adatbázis olyan családi adatokat tartalmaz, mint a név, a cím, a hely, a kapcsolódó szülők, gyermekek és háziállatok. Mielőtt feltölti az adatokat az Azure Cosmos-fiókba, határozza meg egy családi elem tulajdonságait. Hozzon létre `Family.cs` egy új osztályt, amelynek neve a mintaalkalmazás gyökérszintjén található, és adja hozzá a következő kódot:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a használatával direktívákat, & definiálja az ügyfélobjektumot

A projekt könyvtárában `Program.cs` nyissa meg a fájlt a szerkesztőben, és adja hozzá a következőket az alkalmazás tetején található irányelvek használatával:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

A **Program.cs** fájlhoz adjon hozzá kódot az előző lépésben beállított környezeti változók olvasásához. Adja `CosmosClient`meg `Database`a `Container` , és az objektumokat. Ezután adja hozzá a kódot `GetStartedDemoAsync` a fő metódus, amely meghívja a metódust, ahol az Azure Cosmos-fiók erőforrásait kezeli. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Adatbázis létrehozása 

Adja `CreateDatabaseAsync` meg a `program.cs` metódust az osztályon belül. Ez a `FamilyDatabase` módszer létrehozza a ha még nem létezik.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tároló létrehozása

Adja `CreateContainerAsync` meg a `program.cs` metódust az osztályon belül. Ez a `FamilyContainer` módszer létrehozza a ha még nem létezik. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Elem létrehozása

Hozzon létre egy `AddItemsToContainerAsync` családi elemet a metódus nak a következő kóddal való hozzáadásával. A vagy `UpsertItemAsync` `CreateItemAsync` metódusokkal létrehozhat egy elemet:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Elemek lekérdezése

Egy elem beszúrása után futtathat egy lekérdezést az "Andersen" család részleteinek lekérdezéséhez. A következő kód bemutatja, hogyan hajtható végre a lekérdezés közvetlenül az SQL-lekérdezés használatával. Az SQL lekérdezés, hogy az "Anderson" család adatait: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Adja `QueryItemsAsync` meg a `program.cs` metódust az osztályon belül, és adja hozzá a következő kódot:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Az adatbázis törlése 

Végül törölheti az adatbázist, hozzátéve, hogy a `DeleteDatabaseAndCleanupAsync` metódus a következő kódot:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>A CRUD-műveletek végrehajtása

Miután definiálta az összes szükséges metódust, hajtsa végre azokat a `GetStartedDemoAsync` metódusban. A `DeleteDatabaseAndCleanupAsync` módszer kommentálta ki ezt a kódot, mert nem fog látni semmilyen erőforrást, ha a módszer végrehajtása. Uncomment it után érvényesíti, hogy az Azure Cosmos DB-erőforrások az Azure Portalon jött létre. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Miután hozzáadja az összes `Program.cs` szükséges módszert, mentse a fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Következő build és az alkalmazás futtatásához az Azure Cosmos DB-erőforrások létrehozásához. Győződjön meg arról, hogy új parancssori ablakot nyit meg, ne használja ugyanazt a példányt, amelyet a környezeti változók beállításához használt. Mivel a környezeti változók nincsenek beállítva az aktuális nyitott ablakban. A frissítések megtekintéséhez új parancssort kell megnyitnia. 

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás futtatásakor a következő kimenet jön létre. Be is jelentkezhet az Azure Portalon, és ellenőrizheti, hogy az erőforrások létrejönnek-e:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

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
