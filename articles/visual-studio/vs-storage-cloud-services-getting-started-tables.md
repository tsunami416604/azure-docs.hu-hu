---
title: Asztaltárolás első lépései a Visual Studio használatával (felhőszolgáltatások)
description: Az Azure Table storage használatának első lépései egy felhőalapú szolgáltatásprojektben a Visual Studióban, miután a Visual Studio csatlakoztatott szolgáltatásait használva egy tárfiókhoz csatlakoztak
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299989"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Table Storage-hez és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el használni az Azure table storage-t a Visual Studióban, miután létrehozott vagy hivatkozott egy Azure-tárfiókra egy felhőszolgáltatási projektben a Visual Studio **Csatlakoztatott szolgáltatások hozzáadása** párbeszédpanelen. A **Csatlakoztatott szolgáltatások hozzáadása** művelet telepíti a megfelelő NuGet-csomagokat az Azure storage eléréséhez a projektben, és hozzáadja a tárfiók kapcsolati karakterláncát a projekt konfigurációs fájljaihoz.

Az Azure Table storage szolgáltatás lehetővé teszi, hogy nagy mennyiségű strukturált adatok tárolására. A szolgáltatás egy NoSQL-adattár, amely fogadja a hitelesített hívásokat az Azure-felhőn belülről és kívülről. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

A kezdéshez először létre kell hoznia egy táblát a tárfiókban. Bemutatjuk, hogyan hozhat létre egy Azure-táblát kódban, és hogyan hajthatja végre az alapvető tábla- és entitásműveleteket, például a táblaentitások hozzáadását, módosítását, olvasását és olvasását. A minták C-kóddal\# vannak megírva, és a Microsoft Azure Storage [ügyfélkódtárját használják a .NET-hez.](https://msdn.microsoft.com/library/azure/dn261237.aspx)

**MEGJEGYZÉS:** Az Azure storage-ba irányuló hívásokat végző API-k némelyike aszinkron. További információt lásd: [Aszinkron programozás az Async és a Várakozás.](https://msdn.microsoft.com/library/hh191443.aspx) Az alábbi kód feltételezi, hogy aszinkron programozási módszereket használnak.

* A táblák programozott használatával történő használatáról az [Azure Table storage](../storage/storage-dotnet-how-to-use-tables.md) használatának első lépései című témakörben talál további információt.
* Az Azure Storage szolgáltatással kapcsolatos általános tudnivalókat a [Storage dokumentációjában](https://azure.microsoft.com/documentation/services/storage/) találja.
* Az Azure felhőszolgáltatásaival kapcsolatos általános tudnivalókat a [Cloud Services dokumentációjában](https://azure.microsoft.com/documentation/services/cloud-services/) találja.
* Az [ASP.NET](https://www.asp.net) alkalmazások programozásáról ASP.NET programozásáról ASP.NET című témakörben talál további információt.

## <a name="access-tables-in-code"></a>Hozzáférési táblák kódban
A felhőszolgáltatási projektek ben lévő táblák eléréséhez a következő elemeket kell tartalmaznia minden C# forrásfájlhoz, amely hozzáfér az Azure table storage-hoz.

1. Győződjön meg arról, hogy a C# fájl tetején lévő névtérdeklarációk tartalmazzák ezeket a utasítások **használatával.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával lejuthat a tárolási kapcsolat ihúrés tárfiók adatait az Azure szolgáltatás konfigurációjából.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Használja az összes fenti kódot a kód előtt a következő mintákban.
   > 
   > 
3. Szerezzen be egy **CloudTableClient** objektumot a tárfiókban lévő táblaobjektumokra való hivatkozáshoz.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Egy **CloudTable** referenciaobjektum beszereznie egy adott táblára és entitásokra való hivatkozáshoz.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Tábla létrehozása kódban
Az Azure-tábla létrehozásához csak adjon hozzá egy hívást **createIfNotExistsAsync** a miután egy **CloudTable** objektum ot a "Access táblák kódban" szakaszban leírtak szerint.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. A következő kód egy **CustomerEntity** nevű entitásosztályt határoz meg, amely az ügyfél utónevét használja sorkulcsként, a vezetéknevet pedig partíciókulcsként.

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

Az entitásokat érintő táblaműveletek a "Kódban lévő táblák elérése" című részben korábban létrehozott **CloudTable** objektum használatával történnek. A **TableOperation** objektum a megadandó műveletet jelöli. A következő kód példa bemutatja, hogyan hozhat létre egy **CloudTable** objektumot és egy **CustomerEntity** objektumot. A művelet előkészítéséhez létrejön egy **TableOperation,** amely beilleszti a vevő entitást a táblába. Végül a művelet végrehajtása a **CloudTable.ExecuteAsync**metódus hívásával történik.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Egyetlen írási műveletben több entitást is beilleszthet egy táblába. A következő kódpélda két entitásobjektumot hoz létre ("Jeff Smith" és "Ben Smith"), hozzáadja őket egy **TableBatchOperation** objektumhoz a Beszúrás módszerrel, majd elindítja a műveletet a **CloudTable.ExecuteBatchAsync**hívásával.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>A partíció összes entitásának lekeresése
Ha egy partíció összes entitását szeretné lekérdezni, használjon **TableQuery** objektumot. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

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


## <a name="get-a-single-entity"></a>Egyetlen entitás beszereznie
Írhat egy lekérdezést, hogy egyetlen, adott entitást kapjon. A következő kód egy **TableOperation** objektumot használ a "Ben Smith" nevű ügyfél megadásához. Ez a módszer csak egy entitást ad vissza, nem pedig gyűjteményt, és a **TableResult.Result** függvényben megadott érték **egy CustomerEntity** objektum. A partíció- és sorkulcsok lekérdezésben való megadásával a leggyorsabb an- és mód egyetlen entitás **beolvasása** a Table szolgáltatásból.

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
A keresés után törölhet egy entitást. A következő kód a "Ben Smith" nevű vevőentitást keresi, és ha megtalálja, törli azt.

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

