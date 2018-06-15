---
title: Ismerkedés az Azure table storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET) |} Microsoft Docs
description: Ismerkedés az Azure table storage használata egy ASP.NET-projekt, a Visual Studio egy tárfiókot, a Visual Studio kapcsolódó szolgáltatások használatával történő kapcsolódás után
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
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798631"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure table storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés

Az Azure Table storage lehetővé teszi nagy mennyiségű strukturált adatok tárolásához. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

Ez az oktatóanyag bemutatja, hogyan írhat kódot ASP.NET olyan gyakori forgatókönyveket tartalmaz, az Azure table storage entitások használata. Ilyen például, egy tábla létrehozása és hozzáadása, lekérdezése és tábla entitások törlése. 

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Hozzon létre az MVC-vezérlő 

1. A a **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**, és a helyi menüből válassza ki a **Hozzáadás -> tartományvezérlő**.

    ![Vezérlő hozzáadása az ASP.NET MVC alkalmazások számára](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. A a **hozzáadása Scaffold** párbeszédablakban válassza **MVC 5 vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![Adja meg az MVC-vezérlő típusa](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Az a **vezérlő hozzáadása** párbeszédpanelen, a tartományvezérlő nevét *TablesController*, és válassza ki **Hozzáadás**.

    ![Neve az MVC-vezérlő](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adja hozzá a következő *használatával* irányelvek a `TablesController.cs` fájlt:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Hozzon létre egy modellosztállyal

Számos ezen példája a következő cikket: használja a **TableEntity**-származtatott osztályt **CustomerEntity**. A következő lépések végigvezetik egy modellt a forrásosztállyal deklaráló:

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **modellek**, és a helyi menüből válassza ki a **Hozzáadás -> osztály**.

1. Az a **új elem hozzáadása** párbeszédpanelen a név az osztály **CustomerEntity**.

1. Nyissa meg a `CustomerEntity.cs` fájlt, és adja hozzá a következő **használatával** irányelv:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Módosítsa az osztályt, hogy a befejeződése után az osztály hasonlóan az alábbi kódra van deklarálva. Az osztály deklarál egy entitásosztályt nevű **CustomerEntity** , amely használ az ügyfél keresztnevét sorkulcsnak és a vezetéknevét partíciókulcsnak.

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

A következő lépések bemutatják, hogyan hozzon létre egy táblát:

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a hívott metódus **CreateTable** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **CreateTable** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudTableClient** objektum által jelképezett a table szolgáltatásügyfél.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy **CloudTable** hivatkozni kell a kívánt táblanév képviselő objektum. A **CloudTableClient.GetTableReference** metódus nem tesz, a table storage egy kérelmet. A hivatkozás a tábla létezik-e adja vissza. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hívja a **CloudTable.CreateIfNotExists** metódus tábla létrehozása, ha még nem létezik. A **CloudTable.CreateIfNotExists** metódus beolvasása **igaz** , ha a tábla nem létezik, és sikeresen létrejött. Ellenkező esetben **hamis** adja vissza.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Frissítés a **ViewBag** a tábla nevével.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **CreateTable** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `CreateTable.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **tábla létrehozása** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Tábla létrehozása](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Ahogy korábban említettük a **CloudTable.CreateIfNotExists** metódus beolvasása **igaz** csak a tábla nem létezik és jön létre. Ezért, ha futtatja a az alkalmazás a tábla létezik, a metódus visszaadja **hamis**. Az alkalmazás többször is lefuthat, törölnie kell az a táblázat az alkalmazás ismételt futtatása előtt. A tábla törlésével megteheti a **CloudTable.Delete** metódust. A tábla használatával is törölheti a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

*Entitások* C leképezés\# származtatott egyéni osztály használatával objektumok **TableEntity**. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Ebben a szakaszban láthatja, hogyan adhat meg egy entitásosztályt, amely az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb fokú méretezhetőségét teszi lehetővé. A tulajdonság a table szolgáltatásban tárolni a tulajdonság egy egyaránt és értékek beolvasása tévő támogatott típus nyilvános tulajdonságának kell lennie.
Az entitásosztály *kell* deklarálható nyilvános, paraméter nélküli konstruktora.

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment).

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a következő direktíva, hogy a kód a `TablesController.cs` fájl férhetnek hozzá a **CustomerEntity** osztály:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adja hozzá a hívott metódus **AddEntity** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **AddEntity** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudTableClient** objektum által jelképezett a table szolgáltatásügyfél.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy **CloudTable** a tábla, amelyhez az új entitás hozzáadása szeretne hivatkozni képviselő objektum. 
   
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

1. Az insert művelet végrehajtása meghívásával a **CloudTable.Execute** metódust. A művelet eredményét vizsgálatával ellenőrizheti a **TableResult.HttpStatusCode** tulajdonság. 2xx állapotkódot azt jelzi, hogy az ügyfél által kért művelet feldolgozása sikeres volt. Például egy HTTP-állapotkód: 204, ami azt jelenti, hogy a művelet feldolgozása sikeresen megtörtént, és a kiszolgáló új entitások eredmények sikeres Beszúrások nem adott vissza tartalmakhoz.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Frissítés a **ViewBag** a táblázat nevét, és az insert művelet eredménye.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **AddEntity** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `AddEntity.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **entitás** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Entitás hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Ellenőrizheti, hogy az entitás felvette a szakaszban ismertetett lépések [beolvasása egyetlen entitás](#get-a-single-entity). Használhatja a [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md) a táblák összes entitás megtekintéséhez.

## <a name="add-a-batch-of-entities-to-a-table"></a>Egy teljes entitásköteget hozzáadása a táblához

Nem csak [vehető fel olyan entitás egy táblához egyszerre](#add-an-entity-to-a-table), azt is megteheti entitások kötegben. Entitás hozzáadása a kötegelt csökkenti a kódot és az Azure table szolgáltatás közötti üzenetváltások számát. A következő lépések bemutatják, hogyan adhat több entitás egyetlen táblához insert művelet:

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment).

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a hívott metódus **AddEntities** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **AddEntities** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudTableClient** objektum által jelképezett a table szolgáltatásügyfél.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy **CloudTable** hivatkozni kell a tábla, amelyhez kívánja hozzáadni az új entitásokat képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Egyes felhasználói objektumok alapján hozható létre a **CustomerEntity** osztály a szakaszban bemutatott minta [vehető fel olyan entitás egy táblához](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Első egy **TableBatchOperation** objektum.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Entitás hozzáadása a kötegelt beszúrási művelet objektum.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. A kötegelt beszúrási művelet végrehajtása meghívásával a **CloudTable.ExecuteBatch** metódust.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. A **CloudTable.ExecuteBatch** metódus listáját adja vissza **TableResult** objektumok ahol minden **TableResult** objektum is vizsgálja meg a sikeres vagy sikertelen volt-e minden egyes művelethez. Ennél a példánál adja át a listában egy nézetre, és lehetővé teszik a nézetben minden egyes művelet eredményének megjelenítése. 
 
    ```csharp
    return View(results);
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **AddEntities** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `AddEntities.cshtml`, és módosítsa azt, hogy azt a következőhöz hasonló.

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **új entitásokat** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Entitások hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Ellenőrizheti, hogy az entitás felvette a szakaszban ismertetett lépések [beolvasása egyetlen entitás](#get-a-single-entity). Használhatja a [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md) a táblák összes entitás megtekintéséhez.

## <a name="get-a-single-entity"></a>Egyetlen entitás beolvasása

Ez a szakasz bemutatja, hogyan egyetlen entitás lekérése egy táblához, az entitás sorkulcsa és a partíciós kulcs használatával. 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment), és adatokat használ [egy teljes entitásköteget hozzáadása a táblához](#add-a-batch-of-entities-to-a-table). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a hívott metódus **GetSingle** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **GetSingle** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudTableClient** objektum által jelképezett a table szolgáltatásügyfél.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy **CloudTable** hivatkozni kell a táblázat, amelyből az entitás keres képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy lekérése művelet objektumot, amely származó entitásobjektumra **TableEntity**. Az első paraméter az *partitionKey*, és a második paraméter a *rowKey*. Használja a **CustomerEntity** osztály- és a szakaszban bemutatott [egy teljes entitásköteget hozzáadása a táblához](#add-a-batch-of-entities-to-a-table), a következő kódrészletet lekérdezi a tábla egy **CustomerEntity**entitás egy *partitionKey* "Smith" értékének és egy *rowKey* "Ben" értékét:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. A beolvasási műveletet végrehajtani.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. A nézetben megjelenítendő át az eredményt.

    ```csharp
    return View(result);
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **GetSingle** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `GetSingle.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **beolvasása egyetlen** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Egyetlen beolvasása](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése

A szakaszban említett [vehető fel olyan entitás egy táblához](#add-an-entity-to-a-table), egy partíciót és egy sorkulcsa együttesen egyedi módon azonosítja az entitást egy tábla. Az azonos partíciókulcsú entitások gyorsabban entitások lehet lekérdezni a különböző partíciókulcsúak. Ez a szakasz bemutatja, hogyan egy táblából egy partíció megadott származó összes entitás.  

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment), és adatokat használ [egy teljes entitásköteget hozzáadása a táblához](#add-a-batch-of-entities-to-a-table). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a hívott metódus **GetPartition** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **GetPartition** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudTableClient** objektum által jelképezett a table szolgáltatásügyfél.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy **CloudTable** hivatkozni kell a táblázat, amelyből keres az entitások képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozható létre egy **TableQuery** objektum a lekérdezést a **ahol** záradékban. Használja a **CustomerEntity** osztály- és a szakaszban bemutatott [egy teljes entitásköteget hozzáadása a táblához](#add-a-batch-of-entities-to-a-table), a következő kódrészletet lekérdezi a tábla összes entitásra vonatkozó ahol a  **PartitionKey** (az ügyfél utolsó neve) értéke "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. A hurkon belül hívja a **CloudTable.ExecuteQuerySegmented** módszert a lekérdezés objektum példánya létre, ha az előző lépésben átadásakor.  A **CloudTable.ExecuteQuerySegmented** metódus értéket ad vissza egy **TableContinuationToken** által - amikor **null** -azt jelzi, hogy nincsenek-e további entitások beolvasása. A hurkon belül használja egy másik hurok az ismétlés a visszaadott entitás. Az alábbi példakódban minden visszaadott entitás hozzáadandó listáját. Ha a hurok véget ér, a lista nézet megjelenítendő objektumnak átadott: 

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

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **GetPartition** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `GetPartition.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **első partíció** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Partíció beolvasása](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Entitás törlése

Ez a szakasz bemutatja, hogyan entitás törlése a táblázatból.

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment), és adatokat használ [egy teljes entitásköteget hozzáadása a táblához](#add-a-batch-of-entities-to-a-table). 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a hívott metódus **DeleteEntity** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **DeleteEntity** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudTableClient** objektum által jelképezett a table szolgáltatásügyfél.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Első egy **CloudTable** hivatkozni kell a táblázat, amelyből törli az entitást képviselő objektum. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy törlési művelet objektumot, amely fogad, származtatott felületnek entitásobjektumot **TableEntity**. Ebben az esetben használjuk a **CustomerEntity** osztály- és a szakaszban bemutatott [egy teljes entitásköteget hozzáadása a táblához](#add-a-batch-of-entities-to-a-table). Az entitás **ETag** érvényes értékre kell állítani.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. A törlési művelet végrehajtása.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. A nézetben megjelenítendő át az eredményt.

    ```csharp
    return View(result);
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **táblák**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **DeleteEntity** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `DeleteEntity.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **entitás törlése** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Egyetlen beolvasása](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure várólista-tároló és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
