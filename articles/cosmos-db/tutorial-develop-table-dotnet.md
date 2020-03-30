---
title: Azure Cosmos DB Table API a .NET standard SDK használatával
description: Ismerje meg, hogyan tárolhatja és kérdezheti le a strukturált adatokat az Azure Cosmos DB Table API-fiókban
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238453"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Bevezetés az Azure Cosmos DB Table API és az Azure Table Storage a .NET SDK-val való használatába

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Az Azure Cosmos DB Table API vagy az Azure Table storage használatával strukturált NoSQL-adatokat tárolhat a felhőben, így egy kulcs-/attribútumtárolót biztosít egy kisebb tervvel rendelkező sémával. Mivel az Azure Cosmos DB Table API és a Table storage séma kevesebb, könnyen adaptálhatja az adatokat az alkalmazás igényeinek előrehaladtával. Az Azure Cosmos DB Table API-t vagy a Table storage-ot rugalmas adatkészletek, például webes alkalmazások felhasználói adatainak, címjegyzékek, eszközadatok vagy a szolgáltatás által igényelt más típusú metaadatok tárolására használhatja. 

Ez az oktatóanyag egy minta, amely bemutatja, hogyan használhatja a [Microsoft Azure Cosmos DB Table Library library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) az Azure Cosmos DB Table API és az Azure Table storage forgatókönyvek. Az Azure-szolgáltatásra jellemző kapcsolatot kell használnia. Ezeket a forgatókönyveket C# példák segítségével vizsgálja meg, amelyek bemutatják a táblák létrehozását, az adatok beszúrását/ frissítését, az adatok lekérdezését és a táblák törlését.

## <a name="prerequisites"></a>Előfeltételek

A minta sikeres teljesítéséhez a következőkre lesz szüksége:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB table library for .](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) 

* [Az Azure Cosmos DB Table API-fiókja.](create-table-dotnet.md#create-a-database-account)

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET konzolprojekt létrehozása

A Visual Studio alkalmazásban hozzon létre egy új .NET konzolalkalmazást. Az alábbi lépések bemutatják, hogyan hozhat létre konzolalkalmazást a Visual Studio 2019-ben. Az Azure Cosmos DB Table Library bármilyen típusú .NET alkalmazásban használható, beleértve az Azure felhőszolgáltatást vagy webalkalmazást, valamint az asztali és mobilalkalmazásokat. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

1. Válassza **az** > **Új** > **fájl projekt lehetőséget.**

1. Válassza **a Console App (.NET Core)** lehetőséget, majd a **Tovább**gombot.

1. A **Projekt neve** mezőben adja meg az alkalmazás nevét, például **cosmosTableSamples**. (Szükség szerint más nevet is megadhat.)

1. Kattintson a **Létrehozás** gombra.

A mintában szereplő összes kódpélda hozzáadható a konzolalkalmazás **Program.cs** fájljának Main() metódusához.

## <a name="install-the-required-nuget-package"></a>A szükséges NuGet csomag telepítése

A NuGet csomag beszerzéséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.

1. Keressen az [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)interneten [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) , a , [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)majd a Telepítés **gombra** a Microsoft Azure Cosmos DB Table Library telepítéséhez.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg az Azure Cosmos-fiókot vagy a Table Storage-fiókot. 

1. Nyissa meg a **Kapcsolati karakterlánc** vagy **az Access billentyűk** ablaktáblát. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   ![Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.](./media/create-table-dotnet/connection-string.png)
   
1. A kapcsolati karakterlánc konfigurálásához a Visual Studio jobb gombbal a projekt **CosmosTableSamples**.

1. Válassza **a Hozzáadás,** majd **az Új elem lehetőséget.** Hozzon létre egy új **fájlt Settings.json** fájltípussal **TypeScript JSON konfigurációs** fájl. 

1. Cserélje le a Settings.json fájlban lévő kódot a következő kódra, és rendelje hozzá az elsődleges kapcsolati karakterláncot:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza **a Hozzáadás**, **Új elem** lehetőséget, és vegyen fel egy **AppSettings.cs**nevű osztályt.

1. Adja hozzá a következő kódot a AppSettings.cs fájlhoz. Ez a fájl beolvassa a kapcsolati karakterláncot a Settings.json fájlból, és hozzárendeli a konfigurációs paraméterhez:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>A kapcsolat részleteinek elemzése és ellenőrzése 

1. Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza **a Hozzáadás**, **Új elem** lehetőséget, és vegyen fel egy **Common.cs**nevű osztályt. Kódot fog írni a kapcsolat részleteinek ellenőrzéséhez, és hozzon létre egy táblát ebben az osztályban.

1. Definiáljon `CreateStorageAccountFromConnectionString` egy módszert az alábbiak szerint. Ez a módszer elemzi a kapcsolati karakterlánc adatait, és ellenőrzi, hogy a "Settings.json" fájlban megadott fióknév és fiókkulcs adatai érvényesek-e. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Táblázat létrehozása 

A [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) osztály segítségével lekérheti a Table Storage-ban tárolt táblákat és entitásokat. Mivel a Cosmos DB Table API-fiókban nincsenek táblák, `CreateTableAsync` adjuk hozzá a metódust a **Common.cs** osztályhoz egy tábla létrehozásához:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

Ha "503-as szolgáltatás nem érhető el kivétel" hibaüzenet jelenik meg, lehetséges, hogy a kapcsolódási módhoz szükséges portokat tűzfal blokkolja. A probléma megoldásához nyissa meg a szükséges portokat, vagy használja az átjáró módú kapcsolatot az alábbi kód szerint:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Az entitás meghatározása 

Az entitások a [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)objektumból származtatott egyéni osztály használatával lesznek Leképezve C# objektumokra. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait.

Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza **a Hozzáadás**lehetőséget, **az Új mappa** lehetőséget, és nevezze el **modellként.** A Modell mappában vegyen fel egy CustomerEntity.cs nevű **osztályt,** és adja hozzá a következő kódot.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Ez a kód egy entitásosztályt határoz meg, amely az ügyfél utónevét használja sorkulcsként, a vezetéknevet pedig partíciókulcsként. Egy adott entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblában. Az azonos partíciós kulccsal rendelkező entitások gyorsabban lekérdezhetők, mint a különböző partíciós kulcsokkal rendelkező entitások, de a különböző partíciókulcsok használata lehetővé teszi a párhuzamos műveletek nagyobb méretezhetőségét. A táblákban tárolni kívánt entitásoknak támogatott típusúnak, például a [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) osztályból származtatottnak kell lenniük. A táblában tárolni kívánt entitástulajdonságoknak publikusnak kell lenniük, és támogatniuk kell az értékek beolvasását és beállítását is. Az entitástípusnak emellett elérhetővé kell tennie egy paraméter nélküli konstruktort is.

## <a name="insert-or-merge-an-entity"></a>Entitás beszúrása vagy egyesítése

A következő kódpélda létrehoz egy entitásobjektumot, és hozzáadja azt a táblához. A [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) osztályon belüli InsertOrMerge metódus egyentitás beszúrására vagy egyesítésére szolgál. A [cloudtable.executeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) metódus t a művelet végrehajtásához hívják meg. 

Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza **a Hozzáadás**, **Új elem** lehetőséget, és vegyen fel egy **SamplesUtils.cs**nevű osztályt. Ez az osztály tárolja az összes szükséges kódot a CRUD műveletek az entitásokon. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Entitás leelsőjéről partícióról

Entitást egy partícióról a [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) osztály Beolvasás metódusával kaphat be. A következő kód példa leveszi a partíciókulcs sorkulcsát, e-mail címét és telefonszámát egy ügyfél entitás. Ebben a példában az entitás lekérdezéséhez felhasznált kérelemegységek et is kinyomtatja. Entitás lekérdezéséhez fűzze hozzá a következő kódot **SamplesUtils.cs** fájlhoz: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Entitás törlése

A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást. Entitás törléséhez fűzze hozzá a következő kódot **SamplesUtils.cs** fájlhoz: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>A CRUD-műveletek végrehajtása mintaadatokon

Miután definiálta a tábla létrehozásának, beszúrásának vagy egyesítésének módszereit, futtassa ezeket a módszereket a mintaadatokon. Ehhez kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza **a Hozzáadás**, **Az Új elem** lehetőséget, és vegyen fel egy BasicSamples.cs nevű osztályt, és adja hozzá a következő kódot. **BasicSamples.cs** Ez a kód létrehoz egy táblát, entitásokat ad hozzá. Ha törölni szeretné az entitást és a táblázatot a `table.DeleteIfExistsAsync()` `SamplesUtils.DeleteEntityAsync(table, customer)` projekt végén, távolítsa el a megjegyzéseket és a módszereket a következő kódból:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Az előző kód létrehoz egy táblát, amely "demo" kezdetű, és a létrehozott GUID hozzáfűzi a tábla nevét. Ezután hozzáad egy "Harp Walter" vezeték- és vezetéknevű ügyfélentitást, majd később frissíti a felhasználó telefonszámát. 

Ebben az oktatóanyagban a Table API-fiókban tárolt adatok alapvető CRUD-műveletek végrehajtásához készített kódot. Speciális műveleteket is végrehajthat, például – kötegelt adatok beszúrása, a partíción belüli összes adat lekérdezése, egy partíción belüli adattartomány lekérdezése, A fiók azon tábláinak listázása, amelyek neve a megadott előtaggal kezdődik. Letöltheti a teljes mintaűrlapot [az azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub repository. A [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) osztály több műveletet, hogy végre lehet hajtani az adatokat.  

## <a name="run-the-project"></a>A projekt futtatása

A **projektből CosmosTableSamples**. Nyissa meg a **Program.cs** nevű osztályt, és adja hozzá a következő kódot a BasicSamples hívásához a projekt futtatásakor.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Most épít a oldat és sajtó F5 -hoz fuss a tervez. A projekt futtatásakor a parancssorban a következő kimenet jelenik meg:

![Kimenet a parancssorból](./media/tutorial-develop-table-standard/output-from-sample.png)

Ha olyan hibaüzenetet kap, amely szerint a Settings.json fájl nem található a projekt futtatásakor, a projekt beállításaihoz a következő XML-bejegyzés hozzáadásával oldhatja meg. Kattintson a jobb gombbal cosmosTableSamples, válassza Edit CosmosTableSamples.csproj és adjuk hozzá a következő itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Most már bejelentkezhet az Azure Portalon, és ellenőrizheti, hogy az adatok léteznek-e a táblában. 

![Eredmények a portálon](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>További lépések

Most folytathatja a következő oktatóanyag, és megtudhatja, hogyan telepítheti át az adatokat az Azure Cosmos DB Table API-fiókba. 

> [!div class="nextstepaction"]
>[Adatok lekérdezése](../cosmos-db/table-import.md)
