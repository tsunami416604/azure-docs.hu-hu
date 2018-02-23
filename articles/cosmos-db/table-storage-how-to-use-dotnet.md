---
title: "Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel | Microsoft Docs"
description: "Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható."
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/30/2018
ms.author: mimig
ms.openlocfilehash: 317adaac1551cf00e25640f1d99429ad76d598a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage szolgáltatás strukturált NoSQL-adatokat tárol a felhőben, így séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Az oktatóanyag bemutatja, hogyan használható a [Microsoft Azure CosmosDB tábla .NET-keretrendszerhez készült](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) az Azure Table storage gyakori forgatókönyvet. A csomag nevét jelzi való használathoz az Azure Cosmos DB, de a csomag és kiadással egyaránt Azure Cosmos DB Azure táblák tárolási, az egyes szolgáltatások csak egy egyedi végponttal rendelkezik. Forgatókönyvekben írja használatával C# példák bemutatják, hogyan:
* Hozzon létre vagy töröljön a táblák
* INSERT, update és sorokat törölni
* Lekérdezés táblák

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag sikeres teljesítéséhez a következőkre lesz szüksége:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Az Azure Storage közös .NET-keretrendszerhez készült (előzetes verzió)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). A csomag egy szükséges előzetes éles környezetekben támogatott. 
* [Microsoft Azure CosmosDB tábla könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure Storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>További példák
További példák a Table Storage használatára: [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel). Letöltheti és futtathatja a mintaalkalmazást, vagy megkeresheti a kódot a GitHubon.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Az Azure szolgáltatás-fiók létrehozása

Azure Table storage vagy Azure Cosmos DB használatával táblák dolgozhat. További ehhez beolvassa a szolgáltatások közötti különbségekről [ajánlatok tábla](table-introduction.md#table-offerings). Hozzon létre egy fiókot a szolgáltatás használni fog lesz szüksége. 

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
Legegyszerűbben az [Azure Portal](https://portal.azure.com) segítségével hozhatja létre első Azure Storage-fiókját. További tudnivalókért lásd: [Create a storage account](../storage/common/storage-create-storage-account.md#create-a-storage-account) (Tárfiók létrehozása).

Ezenkívül az [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), az [Azure CLI](../storage/common/storage-azure-cli.md), illetve a [.NET-keretrendszerhez készült Storage erőforrás-szolgáltató ügyfél](/dotnet/api/microsoft.azure.management.storage) használatával is létrehozhat egy Azure Storage-fiókot.

Ha most nem kíván tárfiókot létrehozni, az Azure Storage Emulatorral helyi környezetben futtathatja és tesztelheti a kódját. További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](../storage/common/storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB tábla API-fiók létrehozása

Egy Azure Cosmos DB tábla API-fiók létrehozásával kapcsolatos utasításokért lásd: [tábla API-fiók létrehozása](create-table-dotnet.md#create-a-database-account).

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása
A következő lépésként állítsa be a fejlesztési környezetet a Visual Studióban, hogy kipróbálhassa az útmutatóban megadott példákat.

### <a name="create-a-windows-console-application-project"></a>Windows-konzolalkalmazás projekt létrehozása
Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések azt mutatják be, hogyan hozhat létre konzolalkalmazást a Visual Studio 2017-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. Válassza ki **telepített** > **Visual C#** > **klasszikus Windows asztal**.
3. Válassza a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer)) lehetőséget.
4. Írja be az alkalmazás nevét a **Name** (Név) mezőbe.
5. Kattintson az **OK** gombra.

Az oktatóanyagban szereplő példák hozzáadhatók a konzolalkalmazás `Program.cs` fájljában található `Main()` metódushoz.

Az Azure CosmosDB tábla kódtárat bármilyen .NET-alkalmazás, beleértve az Azure felhőalapú szolgáltatás, vagy a webes alkalmazás, és az asztali és mobil alkalmazások is használhatja. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel
Nincsenek a projektben az oktatóanyag elvégzéséhez hivatkoznia kell három csomagok:

* [Az Azure Storage közös .NET-keretrendszerhez készült (8.6.0-preview)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/8.6.0-preview). 
* [A Microsoft Azure CosmosDB tábla .NET-keretrendszerhez készült](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Ez a csomag az Azure Table storage-fiók vagy Azure Cosmos DB tábla API-fiók adatforrásaihoz programozott hozzáférést biztosít.
* [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban található kapcsolati karakterlánc elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGettel mindkét csomagot beszerezheti. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Keresse rá az interneten a "Microsoft.Azure.Storage.Common", és válassza ki **telepítése** telepítése az Azure Storage közös kódtár .NET (előzetes verzió) és annak függőségeit. Győződjön meg arról a **közé tartoznak az előzetes** jelölőnégyzet be van jelölve, a csomag egy minta.
3. Keresse rá az interneten a "Microsoft.Azure.CosmosDB.Table", és válassza ki **telepítése** telepítése a Microsoft Azure CosmosDB tábla könyvtárban.
4. Keresse rá az interneten a "WindowsAzure.ConfigurationManager", és válassza ki **telepítése** telepítése a Microsoft Azure Configuration Manager könyvtár.

> [!NOTE]
> A Storage .NET közös könyvtárban ODataLib-függőségeit a ODataLib-csomagok érhető el a NuGet, nem a WCF Data Services által megoldott. Az ODataLib-kódtárak letölthetők közvetlenül, vagy a kódprojektje hivatkozhat rájuk a NuGeten keresztül. A Storage ügyféloldali kódtár által használt konkrét ODataLib-csomagok az [OData](http://nuget.org/packages/Microsoft.Data.OData/), az [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) és a [Spatial](http://nuget.org/packages/System.Spatial/) csomagok. Ezeket a kódtárakat az Azure Table storage osztályai használják, amíg azok szükséges függőségek a Storage közös kódtár való programozáshoz.
> 
> 

### <a name="determine-your-target-environment"></a>A célkörnyezet meghatározása
Az útmutatóban lévő példákat kétféle környezetben futtathatja:

* A kódot futtathatja a felhőben, egy Azure Storage-fiókban. 
* A kódot futtathatja a felhőben Azure Cosmos DB fiók ellen.
* A kódot futtathatja az Azure Storage Emulatorban is. A Storage Emulator egy helyi környezet, amely egy Azure Storage-fiókot emulál a felhőben. Az emulátor ingyenes lehetőséget biztosít a kódja tesztelésére és hibakeresésére, amíg az alkalmazása fejlesztés alatt áll. Az emulátor egy jól ismert fiókot és kulcsot használ. További információkért lásd: [Fejlesztés és tesztelés az Azure Storage Emulatorral](../storage/common/storage-use-emulator.md).

Ha egy felhőbeli tárfiókot céloz meg, akkor másolja ki a tárfiók elsődleges hívóbetűjét az Azure Portalról. További információért lásd: [View and copy storage access keys](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys) (A tárelérési kulcsok megtekintése és másolása).

> [!NOTE]
> A Storage Emulator megcélzásával elkerülheti az Azure Storage-hoz kapcsolódó költségeket. Ha azonban mégis egy Azure Storage-fiókot céloz meg a felhőben, az oktatóanyag végrehajtásával járó költségek elhanyagolhatóak.
> 
> 

Egy Azure Cosmos DB fiókot céloz meg, ha az elsődleges elérési kulcsot a tábla API fiók másolása az Azure-portálon. További információkért lásd: [frissítse a kapcsolati karakterlánc](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása
Az Azure Storage közös kódtára a .NET által támogatott végpontok és tárolási szolgáltatások eléréséhez szükséges hitelesítő adatok konfigurálása egy tárolási kapcsolati karakterlánc használatával. A tárolási kapcsolati karakterlánc egy konfigurációs fájlban tartható fenn a legjobban. 

A kapcsolati sztringekkel kapcsolatos további információkért lásd: [Az Azure Storage kapcsolati sztringjének konfigurálása](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> A fiókkulcs hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a tárfiók kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen és ne mentse egy mások számára elérhető egyszerű szöveges fájlban. Ha azt gyanítja, hogy a kulcs biztonsága sérült, az Azure portál segítségével generálja újra.
> 
> 

A kapcsolati karakterlánc konfigurálásához nyissa meg az `app.config` fájlt a Visual Studio Megoldáskezelőjében. Adja hozzá az alábbi `<appSettings>` elem tartalmát. Cserélje le `account-name` nevű, a fiókjához, és `account-key` rendelkező a hívóbetűre:

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

Például ha egy Azure Storage-fiókot használ, a konfigurációs beállítások jelenik meg:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Ha egy Cosmos-DB Azure-fiókot használ, a konfigurációs beállítások jelenik meg:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

A Storage Emulator célzásához használhat egy hivatkozást, amely leképezi a jól ismert fióknevet és kulcsot. Ebben az esetben a kapcsolati sztring beállítása a következő:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Adja hozzá a következő **using** irányelveket a `Program.cs` fájl elejéhez:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage.Common; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Kapcsolati karakterlánc elemzése
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
Egy táblalekérdezéssel egy entitás bizonyos tulajdonságait is lekérdezheti az összes helyett. Ez a leképezésnek hívott technika csökkenti a sávszélesség felhasználását, és javítja a lekérdezési teljesítményt, főleg a nagy entitások esetében. Az alábbi kódban szereplő lekérdezés csak a táblában található entitásokhoz tartozó e-mail-címeket kérdezi le. Ez a [DynamicTableEntity][dotnet_DynamicTableEntity] és az [EntityResolver][dotnet_EntityResolver] lekérdezésekkel hajtható végre. A leképezésről az [Introducing Upsert and Query Projection][blog_post_upsert] (Az upsert művelet és a lekérdezésleképezés bemutatása) című blogbejegyzésből tudhat meg többet. A Storage Emulator nem támogatja a leképezést, így a kód csak a Table szolgáltatásbeli fiókkal működik.

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
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API – referencia](http://msdn.microsoft.com/library/azure/dd179355)
* Az [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) használatával megtudhatja, hogyan egyszerűsítheti az Azure Storage használatához írt kódot
* Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
* [Get started with Azure Blob storage using .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel) a strukturálatlan adatok tárolásához.
* [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../sql-database/sql-database-develop-dotnet-simple.md) a relációs adatok tárolásához.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

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
