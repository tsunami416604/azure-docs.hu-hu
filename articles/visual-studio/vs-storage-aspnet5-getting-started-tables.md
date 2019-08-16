---
title: Ismerkedés a Table Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core) | Microsoft Docs
description: Az Azure Table Storage használatának első lépései egy ASP.NET Core-projektben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 7ac610e96d84568b7973f288623730ea1677ceac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510919"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Ismerkedés az Azure Table Storage és a Visual Studio csatlakoztatott szolgáltatásaival

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Table Storage használatát a Visual Studióban, miután létrehozta vagy hivatkozott egy Azure Storage-fiókot egy ASP.NET Core-projektben a Visual Studio **csatlakoztatott szolgáltatások** funkciójának használatával. A **csatlakoztatott szolgáltatások** művelet telepíti a megfelelő NuGet-csomagokat az Azure Storage-ban a projektben, és hozzáadja a Storage-fiókhoz tartozó kapcsolati karakterláncot a projekt konfigurációs fájljaihoz. (Lásd az Azure Storage szolgáltatással kapcsolatos általános információk [tárolási dokumentációját](https://azure.microsoft.com/documentation/services/storage/) .)

Az Azure Table Storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adattárolás tárolását. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőn belüli és kívüli hitelesített hívásokat fogadja el. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. Az Azure Table Storage használatával kapcsolatos általános információkért lásd: [Az Azure Table Storage használatának első lépései a .NET használatával](../storage/storage-dotnet-how-to-use-tables.md).

Első lépésként hozzon létre egy táblázatot a Storage-fiókban. Ez a cikk bemutatja, hogyan hozhat létre táblát a C# alkalmazásban, és hogyan hajthat végre olyan alapszintű táblázatos műveleteket, mint például a táblamezők hozzáadása, módosítása, olvasása és eltávolítása.  A kód a .NET-hez készült Azure Storage ügyféloldali kódtárat használja. További információ a ASP.NET: [ASP.net](https://www.asp.net).

Egyes Azure Storage API-k aszinkron módon működnek, és a cikkben szereplő kód feltételezi az aszinkron metódusok használatát. További információ: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-tables-in-code"></a>Hozzáférési táblázatok a kódban

ASP.NET Core projektek tábláihoz való hozzáféréshez az alábbi elemeket is fel kell vennie az C# Azure Table Storage-hoz hozzáférő forrásfájlokba.

1. Adja hozzá a `using` szükséges utasításokat:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Szerezzen `CloudStorageAccount` be egy objektumot, amely a Storage-fiók adatait jelöli. Használja a következő kódot a Storage-fiókja és a fiók kulcsa alapján, amelyet a appSettings. JSON fájl tárolási kapcsolatainak karakterláncában talál:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Egy `CloudTableClient` objektum lekérése a Storage-fiókban lévő táblázatos objektumokra való hivatkozáshoz:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Egy olyan `CloudTable` hivatkozási objektum beszerzése, amely egy adott táblára és entitásokra hivatkozik:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tábla létrehozása kódban

Az Azure-tábla létrehozásához hozzon létre egy aszinkron metódust, `CreateIfNotExistsAsync()`és kattintson a következőre:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Az entitások táblához való hozzáadásához létre kell hoznia egy olyan osztályt, amely meghatározza az entitás tulajdonságait. A következő kód egy nevű `CustomerEntity` Entity osztályt határoz meg, amely az ügyfél utónevét és vezetéknevét használja a partíciós kulcsként.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Az entitásokat tartalmazó táblák műveletei `CloudTable` a korábban létrehozott objektumot használják a [kódban a hozzáférési táblákban](#access-tables-in-code). Az `TableOperation` objektum a végrehajtani kívánt műveletet jelöli. A következő mintakód bemutatja, hogyan hozhat létre egy `CloudTable` objektumot és egy `CustomerEntity` objektumot. A művelet előkészítéséhez létrejön egy `TableOperation` , az ügyfél entitásnak a táblába való beszúrásához. Végül meghívja `CloudTable.ExecuteAsync`a műveletet.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása

Egyetlen írási művelettel több entitást is beszúrhat egy táblába. A következő mintakód két entitást hoz létre ("Jeff Smith" és "ben Smith"), hozzáadja őket egy `TableBatchOperation` objektumhoz a `Insert` metódus használatával, majd meghívja `CloudTable.ExecuteBatchAsync`a műveletet.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Egy partíció összes entitásának beolvasása

Egy adott partíció összes entitásához tartozó tábla lekérdezéséhez használjon egy `TableQuery` objektumot. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Egyetlen entitás beolvasása

Megírhat egy lekérdezést, amely egyetlen, adott entitást kap. A következő kód egy `TableOperation` objektumot használ a "ben Smith" nevű ügyfél megadásához. A metódus csak egyetlen entitást ad vissza, nem egy gyűjteményt, és a visszaadott `TableResult.Result` érték `CustomerEntity` egy objektum. A partíciók és a sorok kulcsának a lekérdezésben való megadásával a leggyorsabb módszer egyetlen entitás beolvasására a `Table` szolgáltatásból.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Entitás törlése

A keresés után törölheti az entitásokat. A következő kód egy "ben Smith" nevű ügyfél-entitást keres és töröl:

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>További lépések
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
