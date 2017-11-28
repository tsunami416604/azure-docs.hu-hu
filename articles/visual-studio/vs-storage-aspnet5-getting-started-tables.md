---
title: "Első lépések a Visual Studio és a table storage (az ASP.NET Core) szolgáltatások csatlakoztatott |} Microsoft Docs"
description: "Ismerkedés az Azure Table storage egy ASP.NET Core projekt, a Visual Studio egy tárfiókot, a Visual Studio használatával történő kapcsolódás után kapcsolódó szolgáltatások"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: 81f0304850a108fc688dd862ff5ab677d6ebc28e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Ismerkedés az Azure Table storage és a Visual Studio kapcsolódó szolgáltatások

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Ez a cikk ismerteti az első lépések az Azure Table storage a Visual Studióban létrehozott vagy a Visual Studio használatával Azure-tárfiók az ASP.NET Core projekt hivatkozik után **kapcsolódó szolgáltatások** szolgáltatás. A **kapcsolódó szolgáltatások** műveletet a megfelelő NuGet-csomagok az Azure storage a projekt eléréséhez telepíti, és a tárfiók kapcsolati karakterlánca ad hozzá a projekt konfigurációs fájlokat. (Lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkhoz.)

Az Azure Table storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adatok tárolásához. A szolgáltatás egy nosql-alapú adattároló, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. Azure Table storage használatával kapcsolatos további általános információkért lásd: [Ismerkedés az Azure Table storage használatának .NET](../storage/storage-dotnet-how-to-use-tables.md).

Első lépésként hozzon létre egy tábla a tárfiókban lévő. Ez a cikk megjeleníti a tábla létrehozása a C# és hogyan hajthat végre alapszintű tábla műveletek, például a hozzáadása, módosítása, olvasása, és táblabejegyzéseket eltávolítása.  A kód az Azure Storage ügyféloldali kódtára a .NET-hez használja. ASP.NET kapcsolatos további információkért lásd: [ASP.NET](http://www.asp.net).

Az Azure Storage API-k között aszinkron, és ebben a cikkben a kód azt feltételezi, hogy aszinkron metódusok használatban van. Lásd: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) további információt.

## <a name="access-tables-in-code"></a>Hozzáférés táblák kódban

Az ASP.NET Core projektek hozzáférnek, C# forrásfájlokat, amelyhez hozzáférhetnek az Azure table storage a következő elemek is kell.

1. Adja hozzá a szükséges `using` utasításokat:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Első egy `CloudStorageAccount` objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs használja a következő kódot:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy `CloudTableClient` objektum található a tárfiók tábla objektumok hivatkozni:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy `CloudTable` referenciaobjektum bizonyos táblájához és entitások mutató hivatkozás:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable table = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tábla létrehozása a kódban

Az Azure tábla létrehozása, hívja meg a "CreateIfNotExistsAsync()":

```cs
// Create the CloudTable if it does not exist
await table.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Egy entitás felvételére egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Az alábbi kód meghatároz egy entitásosztályt nevű `CustomerEntity` , amely használ az ügyfél keresztnevét sorkulcsnak és a vezetéknevét partíciókulcsnak.

```cs
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

Tábla az entitások használata érintő műveletek a `CloudTable` objektumot, amelyet előzőleg létrehozott [hozzáférnek a kódban](#access-tables-in-code). A `TableOperation` objektum által jelképezett kell elvégezni a műveletet. Az alábbi példakód bemutatja, hogyan hozzon létre egy `CloudTable` objektum és a `CustomerEntity` objektum. A művelet előkészítéséhez egy `TableOperation` jön létre, beszúrja az ügyfélentitást a táblába. Végezetül, a művelet végrehajtása meghívásával `CloudTable.ExecuteAsync`.

```cs
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

Több entitás egyetlen írási művelettel egy táblába beszúrásához. Az alábbi példakód létrehoz két entitásobjektumot ("Jeff Smith" és "Ben Smith"), hozzáadja őket egy `TableBatchOperation` objektumba a `Insert` metódust, és ezután elindítja a művelet által hívott `CloudTable.ExecuteBatchAsync`.

```cs
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

## <a name="get-all-of-the-entities-in-a-partition"></a>Egy partíció beolvasása összes entitások

Egy tábla összes partíció entitástartományának lekérdezéséhez használja a `TableQuery` objektum. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

```cs
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

Írhat egy lekérdezést egy adott entitás eléréséhez. A következő kódban a `TableOperation` adhatja meg az ügyfél "Ben Smith" nevű objektum. A metódus visszaadja az egyetlen entitás helyett egy gyűjtemény és a visszaadott érték `TableResult.Result` van egy `CustomerEntity` objektum. A leggyorsabban az egyetlen entitás lekérdezése partíció-és sorkulcsok megadása a lekérdezésben a `Table` szolgáltatás.

```cs
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

Miután megtalálta az entitás törölheti. Az alábbi kód keresi, és töröl egy ügyfélentitást "Ben Smith" nevű:

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

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

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
