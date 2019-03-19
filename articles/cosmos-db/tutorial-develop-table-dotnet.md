---
title: Ismerkedés az Azure Cosmos DB Table API .NET Standard SDK használatával
description: Store strukturált adatok a felhőben az Azure Cosmos DB Table API használatával.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 03/11/2019
ms.openlocfilehash: 0f324d39db38b17d436583277d60d87b2878d131
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880789"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>A .NET SDK használatával az Azure Cosmos DB Table API és az Azure Table storage használatának első lépései

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Használhatja az Azure Cosmos DB Table API vagy az Azure Table storage strukturált nosql-alapú kevésbé tervezési sémával tárolja biztosító kulcs-/ attribútumtárat, felhőbeli adatok tárolására. Mivel az Azure Cosmos DB Table API- és Table storage séma kevesebb, könnyebbé vált az adatok alkalmazkodni az alkalmazás változásával igényeinek. Az Azure Cosmos DB Table API vagy a Table storage segítségével rugalmas adatkészleteket, például webalkalmazások, címjegyzékek, eszközadatok vagy más típusú metaadatokat a szolgáltatásnak szüksége van a felhasználói adatok tárolására. 

Ez az oktatóanyag leírja egy mintát, amely bemutatja, hogyan használható a [Microsoft Azure Cosmos DB tábla Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) Azure Cosmo DB Table API és az Azure Table storage-forgatókönyvekhez. A kapcsolat az Azure-szolgáltatás adott kell használnia. Ezek a forgatókönyvek teljesítménykezelési használatával C# példa, amelyek bemutatják, hogyan hozhat létre táblákat, beszúrása / adatok frissítése, adatok lekérdezése és a táblák törlése.

## <a name="prerequisites"></a>Előfeltételek

A minta sikeres teljesítéséhez a következőkre lesz szüksége:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [A Microsoft Azure cosmos DB tábla könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – Ez a tár .NET Standard és a .NET-keretrendszer jelenleg érhető el. 

* [Azure Cosmos DB Table API-fiók](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Hozzon létre egy .NET-konzol projektet

A Visual Studióban hozzon létre egy új .NET-konzolalkalmazást. A következő lépések azt mutatják be, hogyan hozhat létre konzolalkalmazást a Visual Studio 2017-ben. A lépések a Visual Studio más verziói esetén is hasonlók. Az Azure Cosmos DB tábla kódtárat bármilyen típusú .NET-alkalmazást, beleértve egy Azure-felhő vagy -webappokat alkalmazást, és az asztali és mobil alkalmazások is használhatja. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.

1. Válassza ki **telepített** > **Visual C#**   >  **Console App (.NET Core)**.

1. Az a **neve** mezőben adjon meg egy nevet az alkalmazásnak, például **CosmosTableSamples** (megadhat egy másik nevet igény szerint).

1. Kattintson az **OK** gombra.

A példában szereplő példák is hozzáadhatók a Konzolalkalmazás a Main() módjának **Program.cs** fájlt.

## <a name="install-the-required-nuget-package"></a>Telepítse a szükséges NuGet-csomagot

A NuGet-csomag beszerzéséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.

1. Keressen rá az interneten a `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` válassza **telepítése** telepítése a Microsoft Azure Cosmos DB tábla könyvtárban.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **kapcsolati karakterlánc**. Az ablak jobb oldalán található Másolás gomb használatával másolja a vágólapra a **PRIMARY CONNECTION STRING** (Elsődleges kapcsolati sztring) értékét.

   ![Tekintse meg és másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán.](./media/create-table-dotnet/connection-string.png)
   
1. A kapcsolati karakterlánc konfigurálása a visual studióban kattintson a jobb gombbal a projekt **CosmosTableSamples**.

1. Válassza ki **hozzáadása** , majd **új elem**. Hozzon létre egy új fájlt **Settings.json** fájl típusú **TypeScript JSON konfigurációs** fájlt. 

1. A kód a Settings.json fájlban cserélje le a következő kódot, és rendelje hozzá az elsődleges kapcsolati karakterlánc:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kattintson jobb gombbal a projektre **CosmosTableSamples**. Válassza ki **Hozzáadás**, **új elem** és hozzá kell adni egy osztályt nevű **AppSettings.cs**.

1. Adja hozzá a következő kódot a AppSettings.cs fájlt. Ez a fájl beolvassa a kapcsolati karakterláncot a Settings.json fájlból, és hozzárendeli azt a konfigurációs paraméter:

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

## <a name="parse-and-validate-the-connection-details"></a>Elemezheti, és ellenőrizze a kapcsolat adatai 

1. Kattintson jobb gombbal a projektre **CosmosTableSamples**. Válassza ki **Hozzáadás**, **új elem** és hozzá kell adni egy osztályt nevű **Common.cs**. A kapcsolati adatok érvényesítéséhez, és hozzon létre egy táblát az osztályon belül kódot ír majd.

1. Adja meg egy metódust `CreateStorageAccountFromConnectionString` alább látható módon. Ez a módszer lesz elemezni a kapcsolati karakterlánc adatait, és ellenőrizze, hogy a fiók neve és a legfontosabb adatokat adott meg a "Settings.json" fájlban érvényesek. 

   ```csharp
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
   ```


## <a name="create-a-table"></a>Hozzon létre egy táblát 

A [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.cloudtableclient?redirectedfrom=MSDN&view=azure-dotnet) osztály segítségével lekérheti a Table Storage-ban tárolt táblákat és entitásokat. Vegyünk fel a Cosmos DB Table API-fiókban, hogy nem rendelkezik táblákkal, mert a `CreateTableAsync` metódust a **Common.cs** osztály tábla létrehozásához:

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

## <a name="define-the-entity"></a>Az entitás megadása 

Entitások leképezése C# osztályból származtatott egyéni osztály használatával objektumok [TableEntity](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx). Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait.

Kattintson jobb gombbal a projektre **CosmosTableSamples**. Válassza ki **Hozzáadás**, **új mappa** és adja neki **modell**. A modell mappán belül adjon hozzá egy osztályt **CustimerEntity.cs** , és adja hozzá a következő kód azt.

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

Ez a kód meghatároz egy entitásosztályt, amely az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy adott entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblában. Az azonos partíciókulcsú entitások entitások gyorsabban lekérdezhetők, az eltérő partíciókulcsok, de partíciókulcsúak használata a párhuzamos műveletek nagyobb méretezhetőségét teszi lehetővé. Táblák entitásoknak támogatott típusúnak, például származó kell lennie a [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableentity?redirectedfrom=MSDN&view=azure-dotnet) osztály. A táblában tárolni kívánt entitástulajdonságoknak publikusnak kell lenniük, és támogatniuk kell az értékek beolvasását és beállítását is. Az entitástípusnak emellett közzé kell tennie egy paraméter nélküli konstruktor.

## <a name="insert-or-merge-an-entity"></a>Helyezze be vagy egy entitás egyesítése

Az alábbi példakód létrehoz egy entitás objektumot, és hozzáadja azt a táblába. A InsertOrMerge metódus belül a [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableoperation?redirectedfrom=MSDN&view=azure-dotnet) osztály beszúrása vagy egy entitás egyesítése szolgál. A [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.cloudtable.executeasync?view=azure-dotnet) módszert hívja meg a művelet végrehajtásához. 

Kattintson jobb gombbal a projektre **CosmosTableSamples**. Válassza ki **Hozzáadás**, **új elem** és hozzá kell adni egy osztályt nevű **SamplesUtils.cs**. Ez az osztály az entitások CRUD-műveletek végrehajtásához szükséges összes kódot tárolja. 

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
        
        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
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

### <a name="get-an-entity-from-a-partition"></a>Egy partíció egy entitás beolvasása

A csoportban lekérése módszer használatával egy partícióról entitás is kap a [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableoperation?redirectedfrom=MSDN&view=azure-dotnet) osztály. A következő mintakód beolvassa a kulcssor partíciókulcsot, egy ügyfélentitást e-mail vagy telefon száma. Ebben a példában is kiírja a lekérdezéshez az entitás felhasznált kérelemegységek. Egy entitás lekérdezéséhez, fűzze hozzá a következő kódot a **SamplesUtils.cs** fájlt: 

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

A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást. Entitás törlése, fűzze hozzá a következő kódot a **SamplesUtils.cs** fájlt: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>A mintaadatok CRUD-műveletek végrehajtása

Tábla, insert nebo egyesítési entitások létrehozásának, meghatározása után ezek a metódusok futtassa a mintaadatokat. Ehhez kattintson a jobb gombbal a projekt **CosmosTableSamples**. Válassza ki **Hozzáadás**, **új elem** és hozzá kell adni egy osztályt nevű **BasicSamples.cs** , és adja hozzá a következő kód azt. Ez a kód egy táblát hoz létre, azt hozzáadja az entitásokat. Ha törli az entitás- és a végén a projekt táblázatban távolítsa el a megjegyzések `table.DeleteIfExistsAsync()` és `SamplesUtils.DeleteEntityAsync(table, customer)` metódusokat a következő kódot:

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

Az előző kód "bemutató" kezdetű táblázatot hoz létre, és a létrehozott globálisan egyedi Azonosítót a rendszer hozzáfűzi a tábla neve. Ez hozzáad egy ügyfélentitást első és utolsó "Harp Walter" nevű, és később frissíti a felhasználó telefonszámát. 

Ebben az oktatóanyagban létrehozott kódot a Table API-fiókban tárolt adatok alapszintű CRUD-műveletek végrehajtásához. Például a speciális műveleteket is elvégezheti, – batch beszúrása az adatok, a lekérdezés egy partíción belül az adatok lekérdezése az adatok egy partíción belül, a fiók a megadott előtaggal kezdődő listák táblák széles. Letöltheti a teljes minta képernyő [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub-adattárban. A [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) osztály rendelkezik további műveleteket hajthat végre az adatokon.  

## <a name="run-the-project"></a>A projekt futtatása

Most állítsa össze a megoldást, és nyomja le az F5 billentyűt a projekt futtatásához. A projekt futtatásakor a parancssorban a következő kimenet jelenik meg:

![Kimeneti parancssor használatával](./media/tutorial-develop-table-standard/output-from-sample.png)

Ha hibaüzenet arról, hogy a Settings.json fájl nem található, ha futtatja a projektet, megoldhatja a Projektbeállítások között a következő XML-bejegyzés hozzáadásával. A jobb gombbal a CosmosTableSamples, CosmosTableSamples.csproj szerkesztéséhez válassza ki, és adja hozzá a következő itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Most jelentkezzen be az Azure Portalra, és győződjön meg arról, hogy az adatok a tábla létezik-e. 

![Eredmények a portál](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>További lépések

Most továbbléphet a következő oktatóanyaggal, és ismerje meg, hogyan telepítheti át adatait az Azure Cosmos DB Table API-fiókhoz. 

> [!div class="nextstepaction"]
>[Adatok lekérdezése](../cosmos-db/table-import.md)
