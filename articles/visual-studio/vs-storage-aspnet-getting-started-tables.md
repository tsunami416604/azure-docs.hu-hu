---
title: Ismerkedés az Azure table storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET) |} A Microsoft Docs
description: Ismerkedés az Azure table storage használata az ASP.NET-projektben a Visual Studio és a egy storage-fiók használata a Visual Studio csatlakoztatott szolgáltatásain csatlakoztatása után
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531389"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure table storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés

Az Azure Table storage lehetővé teszi nagy mennyiségű strukturált adat tárolására. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure-felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

Ez az oktatóanyag bemutatja, hogyan írhat kódot ASP.NET az Azure table storage entitások használata néhány gyakori forgatókönyvet. Ilyen például, amikor egy tábla létrehozása és hozzáadása, lekérdezéséhez és táblaentitások törlése. 

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Hozzon létre egy MVC-vezérlő 

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**, és a helyi menüből válassza az **Hozzáadás -> vezérlő**.

    ![Vezérlő hozzáadása egy ASP.NET MVC-alkalmazáshoz](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. A a **hozzáadása Scaffold** párbeszédpanelen válassza **MVC 5 vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![Adja meg az MVC-vezérlő típusa](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Az a **vezérlő hozzáadása** párbeszédpanelen adja a vezérlő *TablesController*, és válassza ki **Hozzáadás**.

    ![Az MVC-vezérlő neve](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adja hozzá a következő *használatával* irányelveket a `TablesController.cs` fájlt:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>A modellosztály létrehozása

Számos, a példák a jelen cikk használatát egy **TableEntity**-nevű osztály származtatott **CustomerEntity**. A következő lépések végigvezetik egy modellosztályt, ez az osztály deklaráló:

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **modellek**, és a helyi menüből válassza az **Hozzáadás -> osztály**.

1. Az a **új elem hozzáadása** párbeszédpanelen, az osztály neve **CustomerEntity**.

1. Nyissa meg a `CustomerEntity.cs` fájlt, és adja hozzá a következő **használatával** irányelv:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Módosítsa az osztály úgy, hogy befejezésekor az osztály van meghatározva, ahogy az alábbi kódot. Az osztály deklarálja az entitás nevű osztályt **CustomerEntity** , amely használ az ügyfél keresztnevét sorkulcsnak és a vezetéknevét partíciókulcsnak.

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
    }
    ```

## <a name="create-a-table"></a>Tábla létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy táblát:

> [!NOTE]
> 
> Ebben a szakaszban azt feltételezi, hogy végrehajtotta a [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon meg egy metódust nevű **CreateTable** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **CreateTable** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudTableClient** objektum egy table-szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- **CloudTable** egy hivatkozást a kívánt tábla nevét képviselő objektum. A **CloudTableClient.GetTableReference** metódus nem derül a table storage egy kérelmet. A hivatkozás a rendszer visszaadja a tábla létezik-e. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hívja a **CloudTable.CreateIfNotExists** metódussal hoz létre a tábla, ha még nem található. A **CloudTable.CreateIfNotExists** metódus visszatért **igaz** Ha a tábla nem létezik, és sikeresen létrehozva. Ellenkező esetben **hamis** adja vissza.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Frissítés a **ViewBag** a tábla nevével.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **CreateTable** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `CreateTable.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Create table** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Tábla létrehozása](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Ahogy korábban említettük a **CloudTable.CreateIfNotExists** metódus visszatért **igaz** csak a tábla nem létezik a és jön létre. Ezért ha futtassa az alkalmazást, ha a tábla létezik, a metódus visszaadja **hamis**. Futtassa az alkalmazást többször, törölnie kell a táblában az alkalmazás újbóli futtatása előtt. A tábla törlése keresztül lehetséges az **CloudTable.Delete** metódust. A táblát a is törölheti a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

*Entitások* c térkép\# osztályból származtatott egyéni osztály használatával objektumok **TableEntity**. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Ebben a szakaszban láthatja, hogyan adhat meg egy entitásosztályt, amely az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb fokú méretezhetőségét teszi lehetővé. Bármilyen tulajdonság, a table service szolgáltatásban tárolni kívánt a tulajdonság, amely egyaránt beállítási és lekérési értéket támogatott típusú nyilvános tulajdonságának kell lennie.
Az entitásosztály *kell* deklaráljon egy nyilvános paraméter nélküli konstruktor.

> [!NOTE]
> 
> Ebben a szakaszban azt feltételezi, hogy végrehajtotta a [a fejlesztési környezet beállítása](#set-up-the-development-environment).

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a következő irányelv, hogy a kód a `TablesController.cs` fájl hozzáférhet a **CustomerEntity** osztály:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adjon meg egy metódust nevű **AddEntity** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **AddEntity** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudTableClient** objektum egy table-szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- **CloudTable** egy hivatkozást a tábla, amelyhez fog hozzáadni az új entitást képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Példányt létrehozni és inicializálni a **CustomerEntity** osztály.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Hozzon létre egy **TableOperation** objektum, amely beszúrja az ügyfélentitást.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. A insert művelet meghívásával hajtható végre a **CloudTable.Execute** metódust. A művelet eredményét vizsgálatával ellenőrizheti a **TableResult.HttpStatusCode** tulajdonság. A 2xx állapotkóddal: azt jelzi, hogy az ügyfél által kért művelet feldolgozása sikeres volt. Például egy HTTP-állapotkód: 204, ami azt jelenti, hogy a művelet feldolgozása sikeresen megtörtént, és a kiszolgáló új entitások az eredmények sikeres Beszúrások nem adott vissza bármilyen tartalmat.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Frissítés a **ViewBag** a táblázat neve és a beillesztési művelet eredményét.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **AddEntity** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `AddEntity.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **entitás hozzáadása** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Entitás hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Az entitás hozzáadásának a szakasz lépéseit követve ellenőrizheti [beolvasása egyetlen entitás](#get-a-single-entity). Is használhatja a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) a táblák összes entitások megtekintéséhez.

## <a name="add-a-batch-of-entities-to-a-table"></a>Egy entitásköteget hozzáadása táblázathoz

Mellett [hozzáadása egy entitás egy táblához, egy egyszerre](#add-an-entity-to-a-table), entitásokat is hozzáadhat a Batch szolgáltatásban. Entitások hozzáadása a batch csökkenti a kód és az Azure table service közötti üzenetváltások számát. A következő lépések bemutatják, hogyan adhat hozzá több entitás egyetlen táblába insert művelet:

> [!NOTE]
> 
> Ebben a szakaszban azt feltételezi, hogy végrehajtotta a [a fejlesztési környezet beállítása](#set-up-the-development-environment).

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon meg egy metódust nevű **AddEntities** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **AddEntities** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudTableClient** objektum egy table-szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- **CloudTable** egy hivatkozást a tábla, amelyhez fog hozzáadni az új entitások képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Néhány felhasználói objektum alapján hozza létre a **CustomerEntity** modellezheti az osztály a szakaszban bemutatott [hozzáadása egy entitás egy táblához](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Get- **TableBatchOperation** objektum.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Entitások hozzáadása a kötegelt insert művelet objektumhoz.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Hajtsa végre a kötegelt insert művelet meghívásával a **CloudTable.ExecuteBatch** metódust.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. A **CloudTable.ExecuteBatch** metódus listáját adja vissza **TableResult** objektumok ahol minden egyes **TableResult** objektum ellenőrizni lehet meghatározni a sikeres vagy sikertelen egyes műveleteket. Ebben a példában adja át a listában a nézetre, és lehetővé teszik a nézet megjeleníti az eredményeket az egyes műveletek. 
 
    ```csharp
    return View(results);
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **AddEntities** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `AddEntities.cshtml`, és módosítsa, hogy a következőhöz hasonlóan néz ki.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **entitások hozzáadása** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Entitások hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Az entitás hozzáadásának a szakasz lépéseit követve ellenőrizheti [beolvasása egyetlen entitás](#get-a-single-entity). Is használhatja a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) a táblák összes entitások megtekintéséhez.

## <a name="get-a-single-entity"></a>Egyetlen entitás beolvasása

Ez a szakasz bemutatja, hogyan beolvasása egyetlen entitás az a entitás sorkulcsa és partíciókulcs egy táblából. 

> [!NOTE]
> 
> Ebben a szakaszban azt feltételezi, hogy végrehajtotta a [a fejlesztési környezet beállítása](#set-up-the-development-environment), és adatokat használ [entitásköteg hozzáadása táblázathoz](#add-a-batch-of-entities-to-a-table). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon meg egy metódust nevű **GetSingle** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **GetSingle** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudTableClient** objektum egy table-szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- **CloudTable** egy hivatkozást a tábla, amelyből keres az entitást képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy beolvasása művelet objektumot, amely a osztályból származtatott entitásobjektumot **TableEntity**. Az első paraméter az *partitionKey*, második paraméterként pedig a *rowKey*. Használatával a **CustomerEntity** osztály- és a szakaszban bemutatott [entitásköteg hozzáadása táblázathoz](#add-a-batch-of-entities-to-a-table), a következő kódrészlet lekérdezi a táblázat egy **CustomerEntity**az entitás egy *partitionKey* "Smith" értékét és a egy *rowkey tulajdonságok esetén* "Ben" értékét:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Hajtsa végre a lekérési művelet.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Adja meg az eredményt a nézet a megjelenítéshez.

    ```csharp
    return View(result);
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **GetSingle** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `GetSingle.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **beolvasása egyetlen** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Egyetlen beolvasása](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Minden entitás egy partíció beolvasása

A szakaszban ismertetett módon [hozzáadása egy entitás egy táblához](#add-an-entity-to-a-table), egy partíciót és a egy sorkulcsot kombinációja a táblában az entitás egyedi azonosításához. Az azonos partíciókulcsú entitások az eltérő partíciókulcsok entitások gyorsabban lekérdezhetők. Ez a szakasz bemutatja, hogyan egy táblából egy partíció megadott származó összes entitás.  

> [!NOTE]
> 
> Ebben a szakaszban azt feltételezi, hogy végrehajtotta a [a fejlesztési környezet beállítása](#set-up-the-development-environment), és adatokat használ [entitásköteg hozzáadása táblázathoz](#add-a-batch-of-entities-to-a-table). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon meg egy metódust nevű **GetPartition** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **GetPartition** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudTableClient** objektum egy table-szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- **CloudTable** egy hivatkozást a tábla, amelyből keres az entitások képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozza létre a **TableQuery** objektumot adja meg a lekérdezés a **ahol** záradékban. Használatával a **CustomerEntity** osztály- és a szakaszban bemutatott [entitásköteg hozzáadása táblázathoz](#add-a-batch-of-entities-to-a-table), a következő kódrészlet lekérdezi a tábla összes entitáshoz, a  **PartitionKey** (az ügyfél Vezetéknév), a "Smith" értékkel rendelkezik:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Belül egy hurkot, hívja a **CloudTable.ExecuteQuerySegmented** továbbítja a lekérdezés objektumot, az előző lépésben példányosítása metódust.  A **CloudTable.ExecuteQuerySegmented** metódus adja vissza egy **TableContinuationToken** objektum, amely – ha **null** -azt jelzi, hogy nincsenek-e további entitások lekéréséhez. A hurok belül egy másik ciklus használatával megismételheti a visszaadott entitásokat. Az alábbi példakód egy listához minden visszaadott entitás egészül ki. Miután a hurok véget ér, a lista nézet átadott Megjelenítés: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **GetPartition** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `GetPartition.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **partíció beolvasása** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Partíció beolvasása](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Entitás törlése

Ez a szakasz bemutatja, hogyan olyan entitást töröl egy táblából.

> [!NOTE]
> 
> Ebben a szakaszban azt feltételezi, hogy végrehajtotta a [a fejlesztési környezet beállítása](#set-up-the-development-environment), és adatokat használ [entitásköteg hozzáadása táblázathoz](#add-a-batch-of-entities-to-a-table). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon meg egy metódust nevű **DeleteEntity** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **DeleteEntity** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudTableClient** objektum egy table-szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Get- **CloudTable** egy hivatkozást a tábla, amelyből törölni az entitást képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy törlési művelet objektumot, amely a osztályból származtatott entitásobjektumot **TableEntity**. Ebben az esetben használja a **CustomerEntity** osztály- és a szakaszban bemutatott [entitásköteg hozzáadása táblázathoz](#add-a-batch-of-entities-to-a-table). Az entitás **ETag** érvényes értékre kell állítani.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Hajtsa végre a törlési művelet.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Adja meg az eredményt a nézet a megjelenítéshez.

    ```csharp
    return View(result);
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **DeleteEntity** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `DeleteEntity.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **entitás törlése** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Egyetlen beolvasása](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure blob storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure queue storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
