---
title: Ismerkedés a table storage és a Visual Studio csatlakoztatott szolgáltatásainak (felhőszolgáltatások) |} A Microsoft Docs
description: Első lépések az Azure Table storage használata egy felhőszolgáltatás-projektet a Visual Studióban egy tárfiókot, a Visual Studio használatával való csatlakozást követően kapcsolódó szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f1e663463ff91da887a4afaebde4b2257347d4f4
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056379"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Első lépései az Azure table storage és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan kezdheti el az Azure table storage használata a Visual Studióban, hivatkozott Azure storage-fiók egy felhőszolgáltatási projekt a Visual studióval vagy létrehozása után **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel. A **csatlakoztatott szolgáltatás hozzáadása** művelet telepíti a megfelelő NuGet-csomagokat a projekthez az Azure storage eléréséhez, és a projekt konfigurációs fájlokat ad hozzá a tárfiók kapcsolati karakterláncát.

Az Azure Table storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adat tárolására. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure-felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

A kezdéshez először hozzon létre egy táblát a storage-fiókban. Bemutatjuk, egy Azure-tábla létrehozása a kódot, valamint hogyan hajthat végre alapszintű táblázat és az entitás műveletek, például a hozzáadása, módosítása, olvasó és táblaentitások olvasása. A Kódminták C nyelven írt\# kódot, és használja a [a Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Megjegyzés:** hajtsa végre az Azure storage hívásainak API-k némelyike aszinkron. Lásd: [aszinkron programozás az Async és Await](http://msdn.microsoft.com/library/hh191443.aspx) további információt. Az alábbi kódot feltételezi, hogy programozási aszinkron metódusok vannak használatban.

* Lásd: [.NET használatával az Azure Table storage használatának első lépései](../storage/storage-dotnet-how-to-use-tables.md) programozott módon kezelésére szolgáló táblák további tájékoztatást.
* Lásd: [Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/) Azure Storage szolgáltatással kapcsolatos általános információkat.
* Lásd: [Cloud Services – dokumentáció](https://azure.microsoft.com/documentation/services/cloud-services/) az Azure cloud services kapcsolatos általános információkat.
* Lásd: [ASP.NET](http://www.asp.net) ASP.NET-alkalmazások programozása további információt.

## <a name="access-tables-in-code"></a>Hozzáférés táblák a code-ban
Hozzáférnek a cloud service-projektek, a C# forrásfájlokat, amely az Azure table storage eléréséhez a következő elemeket is kell.

1. Ellenőrizze, hogy a névtér-deklarációk, a C# fájlban tetején az ezen **használatával** utasításokat.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Get- **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A következő kód használatával a tárolási kapcsolati karakterlánccal és a tárfiók-információ lekérése az Azure-szolgáltatás konfigurációjából.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Használja a kód előtt a fenti kódja a következő minták.
   > 
   > 
3. Get- **CloudTableClient** objektum, melyet a tárfiókban található táblázat objektumokra hivatkozzanak.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Get- **CloudTable** hivatkozási objektumot egy adott táblához és entitások mutató hivatkozás.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Hozzon létre egy táblát a code-ban
Az Azure-tábla létrehozásához adja hozzá a hívás **CreateIfNotExistsAsync** , a beszerzése után egy **CloudTable** objektumot a "Hozzáférési kódot a táblák" szakaszban leírtak szerint.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Az alábbi kód meghatároz egy entitásosztályt, nevű **CustomerEntity** , amely használ az ügyfél keresztnevét a sorkulcs és a vezetéknevét partíciókulcsnak.

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

Entitásokat érintő tábla műveletek elvégzése használatával a **CloudTable** objektum, amely a korábban létrehozott "Access táblák a kódban." A **TableOperation** objektum képviseli kell végrehajtani a műveletet. Az alábbi példakód bemutatja, hogyan hozhat létre egy **CloudTable** objektum és a egy **CustomerEntity** objektum. A művelet előkészítéséhez egy **TableOperation** hoz létre, beszúrja az ügyfélentitást a táblába. Végül, a művelet meghívásával hajtható végre **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Több entitás beszúrhat egy táblába egyetlen írási művelettel. Az alábbi példakód létrehoz két entitásobjektumot ("Kovács" és "Ben Smith"), hozzáadja őket egy **TableBatchOperation** objektumot, az Insert metódus használatával, és ezután elindítja a művelet meghívásával  **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Az összes entitás partíció beolvasása
Egy tábla az entitások egy partíció összes lekérdezés, használja a **TableQuery** objektum. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>Egyetlen entitás beolvasása
A lekérdezés az egy adott entitás írhat. A következő kódban a **TableOperation** objektumot adja meg a "Ben Smith" nevű ügyfélhez. A metódus visszatérése csak egyetlen entitást helyett egy gyűjteményt, és a visszaadott érték **Ableresult.Result** van egy **CustomerEntity** objektum. A leggyorsabb módja az egyetlen entitás lekérdezése partíció-és sorkulcsok megadása a lekérdezésben a **tábla** szolgáltatás.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Entitás törlése
Egy entitás után keresse meg azt törölheti. A következő kódot egy ügyfélentitást "Ben Smith" nevű keres, és ha megtalálja, hogy törli őket.

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

## <a name="next-steps"></a>További lépések
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

