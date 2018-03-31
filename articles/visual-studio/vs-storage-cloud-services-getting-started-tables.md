---
title: Ismerkedjen meg a table storage és a Visual Studio services (felhőszolgáltatások) kapcsolódó |} Microsoft Docs
description: Ismerkedés az Azure Table storage egy tárfiókot, a Visual Studio használatával csatlakozzon egy felhőszolgáltatás-projektet a Visual Studio használatával kapcsolódó szolgáltatások
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 68ce9630c5224bd7d9bc4b093ce54dd94140f9e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure table storage és a Visual Studio kapcsolódó szolgáltatások (felhőalapú szolgáltatások projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az első lépések az Azure table storage a Visual Studióban létrehozott vagy a Visual Studio használatával Azure-tárfiók felhőalapú szolgáltatások projektben hivatkozott után **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel. A **kapcsolódó szolgáltatások hozzáadása** műveletet a megfelelő NuGet-csomagok az Azure storage a projekt eléréséhez telepíti, és a tárfiók kapcsolati karakterlánca ad hozzá a projekt konfigurációs fájlokat.

Az Azure Table storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adatok tárolásához. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

A kezdéshez, először a tárfiókban lévő tábla létrehozásához. Mutat be egy Azure-tábla létrehozása a kódban, valamint hogyan hajthat végre alapszintű tábla és entitás műveletek, például a hozzáadása, módosítása, olvasási és táblaentitásokat olvasásakor. A Kódminták C nyelven íródtak\# code, és használja a [Microsoft Azure Storage ügyféloldali kódtára a .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Megjegyzés:** hajtsa végre az Azure storage hívásainak API-k aszinkron. Lásd: [aszinkron programozás az Async és Await](http://msdn.microsoft.com/library/hh191443.aspx) további információt. Az alábbi kódot azt feltételezi, hogy aszinkron programozási módszerek vannak használatban.

* Lásd: [Ismerkedés az Azure Table storage használatának .NET](../storage/storage-dotnet-how-to-use-tables.md) bővebben programozott módon kezelését.
* Lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkat.
* Lásd: [felhőalapú szolgáltatások dokumentációja](https://azure.microsoft.com/documentation/services/cloud-services/) Azure felhőszolgáltatások kapcsolatos általános információkat.
* Lásd: [ASP.NET](http://www.asp.net) ASP.NET-alkalmazások programozásával kapcsolatos további információt.

## <a name="access-tables-in-code"></a>Hozzáférés táblák kódban
Hozzáférnek a cloud service projektek, C# forrásfájlokat, amelyhez hozzáférhetnek az Azure table storage a következő elemek is kell.

1. Győződjön meg arról, hogy a névtér-deklarációk, a C# fájlban tetején az ezen **használatával** utasításokat.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Első egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A következő kódot használja a tárolási kapcsolati karakterlánc és a tárfiók adatait lekérése az Azure szolgáltatás konfigurációját.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Az összes használja a fenti kódot a kód előtt a következő mintában.
   > 
   > 
3. Első egy **CloudTableClient** hivatkozhasson rá az a táblázat objektumok a tárfiókban lévő objektum.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Első egy **CloudTable** referenciaobjektum bizonyos táblájához és entitások mutató hivatkozás.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Tábla létrehozása a kódban
Az Azure-tábla létrehozásához csak adjon hozzá egy **CreateIfNotExistsAsync** való a után kap egy **CloudTable** objektum a hozzáférés-kódban táblák"című részben leírtak szerint.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Az alábbi kód meghatároz egy entitásosztályt nevű **CustomerEntity** , amely használ az ügyfél keresztnevét a sorkulcs és a vezetéknevét partíciókulcsnak.

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

Entitások érintő tábla műveletet végzett használatával a **CloudTable** objektum, amely a korábban létrehozott "Hozzáférési táblák a kódban." A **TableOperation** objektum által jelképezett kell elvégezni a műveletet. Az alábbi példakód bemutatja, hogyan hozzon létre egy **CloudTable** objektum és a **CustomerEntity** objektum. A művelet előkészítéséhez egy **TableOperation** jön létre, beszúrja az ügyfélentitást a táblába. Végezetül, a művelet végrehajtása meghívásával **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Több entitás egyetlen írási művelettel egy táblába beszúrásához. Az alábbi példakód létrehoz két entitásobjektumot ("Jeff Smith" és "Ben Smith"), hozzáadja őket egy **TableBatchOperation** objektumot, az Insert metódus használata, és ezután elindítja a művelet által hívott ** CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Egy partíció beolvasása összes entitások
Egy tábla összes partíció entitástartományának lekérdezéséhez használja a **TableQuery** objektum. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

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
Írhat egy lekérdezést egy adott entitás eléréséhez. A következő kódban a **TableOperation** adhatja meg az ügyfél "Ben Smith" nevű objektum. Ez a módszer adja vissza, csak egyetlen entitást helyett egy gyűjtemény és a visszaadott érték **Ableresult.Result** van egy **CustomerEntity** objektum. A leggyorsabban az egyetlen entitás lekérdezése partíció-és sorkulcsok megadása a lekérdezésben a **tábla** szolgáltatás.

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
Miután megtalálta az entitás törölheti. A következő kód egy ügyfélentitást "Ben Smith" nevű keres, és megtalálja, ha azt törli őket.

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

