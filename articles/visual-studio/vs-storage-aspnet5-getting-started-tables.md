---
title: Asztaltárolás első lépései a Visual Studio (ASP.NET Core) használatával
description: Az Azure Table storage használatának első lépései egy ASP.NET Core projektben a Visual Studióban, miután a Visual Studio csatlakoztatott szolgáltatásait használva egy tárfiókhoz csatlakoztak
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298813"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Az Azure Table storage és a Visual Studio csatlakoztatott szolgáltatásainak első lépései

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Ez a cikk bemutatja, hogyan kezdheti el használni az Azure Table storage-t a Visual Studióban, miután létrehozott vagy hivatkozott egy Azure storage-fiókra egy ASP.NET Core projektben a Visual Studio **Connected Services** szolgáltatás használatával. A **Connected Services** művelet telepíti a megfelelő NuGet csomagokat az Azure storage eléréséhez a projektben, és hozzáadja a tárfiók kapcsolati karakterláncát a projekt konfigurációs fájljaihoz. (Az Azure Storage szolgáltatással kapcsolatos általános tudnivalókat a [Storage dokumentációjában](https://azure.microsoft.com/documentation/services/storage/) találja.)

Az Azure Table storage szolgáltatás lehetővé teszi, hogy nagy mennyiségű strukturált adatok tárolására. A szolgáltatás egy NoSQL-adattár, amely fogadja a hitelesített hívásokat az Azure-felhőn belülről és kívülről. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. Az Azure Table storage használatáról az [Azure Table storage használatával kapcsolatos első lépések című témakörben talál.](../storage/storage-dotnet-how-to-use-tables.md)

Első lépésekhez hozzon létre egy táblát a tárfiókban. Ez a cikk bemutatja, hogyan hozhat létre táblázatot C# nyelven, és hogyan hajthatja végre az alapvető táblaműveleteket, például a táblabejegyzések hozzáadását, módosítását, olvasását és eltávolítását.  A kód az Azure Storage ügyfélkódtárját használja a .NET számára. A ASP.NET ASP.NET a ASP.NET című témakörben [talál](https://www.asp.net)további információt.

Az Azure Storage API-k egy része aszinkron, és a jelen cikkben szereplő kód feltételezi, hogy aszinkron metódusok használatosak. További információt az [Aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) című témakörben talál.

## <a name="access-tables-in-code"></a>Hozzáférési táblák kódban

A ASP.NET Core-projektek ben lévő táblák eléréséhez a következő elemeket kell megadnia minden C# forrásfájlhoz, amely hozzáfér az Azure table storage-hoz.

1. Adja hozzá `using` a szükséges állításokat:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Szerezzen `CloudStorageAccount` be egy objektumot, amely a tárfiók adatait jelöli. Használja a következő kódot a tárfiók nevével és a fiókkulcshasználatával, amely az appSettings.json tárolókapcsolati karakterláncában található:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. A `CloudTableClient` tárfiókban lévő táblaobjektumokra hivatkozó objektum beszereznie:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Egy `CloudTable` adott táblára és entitásokra való hivatkozáshoz referenciaobjektum beszerezni:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tábla létrehozása kódban

Az Azure-tábla létrehozásához hozzon létre egy `CreateIfNotExistsAsync()`aszinkron metódust, és belül meghívja:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Entitás táblához való hozzáadásához hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. A következő kód egy nevű `CustomerEntity` entitásosztályt határoz meg, amely az ügyfél utónevét használja sorkulcsként, a vezetéknevet pedig partíciókulcsként.

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

Az entitásokat érintő táblaműveletek a korábban létrehozott objektumot használják az `CloudTable` Access [táblákban a kódban](#access-tables-in-code). Az `TableOperation` objektum a műveletet jelöli. A következő kódpélda bemutatja, hogyan hozhat létre objektumot `CloudTable` és objektumot. `CustomerEntity` A művelet előkészítéséhez `TableOperation` létrejön egy, amely beilleszti a vevő entitást a táblába. Végül a művelet végrehajtása `CloudTable.ExecuteAsync`a .

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

Egyetlen írási műveletben több entitást is beilleszthet egy táblába. A következő kódpélda két entitásobjektumot hoz létre ("Jeff Smith" és "Ben Smith"), hozzáadja őket egy `TableBatchOperation` objektumhoz a `Insert` metódus használatával, majd elindítja a műveletet a hívással. `CloudTable.ExecuteBatchAsync`

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

## <a name="get-all-of-the-entities-in-a-partition"></a>A partíció összes entitásának lekeresése

Ha egy partíció összes entitását szeretné lekérdezni, `TableQuery` használjon objektumot. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

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

## <a name="get-a-single-entity"></a>Egyetlen entitás beszereznie

Írhat egy lekérdezést, hogy egyetlen, adott entitást kapjon. A következő kód `TableOperation` egy "Ben Smith" nevű ügyfél megadására használja az objektumot. A metódus csak egy entitást ad vissza, `TableResult.Result` nem `CustomerEntity` pedig egy gyűjteményt, és a visszaadott érték egy objektum. A partíció- és sorkulcsok lekérdezésben való megadásával a leggyorsabb `Table` módja annak, hogy egyetlen entitást olvasson le a szolgáltatásból.

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

A keresés után törölhet egy entitást. A következő kód megkeresi és törli a "Ben Smith" nevű vevőentitást:

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
