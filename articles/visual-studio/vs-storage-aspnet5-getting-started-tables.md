---
title: Ismerkedés a table storage és a Visual Studio csatlakoztatott szolgáltatásainak (az ASP.NET Core) |} A Microsoft Docs
description: Első lépések az Azure Table storage, ASP.NET Core-projektben a Visual Studióban egy tárfiókot, a Visual Studio használatával való csatlakozást követően kapcsolódó szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 1f90ce71084ba3acbf5a0aec5c7b8e9683323766
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011622"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Ismerkedés az Azure Table storage és a Visual Studio csatlakoztatott szolgáltatásainak

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Ez a cikk bemutatja, hogyan kezdheti el az Azure Table storage a Visual Studióban létrehozott vagy hivatkozott Azure storage-fiók egy ASP.NET Core-projektet a Visual Studio használatával után **csatlakoztatott szolgáltatás** funkció. A **csatlakoztatott szolgáltatás** művelet telepíti a megfelelő NuGet-csomagokat a projekthez az Azure storage eléréséhez, és a projekt konfigurációs fájlokat ad hozzá a tárfiók kapcsolati karakterláncát. (Lásd: [Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkhoz.)

Az Azure Table storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adat tárolására. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure-felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. Az Azure Table storage használatával kapcsolatos további általános információkért lásd: [.NET használatával az Azure Table storage használatának első lépései](../storage/storage-dotnet-how-to-use-tables.md).

Első lépésként hozzon létre egy táblát a storage-fiókban. Ez a cikk majd megjelenít egy tábla létrehozása a C#-ban, és hogyan hajthat végre alapszintű táblázat műveletek, például a hozzáadása, módosítása, olvasó és táblabejegyzéseket eltávolítása.  A kódot használja az Azure Storage ügyféloldali kódtára a .NET-hez. Az ASP.NET kapcsolatos további információkért lásd: [ASP.NET](https://www.asp.net).

Az Azure Storage API-k némelyike aszinkron, és ebben a cikkben a kód azt feltételezi, hogy az aszinkron módszereket használ. Lásd: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) további információt.

## <a name="access-tables-in-code"></a>Hozzáférés táblák a code-ban

Hozzáférnek az ASP.NET Core-projektek, a C# forrásfájlokat, amely az Azure table storage eléréséhez a következő elemeket is kell.

1. Adja hozzá a szükséges `using` utasításokat:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Get- `CloudStorageAccount` objektum, amely a storage-fiók adatait jelöli. Használja a következő kódra, a storage-fiók és a fiókkulcsot, amely találhatja meg az appSettings.JSON fájl tárolási kapcsolati karakterlánc neve használatával:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Get- `CloudTableClient` objektum, melyet a tárfiókban található táblázat objektumokra hivatkozzanak:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- `CloudTable` hivatkozási objektumot egy adott táblához és entitások beállításához:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Hozzon létre egy táblát a code-ban

Az Azure-tábla létrehozásához async módszert létrehozása, és a benne található hívja `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Entitás hozzáadása egy táblához, hozzon létre egy osztályt, amely az entitás tulajdonságainak meghatározása. Az alábbi kód meghatároz egy entitásosztályt, nevű `CustomerEntity` , amely használ az ügyfél keresztnevét sorkulcsnak és a vezetéknevét partíciókulcsnak.

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

Tábla műveletek használata esetén az entitások használata a `CloudTable` objektumot, amelyet előzőleg létrehozott [hozzáférnek a kódban](#access-tables-in-code). A `TableOperation` objektum képviseli kell végrehajtani a műveletet. Az alábbi példakód bemutatja, hogyan hozhat létre egy `CloudTable` objektum és a egy `CustomerEntity` objektum. A művelet előkészítéséhez egy `TableOperation` hoz létre, beszúrja az ügyfélentitást a táblába. Végül, a művelet meghívásával hajtható végre `CloudTable.ExecuteAsync`.

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

Több entitás beszúrhat egy táblába egyetlen írási művelettel. Az alábbi példakód létrehoz két entitásobjektumot ("Kovács" és "Ben Smith"), hozzáadja őket egy `TableBatchOperation` objektumba a `Insert` módszer, majd elindítja a művelet meghívásával `CloudTable.ExecuteBatchAsync`.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Az összes entitás partíció beolvasása

Egy tábla az entitások egy partíció összes lekérdezés, használja a `TableQuery` objektum. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

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

A lekérdezés az egy adott entitás írhat. A következő kódban a `TableOperation` objektumot adja meg a "Ben Smith" nevű ügyfélhez. A metódus adja vissza, csak egyetlen entitást helyett egy gyűjteményt, és a visszaadott érték `TableResult.Result` van egy `CustomerEntity` objektum. A leggyorsabb módja az egyetlen entitás lekérdezése partíció-és sorkulcsok megadása a lekérdezésben a `Table` szolgáltatás.

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

Egy entitás után keresse meg azt törölheti. Az alábbi kód keres, és töröl egy ügyfélentitást "Ben Smith" nevű:

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
