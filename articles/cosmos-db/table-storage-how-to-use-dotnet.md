---
title: Bevezetés az Azure Table Storage és az Azure Cosmos DB Table API a .NET-tel való használatába
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 08/17/2018
ms.openlocfilehash: 7798af5d667bcf70ba562bb7198f9af570f3005a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044416"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-net"></a>Bevezetés az Azure Table Storage és az Azure Cosmos DB Table API a .NET-tel való használatába
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Az Azure Table Storage szolgáltatással vagy az Azure Cosmos DB Table API-val strukturált NoSQL-adatokat tárolhat a felhőben, ami séma nélküli kulcs-/attribútumtárat biztosít. Mivel a Table Storage és az Azure Cosmos DB Table API séma nélküliek, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage és az Azure Cosmos DB Table API adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage vagy az Azure Cosmos DB Table API segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók vagy Table API-fiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

### <a name="about-this-sample"></a>A minta ismertetése
Ez a minta a [.NET-hez készült Microsoft Azure Cosmos DB Table Library](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) használatát mutatja be gyakori Azure Table Storage-forgatókönyvekben. A csomag neve azt jelzi, hogy az Azure Cosmos DB-hez készült, azonban az Azure Cosmos DB Table API-val és az Azure Table Storage szolgáltatással egyaránt használható, mivel az egyes szolgáltatásoknak csak a végpontja egyedi. Ezeket a forgatókönyveket C#-példák mutatják be, amelyek a következőket ismertetik:
* Táblák létrehozása és törlése
* Sorok beszúrása, frissítése és törlése
* Táblák lekérdezése

## <a name="prerequisites"></a>Előfeltételek

A minta sikeres teljesítéséhez a következőkre lesz szüksége:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Az Azure Storage .NET-hez készült közös kódtára (előzetes verzió)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). - Ez egy szükséges előzetes verziójú csomag, amelyet az éles környezetek támogatnak. 
* [A Microsoft Azure Cosmos DB táblakódtára a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) - Ez a kódtár jelenleg csak .NET Standardhoz érhető el, .NET Core-hoz egyelőre nem.
* [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure Storage-fiók](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>További példák
További példák a Table Storage használatára: [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel). Letöltheti és futtathatja a mintaalkalmazást, vagy megkeresheti a kódot a GitHubon.

## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
* Legegyszerűbben az [Azure Portal](https://portal.azure.com) segítségével hozhatja létre első Azure Storage-fiókját. További tudnivalókért lásd: [Create a storage account](../storage/common/storage-quickstart-create-account.md) (Tárfiók létrehozása).

* Ezenkívül az [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), az [Azure CLI](../storage/common/storage-azure-cli.md), illetve a [.NET-keretrendszerhez készült Storage erőforrás-szolgáltató ügyfél](/dotnet/api/microsoft.azure.management.storage) használatával is létrehozhat egy Azure Storage-fiókot.

* Ha most nem kíván tárfiókot létrehozni, az Azure Storage Emulatorral helyi környezetben futtathatja és tesztelheti a kódját. További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](../storage/common/storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

### <a name="create-a-windows-console-application-project"></a>Windows-konzolalkalmazás projekt létrehozása
Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések azt mutatják be, hogyan hozhat létre konzolalkalmazást a Visual Studio 2017-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. Válassza az **Installed** (Telepítve) > **Visual C#** > **Windows Classic Desktop** (Windows klasszikus asztal) lehetőséget.
3. Válassza a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer)) lehetőséget.
4. Írja be az alkalmazás nevét a **Name** (Név) mezőbe.
5. Kattintson az **OK** gombra.

A mintában szereplő példák hozzáadhatók a konzolalkalmazás `Program.cs` fájljában található `Main()` metódushoz.

Az Azure Cosmos DB táblakódtárat bármilyen típusú .NET-alkalmazásban használhatja, ideértve az Azure-felhőszolgáltatásokat vagy -webappokat és az asztali és mobilalkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="install-the-required-nuget-packages"></a>A szükséges NuGet-csomagok telepítése
Három javasolt csomagra kell hivatkoznia a projektben a minta teljesítéséhez:

* [Az Azure Storage .NET-hez készült közös kódtára (előzetes verzió)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). - A 9.0.0.1-es, vagy annál korábbi verziót használjon (<= 9.0.0.1).

* [A Microsoft Azure Cosmos DB táblakódtára a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) Ez a csomag programozott hozzáférést biztosít az Azure Table Storage-fiókjában vagy az Azure Cosmos DB Table API-fiókjában található adaterőforrásokhoz. Ez a kódtár jelenleg csak .NET Standardhoz érhető el, .NET Core-hoz egyelőre nem.

* [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban, függetlenül attól, hol futnak az alkalmazás kapcsolati karakterlánc elemzéséhez.

A NuGet-csomagok beszerzéséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Keressen rá az interneten a „Microsoft.Azure.Storage.Common” kifejezésre, válasszon egy, a 9.0.0.1-nél nem újabb verziót, és válassza az **Install** (Telepítés) gombot az Azure Storage .NET-hez készült közös kódtárának és annak függőségeinek telepítéséhez. Ügyeljen arra, hogy bejelölje az **Include prerelease** (Előzetes verzió is) négyzetet, mivel ez egy előzetes verziójú csomag.
3. Keressen rá az interneten a „Microsoft.Azure.CosmosDB.Table” kifejezésre, és válassza az **Install** (Telepítés) gombot a Microsoft Azure CosmosDB táblakódtárának telepítéséhez.
4. Keressen rá az interneten a „WindowsAzure.ConfigurationManager” kifejezésre, és válassza az **Install** (Telepítés) gombot a Microsoft Azure Configuration Manager kódtárának telepítéséhez.

> [!NOTE]
> A .NET-keretrendszerhez készült Storage közös kódtár ODataLib-függőségeit nem a WCF-adatszolgáltatások, hanem a NuGeten elérhető ODataLib-csomagok oldják fel. Az ODataLib-kódtárak letölthetők közvetlenül, vagy a kódprojektje hivatkozhat rájuk a NuGeten keresztül. A Storage ügyféloldali kódtár által használt konkrét ODataLib-csomagok az [OData](https://nuget.org/packages/Microsoft.Data.OData/), az [Edm](https://nuget.org/packages/Microsoft.Data.Edm/) és a [Spatial](https://nuget.org/packages/System.Spatial/) csomagok. Ezeket a kódtárakat az Azure Table Storage osztályai használják, de szükséges függőségek a Storage közös kódtárral való programozáshoz.
> 
> 

> [!TIP]
> Ha ismeri az Azure Table Storage szolgáltatást, lehetséges, hogy korábban már használta a [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) csomagot. Javasoljuk, hogy minden új táblaalkalmazás az [Azure Storage közös kódtárát](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common) és az [Azure Cosmos DB táblakódtárát](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) használja, azonban a WindowsAzure.Storage csomag továbbra is támogatott. Ha a WindowsAzure.Storage kódtárat használja, a felhasználási utasításaiba foglalja bele a „Microsoft.WindowsAzure.Storage.Table” részt.
>
>

### <a name="determine-your-target-environment"></a>A célkörnyezet meghatározása
Az útmutatóban lévő példákat háromféle környezetben futtathatja:

* A kódot futtathatja a felhőben, egy Azure Storage-fiókban. 
* A kódot futtathatja a felhőben, egy Azure Cosmos DB-fiókban.
* A kódot futtathatja az Azure Storage Emulatorban is. A Storage Emulator egy helyi környezet, amely egy Azure Storage-fiókot emulál a felhőben. Az emulátor ingyenes lehetőséget biztosít a kódja tesztelésére és hibakeresésére, amíg az alkalmazása fejlesztés alatt áll. Az emulátor egy jól ismert fiókot és kulcsot használ. További információkért lásd: [Fejlesztés és tesztelés az Azure Storage Emulatorral](../storage/common/storage-use-emulator.md).

Ha egy felhőbeli tárfiókot céloz meg, akkor másolja ki a tárfiók elsődleges hívóbetűjét az Azure Portalról. További információkért olvassa el a [tárfiókok hozzáférési kulcsait](../storage/common/storage-account-manage.md#access-keys) ismertető cikket.

> [!NOTE]
> A Storage Emulator megcélzásával elkerülheti az Azure Storage-hoz kapcsolódó költségeket. Ha azonban mégis egy Azure Storage-fiókot céloz meg a felhőben, a minta végrehajtásával járó költségek elhanyagolhatóak.
> 
> 

Ha egy felhőbeli Azure Cosmos DB-fiókot céloz meg, akkor másolja ki a Table API-fiók elsődleges hozzáférési kulcsát az Azure Portalról. További információ: [A kapcsolati sztring frissítése](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása
A .NET-hez készült Azure Storage közös kódtár támogatja a tárolási szolgáltatások eléréséhez használt végpontok és hitelesítő adatok tárolási kapcsolati sztringgel történő konfigurálását. A tárolási kapcsolati sztring egy konfigurációs fájlban tartható fenn a legjobban. 

A kapcsolati sztringekkel kapcsolatos további információkért lásd: [Az Azure Storage kapcsolati sztringjének konfigurálása](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> A fiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a tárfiók kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen és ne mentse egy mások számára elérhető egyszerű szöveges fájlban. Ha azt gyanítja, hogy a kulcs biztonsága sérült, az Azure portál segítségével generálja újra.
> 

A kapcsolati sztring konfigurálásához nyissa meg az `app.config` fájlt a Visual Studio Megoldáskezelőjében. Adja hozzá az alábbi `<appSettings>` elem tartalmát. Az `account-name` kifejezést cserélje a fiókja nevére, az `account-key` kifejezést pedig a hozzáférési kulcsra.

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Például egy Azure Storage-fiók használata esetén a konfiguráció beállítása az alábbihoz hasonló lesz:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>" />
```

Egy Azure Cosmos DB-fiók használata esetén a konfiguráció beállítása az alábbihoz hasonló lesz:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=<account-key>;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

A Storage Emulator célzásához használhat egy hivatkozást, amely leképezi a jól ismert fióknevet és kulcsot. Ebben az esetben a kapcsolati sztring beállítása a következő:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Adja hozzá a következő **using** irányelveket a `Program.cs` fájl elejéhez:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>A Table szolgáltatásügyfél létrehozása
A [CloudTableClient][dotnet_CloudTableClient] osztály segítségével lekérheti a Table Storage-ban tárolt táblákat és entitásokat. A Table-szolgáltatásügyfél létrehozásának egyik módja a következő:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Most már készen áll a Table Storage-ból adatokat olvasó és abba adatokat író kód írására.

## <a name="create-a-table"></a>Tábla létrehozása
A példa bemutatja, hogyan hozhat létre táblát, ha még nem rendelkezik vele:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Az entitásokat a rendszer C#-objektumokra képezi le egy, a [TableEntity][dotnet_TableEntity] osztályból származtatott egyéni osztály használatával. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Az alábbi kód meghatároz egy entitásosztályt, amely az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy adott entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblában. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb fokú méretezhetőségét teszi lehetővé. A táblákban tárolni kívánt entitásoknak támogatott típusúnak, például a [TableEntity][dotnet_TableEntity] osztályból származtatottnak kell lenniük. A táblában tárolni kívánt entitástulajdonságoknak publikusnak kell lenniük, és támogatniuk kell az értékek beolvasását és beállítását is. Az entitástípusnak emellett elérhetővé *kell* tennie egy paraméter nélküli konstruktort is.

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

Az entitásokat is tartalmazó táblaműveleteket a fentebbi, Tábla létrehozása című szakaszban létrehozott [CloudTable][dotnet_CloudTable] objektum végzi el. A végrehajtandó műveletet egy [TableOperation][dotnet_TableOperation] objektum képviseli. Az alábbi példakód bemutatja a [CloudTable][dotnet_CloudTable] objektum, majd egy **CustomerEntity** objektum létrehozását. A művelet előkészítéséhez létrejön egy [TableOperation][dotnet_TableOperation] objektum, amely beszúrja az ügyfélentitást a táblába. Maga a művelet a [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute] meghívásával hajtható végre.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Egyetlen írási művelettel egy teljes entitásköteget is beszúrhat egy táblába. Néhány további megjegyzés a kötegműveletekkel kapcsolatban:

* Egyetlen kötegművelettel frissítéseket, törléseket és beszúrásokat hajthat végre.
* Egy kötegművelet legfeljebb 100 entitást tartalmazhat.
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.
* Ugyan lekérdezést is végre lehet hajtani kötegműveletként, de ilyenkor ez lehet a köteg egyetlen művelete.

Az alábbi példakód létrehoz két entitásobjektumot, és mindkettőt hozzáadja a [TableBatchOperation][dotnet_TableBatchOperation] művelethez az [Insert][dotnet_TableBatchOperation_Insert] metódussal. Ezután meghívja a [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] objektumot a művelet végrehajtásához.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Ha egy táblából egy partíció összes entitását le szeretné kérdezni, használjon [TableQuery][dotnet_TableQuery] objektumot. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése
Ha nem szeretné az összes entitást lekérdezni egy partícióból, megadhat egy tartományt a partíciókulcs és a sorkulcs szűrőjének kombinálásával. Az alábbi példakód két szűrő segítségével kéri le az összes olyan entitást a „Smith” partícióból, melyeknél a sorkulcs (keresztnév) az ábécében az „E”-t megelőző betűvel kezdődik, majd megjeleníti a lekérdezés eredményeit.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kódban a [TableOperation][dotnet_TableOperation] művelettel adja meg a „Ben Smith” nevű ügyfelet. Ezzel a módszerrel a rendszer egy gyűjtemény helyett csak egyetlen entitást ad vissza, a [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] táblában visszaadott érték pedig egy **CustomerEntity** objektum lesz. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Entitás cseréje
Ha frissíteni kíván egy entitást, kérje le a Table szolgáltatásból, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table szolgáltatásba. A következő kód egy meglévő ügyfél telefonszámát módosítja. Az [Insert][dotnet_TableOperation_Insert] parancs hívása helyett a kód a [Replace][dotnet_TableOperation_Replace] parancsot használja. A [Replace][dotnet_TableOperation_Replace] teljesen lecseréli az entitást a kiszolgálón, hacsak az a lekérdezés óta nem módosult, mert ez esetben a művelet sikertelen lesz. Erre a hibára azért van szükség, hogy az alkalmazás ne írhasson felül véletlenül egy olyan módosítást, amelyet az alkalmazás egy másik összetevője hozott létre a lekérés és a frissítés között. A hiba megfelelő kezeléséhez kérje le újra az entitást, végezze el a módosításokat (ha még érvényesek), majd hajtson végre egy újabb [Replace][dotnet_TableOperation_Replace] műveletet. A következő szakaszban megtudhatja, hogyan bírálhatja felül ezt a viselkedést.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Entitás beszúrása vagy lecserélése
Ha az entitás a kiszolgálóról való lekérdezés óta módosult, a [Replace][dotnet_TableOperation_Replace] műveletek sikertelenek lesznek. Ezenkívül a [Replace][dotnet_TableOperation_Replace] művelet sikeres végrehajtásához először le kell kérnie az entitást a kiszolgálóról. Néha azonban nem tudható, hogy az entitás létezik-e a kiszolgálón, és hogy a benne tárolt aktuális értékek irrelevánsak-e. A frissítés mindent felülír. Ehhez használja az [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] műveletet. Ha nem létezik az entitás, ez a művelet beszúrja, ha pedig létezik, akkor a legutóbbi frissítés idejétől függetlenül lecseréli.

Az alábbi példakód létrehoz egy ügyfélentitást Fred Joneshoz, és beilleszti azt a people táblába. Ezután az [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] művelettel menti az azonos partíciókulccsal (Jones) és sorkulccsal (Fred) rendelkező entitást a kiszolgálón, ezúttal a PhoneNumber tulajdonság egy eltérő értékével. Mivel az [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] műveletet használjuk, minden tulajdonság értéke lecserélődik. Ha azonban a táblában korábban nem szerepelt volna Fred Jones entitás, a művelet beszúrta volna.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
Egy táblalekérdezéssel egy entitás bizonyos tulajdonságait is lekérdezheti az összes helyett. Ez a leképezésnek hívott technika csökkenti a sávszélesség felhasználását, és javítja a lekérdezési teljesítményt, főleg a nagy entitások esetében. Az alábbi kódban szereplő lekérdezés csak a táblában található entitásokhoz tartozó e-mail-címeket kérdezi le. Ez a [DynamicTableEntity][dotnet_DynamicTableEntity] és az [EntityResolver][dotnet_EntityResolver] lekérdezésekkel hajtható végre. A Storage Emulator nem támogatja a leképezést, így a kód csak a Table szolgáltatásbeli fiókkal működik.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Entitás törlése
A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Tábla törlése
Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. A törölt táblák a törlés után egy ideig nem hozhatók létre újra.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Oldalak entitásainak aszinkron lekérése
Ha sok entitást olvas, és az összes visszaadott entitás helyett csak az éppen lekérdezett entitásokat szeretné feldolgozni/megjeleníteni, szegmentált lekérdezéssel kérje le az entitásokat. A példa bemutatja, hogy az Async-Await mintázattal hogyan kérhetők le eredmények az oldalakról úgy, hogy ne legyen letiltva a végrehajtás, amíg egy nagy eredménykészletre várakozik. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Asynchronous Programming with Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Aszinkron programozás az Async és Await műveletekkel (C# és Visual Basic)).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>További lépések
Most, hogy mér megismerte a Table Storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről is:

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* További Table Storage-példákat a [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel) című cikkben tekinthet meg.
* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API – referencia](https://msdn.microsoft.com/library/azure/dd179355)
* Az [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) használatával megtudhatja, hogyan egyszerűsítheti az Azure Storage használatához írt kódot
* Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
* [Get started with Azure Blob storage using .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel) a strukturálatlan adatok tárolásához.
* [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../sql-database/sql-database-develop-dotnet-simple.md) a relációs adatok tárolásához.

[Download and install the Azure SDK for .NET]: /develop/net/

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
