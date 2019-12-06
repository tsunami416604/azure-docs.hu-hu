---
title: A Azure Cosmos DB Table API használatának első lépései a .NET Standard SDK-val
description: Megtudhatja, hogyan tárolhatja és kérdezheti le a strukturált információkat Azure Cosmos DB Table API-fiókban
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: 0a1ba00d2d24664590f76438a90f651e0826d419
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870547"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Az Azure Cosmos DB Table API és az Azure Table Storage használatának első lépései a .NET SDK-val

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

A Azure Cosmos DB Table API vagy az Azure Table Storage használatával strukturált NoSQL-adatmennyiségeket tárolhat a felhőben, így egy kulcs-/attribútum-tárolót biztosít a séma kisebb kialakításával. Mivel Azure Cosmos DB Table API és a Table Storage kevesebb séma, egyszerűen alkalmazkodhat az adataihoz az alkalmazás igényeinek megfelelően. Az Azure Cosmos DB Table API vagy a Table Storage használatával rugalmas adatkészleteket tárolhat, például a webalkalmazások felhasználói adatait, a címjegyzékeket, az eszköz adatait vagy a szolgáltatás által igényelt más típusú metaadatokat. 

Ez az oktatóanyag egy példát mutat be, amely bemutatja, hogyan használható a [.net-hez készült Microsoft Azure Cosmos db Table Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) a Azure Cosmos db Table API és az Azure Table Storage forgatókönyvekkel. Az Azure-szolgáltatáshoz tartozó kapcsolódást kell használnia. Ezek a forgatókönyvek olyan példákat mutatnak be, amelyek bemutatják, hogyan lehet táblákat létrehozni, adatok beszúrására/frissítésére, adatok lekérdezésére és a táblák törlésére használni C# .

## <a name="prerequisites"></a>Előfeltételek

A minta sikeres teljesítéséhez a következőkre lesz szüksége:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB a .net-hez](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – ez a tár jelenleg a .NET Standard és a .NET-keretrendszer számára érhető el. 

* [Azure Cosmos DB Table API fiók](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET-konzol projekt létrehozása

Hozzon létre egy új .NET-konzol alkalmazást a Visual Studióban. A következő lépések bemutatják, hogyan hozhat létre egy Console-alkalmazást a Visual Studio 2019-ben. A Azure Cosmos DB Table Library bármilyen típusú .NET-alkalmazásban használható, beleértve az Azure Cloud Service-t vagy a webalkalmazást, valamint az asztali és mobil alkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.

1. Válassza a **Console app (.net Core)** lehetőséget, majd kattintson a **tovább**gombra.

1. A **projekt neve** mezőben adja meg az alkalmazás nevét, például **CosmosTableSamples**. (Szükség szerint más nevet is megadhat.)

1. Kattintson a **Létrehozás** gombra.

Az ebben a mintában szereplő összes példa felvehető a konzol alkalmazás **program.cs** fájljának Main () metódusára.

## <a name="install-the-required-nuget-package"></a>A szükséges NuGet-csomag telepítése

A NuGet csomag beszerzéséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.

1. Keressen rá az interneten a `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` lehetőségre, és válassza a **telepítés** lehetőséget a Microsoft Azure Cosmos db Table Library telepítéséhez.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

1. A [Azure Portal](https://portal.azure.com/)navigáljon az Azure Cosmos-fiókjához vagy a Table Storage-fiókhoz. 

1. Nyissa meg a **kapcsolati karakterlánc** vagy a **hozzáférési kulcsok** panelt. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   ![Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.](./media/create-table-dotnet/connection-string.png)
   
1. A kapcsolódási karakterlánc konfigurálásához a Visual studióból kattintson a jobb gombbal a projekt **CosmosTableSamples**.

1. Válassza a **Hozzáadás** , majd az **új elem**lehetőséget. Hozzon létre egy új file **Settings. JSON** fájlt, amely az **írógéppel JSON konfigurációs** fájl fájltípusa. 

1. Cserélje le a Code in Settings. JSON fájlt a következő kódra, és rendelje hozzá az elsődleges kapcsolatok karakterláncát:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem elemet** , és adjon hozzá egy **appSettings.cs**nevű osztályt.

1. Adja hozzá a következő kódot a AppSettings.cs fájlhoz. Ez a fájl a Settings. JSON fájlból olvassa be a kapcsolódási karakterláncot, és hozzárendeli azt a konfigurációs paraméterhez:

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

1. Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem elemet** , és adjon hozzá egy **Common.cs**nevű osztályt. A kapcsolat részleteinek érvényesítéséhez és az ebben az osztályban található tábla létrehozásához kódot kell írnia.

1. Definiáljon egy metódust `CreateStorageAccountFromConnectionString` az alább látható módon. Ez a metódus elemzi a kapcsolati sztring részleteit, és ellenőrzi, hogy érvényes-e a "Settings. JSON" fájlban megadott fiók neve és a fiók kulcsa. 

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


## <a name="create-a-table"></a>Tábla létrehozása 

A [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) osztály segítségével lekérheti a Table Storage-ban tárolt táblákat és entitásokat. Mivel nem található táblázat a Cosmos DB Table API-fiókban, vegyük fel a `CreateTableAsync` metódust a **Common.cs** osztályba egy tábla létrehozásához:

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

## <a name="define-the-entity"></a>Az entitás definiálása 

Az entitások C# az objektumokhoz egy [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)származtatott egyéni osztály használatával képezhetők le. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait.

Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új mappa** lehetőséget, és nevezze el **modellként**. A Model mappában adjon hozzá egy **CustomerEntity.cs** nevű osztályt, és adja hozzá a következő kódot.

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

Ez a kód olyan Entity osztályt határoz meg, amely az ügyfél utónevét használja, és a vezetéknevet adja meg a partíció kulcsaként. Egy adott entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblában. Az ugyanazzal a partíciós kulccsal rendelkező entitások gyorsabban lekérdezhető, mint a különböző partíciós kulcsokkal rendelkező entitások, de a különféle partíciós kulcsok használata lehetővé teszi a párhuzamos műveletek nagyobb méretezhetőségét. A táblákban tárolandó entitásoknak támogatott típusúnak kell lenniük, például a [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) osztályból származtatva. A táblában tárolni kívánt entitástulajdonságoknak publikusnak kell lenniük, és támogatniuk kell az értékek beolvasását és beállítását is. Emellett az entitás típusának ki kell jelölnie egy paraméter nélküli konstruktort.

## <a name="insert-or-merge-an-entity"></a>Entitás beszúrása vagy egyesítése

A következő mintakód létrehoz egy entitás objektumot, és hozzáadja azt a táblához. Az entitások beszúrásához vagy egyesítéséhez a [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) osztályon belüli InsertOrMerge metódus használható. A művelet végrehajtásához a [CloudTable. ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) metódust kell meghívni. 

Kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem elemet** , és adjon hozzá egy **SamplesUtils.cs**nevű osztályt. Ez az osztály tárolja az entitásokon a SZIFILISZi műveletek végrehajtásához szükséges összes kódot. 

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

### <a name="get-an-entity-from-a-partition"></a>Entitás beolvasása egy partícióból

Az entitásokat a [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) osztály lekérési metódusának használatával szerezheti be a partícióból. A következő kódrészlet a partíciós kulcs sorát, e-mail-címét és telefonszámát kéri le. Ez a példa az entitás lekérdezéséhez felhasznált kérelmek egységeit is kiírja. Az entitások lekérdezéséhez fűzze hozzá a következő kódot a **SamplesUtils.cs** fájlhoz: 

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

A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást. Entitás törléséhez fűzze hozzá a következő kódot a **SamplesUtils.cs** fájlhoz: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>A szifilisz-műveletek végrehajtása a mintaadatok alapján

Miután meghatározta a tábla létrehozásához, az entitások beszúrásához vagy egyesítéséhez szükséges metódusokat, futtassa ezeket a metódusokat a mintaadatok alapján. Ehhez kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza a **Hozzáadás**, **új elem** lehetőséget, és vegyen fel egy **BasicSamples.cs** nevű osztályt, és adja hozzá a következő kódot. Ez a kód létrehoz egy táblát, entitásokat hoz létre hozzá. Ha törölni szeretné az entitást és a táblát a projekt végén, távolítsa el `table.DeleteIfExistsAsync()` és `SamplesUtils.DeleteEntityAsync(table, customer)` metódusokból származó megjegyzéseket a következő kódból:

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

Az előző kód létrehoz egy táblázatot, amely a "demo" kezdetű, a generált GUID pedig a táblázat nevéhez lesz hozzáfűzve. Ezután hozzáadja a "hárfa Walter" nevű ügyfél-entitást és a vezetéknevét, és később frissíti a felhasználó telefonszámát. 

Ebben az oktatóanyagban a Table API fiókban tárolt adatok alapszintű szifilisz-műveleteinek elvégzésére szolgáló kódot készített. Olyan speciális műveleteket is végrehajthat, mint például a – Batch adatok beszúrása, a partíción belüli összes adat lekérdezése, a partíción belüli adatok lekérdezése, a fiók azon tábláinak felsorolása, amelyek neve a megadott előtaggal kezdődik. Letöltheti a teljes minta űrlapot az [Azure-Cosmos-Table-DotNet-Core-Getting-Started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub-tárházban. A [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) osztály több műveletet is végrehajthat az adatokon.  

## <a name="run-the-project"></a>A projekt futtatása

A projekt **CosmosTableSamples**. Nyissa meg a **program.cs** nevű osztályt, és adja hozzá a következő kódot a BasicSamples a projekt futtatásakor történő meghívásához.

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

Most hozza létre a megoldást, és nyomja le az F5 billentyűt a projekt futtatásához. A projekt futtatásakor a következő kimenet jelenik meg a parancssorban:

![Kimenet a parancssorból](./media/tutorial-develop-table-standard/output-from-sample.png)

Ha olyan hibaüzenetet kap, amely szerint a Project futtatásakor nem található a Settings. JSON fájl, a következő XML-bejegyzést adja hozzá a projekt beállításaihoz. Kattintson a jobb gombbal a CosmosTableSamples elemre, válassza a CosmosTableSamples. csproj szerkesztése lehetőséget, és adja hozzá a következő itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Most jelentkezzen be a Azure Portalba, és ellenőrizze, hogy az adatkészletek szerepelnek-e a táblában. 

![Eredmények a portálon](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Következő lépések

Most folytassa a következő oktatóanyaggal, és megtudhatja, hogyan telepítheti át az információkat Azure Cosmos DB Table API-fiókba. 

> [!div class="nextstepaction"]
>[Az adatlekérdezés](../cosmos-db/table-import.md)
