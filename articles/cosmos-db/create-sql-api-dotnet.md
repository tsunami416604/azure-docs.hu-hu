---
title: .NET-konzol alkalmazás létrehozása Azure Cosmos DB SQL API-erőforrások kezeléséhez
description: Ismerje meg, hogyan hozhat létre egy .NET-konzolos alkalmazást Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: c738b2d44c5faca1ef95b2da8fd1f90a1b3af919
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371022"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Gyors útmutató: .NET-konzol alkalmazás létrehozása Azure Cosmos DB SQL API-erőforrások kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Ismerkedés a Azure Cosmos DB SQL API .NET-hez készült ügyféloldali kódtáraval. Kövesse a jelen dokumentum lépéseit a .NET-csomag telepítéséhez, egy alkalmazás létrehozásához, és próbálja ki az alapszintű szifilisz-műveletekre vonatkozó példát a Azure Cosmos DBban tárolt adatokon. 

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. A Azure Cosmos DB a kulcs/érték, a dokumentum és a Graph-adatbázisok gyors létrehozásához és lekérdezéséhez használható. A .NET-hez készült Azure Cosmos DB SQL API ügyféloldali kódtára a következőre használható:

* Azure Cosmos-adatbázis és-tároló létrehozása
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[API](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | -referenciák dokumentációs[könyvtárának forráskód](https://github.com/Azure/azure-cosmos-dotnet-v3) | [-csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/) , vagy ingyenes Azure-előfizetés nélkül is [kipróbálhat Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) 
* A [.net Core 2,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti egy Azure Cosmos-fiók létrehozásán és egy olyan projekt beállításán, amely Azure Cosmos DB SQL API-ügyfél-függvénytárat használ a .NET-hez az erőforrások kezeléséhez. A cikkben ismertetett mintakód létrehoz egy adatbázist és `FamilyDatabase` egy családtagot (az egyes családtagok egy elem) az adott adatbázison belül. Minden családtag olyan tulajdonságokkal rendelkezik, mint `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`például a. A `LastName` tulajdonságot a tároló partíciós kulcsaként használja a rendszer. 

### <a id="create-account"></a>Azure Cosmos-fiók létrehozása

A következő kód egy Azure Cosmos-fiókot hoz létre a munkamenet konzisztenciájával. A fiók a és `South Central US` `North Central US`a-ben replikálódik. Jelölje ki  a kipróbálom gombot, és illessze be a kódot az Azure Cloud shellben való futtatáshoz. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

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

### <a id="create-dotnet-core-app"></a>Új .NET-alkalmazás létrehozása

Hozzon létre egy új .NET-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. A konzol ablakban futtassa a következő DotNet új parancsot egy új, nevű `todo`alkalmazás létrehozásához.

```console
dotnet new console --langVersion 7.1 -n todo
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
cd todo
dotnet build
```

A Build várt kimenetének a következőhöz hasonlóan kell kinéznie:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>A Azure Cosmos DB csomag telepítése

Miközben továbbra is az alkalmazás könyvtárában található, telepítse a .NET Core-hoz készült Azure Cosmos DB ügyféloldali kódtárat a DotNet-csomag hozzáadása paranccsal.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Az Azure Cosmos-fiók hitelesítő adatainak másolása a Azure Portal

A minta alkalmazásnak hitelesítenie kell magát az Azure Cosmos-fiókban. A hitelesítéshez át kell adni az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatait a következő lépésekkel szerezheti be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához. 

1. Nyissa meg a **kulcsok** ablaktáblát, és másolja a fiókjának **URI-JÁT** és **elsődleges kulcsát** . A következő lépésben hozzá kell adnia az URI és a kulcsok értékeit egy környezeti változóhoz.

### <a name="set-the-environment-variables"></a>Környezeti változók beállítása

Miután átmásolta a fiókja **URI-ját** és **elsődleges kulcsát** , mentse azokat egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó beállításához nyisson meg egy konzolablak ablakot, és futtassa a következő parancsot. Ügyeljen rá, hogy `<Your_Azure_Cosmos_account_URI>` cserélje `<Your_Azure_Cosmos_account_PRIMARY_KEY>` le és adja meg az értékeket.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Objektummodell

Az alkalmazás létrehozásának megkezdése előtt tekintsük át a Azure Cosmos DB erőforrásainak hierarchiáját, és az erőforrások létrehozásához és eléréséhez használt objektummodell. A Azure Cosmos DB erőforrásokat hoz létre a következő sorrendben:

* Azure Cosmos-fiók 
* Adatbázisok 
* Containers 
* Elemek

A különböző entitások hierarchiájának megismeréséhez tekintse meg az [adatbázisok, tárolók és elemek használata Azure Cosmos db](databases-containers-items.md) cikkben. A következő .NET-osztályokat fogja használni az alábbi erőforrásokkal való interakcióhoz:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) – ez az osztály a Azure Cosmos db szolgáltatás ügyféloldali logikai ábrázolását biztosítja. Az ügyfél-objektum a kérések konfigurálására és végrehajtására szolgál a szolgáltatáson.

* [Createdatabaseifnotexistasync metódusának](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) – ez a metódus (ha nem létezik) vagy (ha már létezik) egy adatbázis-erőforrás aszinkron műveletként való létrehozása. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)– ez a metódus létrehoz (ha nem létezik), vagy (ha már létezik) a tároló aszinkron műveletként. A válaszban található állapotkód alapján megállapíthatja, hogy a tárolót újonnan hozták-e létre (201), vagy egy meglévő tárolót adott vissza (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) – ez a metódus létrehoz egy tételt a tárolón belül. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) – ez a metódus létrehoz egy lekérdezést az Azure Cosmos-adatbázis tárolói alá tartozó elemekhez egy paraméteres értékeket tartalmazó SQL-utasítás használatával. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) – törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync`a metódus csak az adatbázist törli. A `Cosmosclient` példány ártalmatlanításának külön kell történnie (amely a DeleteDatabaseAndCleanupAsync metódusban található). 

 ## <a id="code-examples"></a>Példák a kódokra

A cikkben ismertetett mintakód egy családi adatbázist hoz létre Azure Cosmos DBban. A család adatbázisa olyan családi adatokat tartalmaz, mint például a név, a lakcím, a hely, a társított szülők, a gyermekek és a háziállatok. Mielőtt feltölti az adatokat az Azure Cosmos-fiókjába, definiálja egy családi elem tulajdonságait. Hozzon létre egy új `Family.cs` , nevű osztályt a minta-alkalmazás legfelső szintjén, és adja hozzá a következő kódot:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a using direktíva & a Client objektum megadása

A projekt könyvtárában nyissa meg `Program.cs` a fájlt a szerkesztőben, és adja hozzá a következő használati irányelveket az alkalmazás tetején:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

A `program.cs file`alkalmazáshoz adja hozzá a kódot az előző lépésben beállított környezeti változók olvasásához. Adja meg `CosmosClient`a `Database`, és az `Container` objektumokat. Ezután adjon hozzá egy kódot a Main metódushoz, `GetStartedDemoAsync` amely meghívja a metódust, amelyben az Azure Cosmos-fiók erőforrásait kezeli. 

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

Adja meg `CreateDatabaseAsync` a metódust `program.cs` a osztályon belül. Ez a metódus hozza `FamilyDatabase` létre a ha még nem létezik. 

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tároló létrehozása

Adja meg `CreateContainerAsync` a metódust `program.cs` a osztályon belül. Ez a metódus hozza `FamilyContainer` létre a ha még nem létezik. 

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

### <a name="create-an-item"></a>Elemek létrehozása

Hozzon létre egy családi tételt a `AddItemsToContainerAsync` metódus hozzáadásával a következő kóddal:

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
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Elemek lekérdezése

Egy elem beszúrása után futtathat egy lekérdezést, amely az "Andersen" család részleteit kéri le. A következő kód bemutatja, hogyan hajthatja végre a lekérdezést közvetlenül az SQL-lekérdezés használatával. Az "Anderson" család részleteit lekérdező SQL-lekérdezés `SELECT * FROM c WHERE c.LastName = 'Andersen'`a következő:. Adja meg a `program.cs` metódust a osztályon belül, és adja hozzá a következő kódot: `QueryItemsAsync`


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

Végül törölheti az adatbázist a `DeleteDatabaseAndCleanupAsync` metódus hozzáadásával a következő kóddal:

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

### <a name="execute-the-crud-operations"></a>A szifilisz-műveletek végrehajtása

Miután meghatározta az összes szükséges metódust, hajtsa végre őket a `GetStartedDemoAsync` metódusban. Ez `DeleteDatabaseAndCleanupAsync` a kód nem jelenik meg a kódban, mert a metódus végrehajtásakor nem jelennek meg erőforrások. A Azure Cosmos DB-erőforrások a Azure Portalban való létrehozása után törölheti a megjegyzéseit. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
    await this.ReplaceFamilyItemAsync();
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
}
```

Miután hozzáadta az összes szükséges metódust, mentse `Program.cs` a fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Ezután hozza létre és futtassa az alkalmazást a Azure Cosmos DB erőforrások létrehozásához. Győződjön meg arról, hogy új parancssorablakot nyit meg, ne használja ugyanazt a példányt, amelyet a környezeti változók beállításához használt. Mivel a környezeti változók nincsenek beállítva az aktuális megnyitott ablakban. A frissítések megtekintéséhez meg kell nyitnia egy új parancssort. 

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás futtatásakor a következő kimenet jön létre. A Azure Portalba is bejelentkezhet, és ellenőrizheti, hogy az erőforrások létrejöttek-e:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Az adatok létrejöttének ellenőrzéséhez jelentkezzen be a Azure Portalba, és tekintse meg az Azure Cosmos-fiókban szükséges elemeket. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell az Azure Cosmos-fiók és a hozzá tartozó erőforráscsoport eltávolításához. Az alábbi parancs bemutatja, hogyan törölheti az erőforráscsoportot az Azure CLI használatával:

```azurecli
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre egy adatbázist és egy tárolót egy .NET Core-alkalmazás használatával. Mostantól a következő cikk utasításait követve importálhat további információkat az Azure Cosmos-fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
