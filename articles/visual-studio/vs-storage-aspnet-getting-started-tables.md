---
title: Az Azure Table Storage használatának első lépései a Visual Studióval (ASP.NET)
description: Az Azure Table Storage használatának első lépései egy ASP.NET-projektben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979628"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure Table Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés

Az Azure Table Storage lehetővé teszi nagy mennyiségű strukturált adattárolás tárolását. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőn belüli és kívüli hitelesített hívásokat fogadja el. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET-kódokat néhány gyakori forgatókönyvhöz az Azure Table Storage-entitások használatával. Ilyen forgatókönyvek például a táblák létrehozása, valamint a táblák entitások hozzáadása, lekérdezése és törlése. 

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **megoldáskezelő**kattintson a jobb gombbal a **vezérlők**elemre, és a helyi menüben válassza a **Hozzáadás – >vezérlő**elemet.

    ![Vezérlő hozzáadása egy ASP.NET MVC-alkalmazáshoz](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Az **állvány hozzáadása** párbeszédpanelen válassza az **MVC 5 vezérlő – üres**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    ![MVC-vezérlő típusának megadása](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. A **vezérlő hozzáadása** párbeszédpanelen nevezze el a vezérlő *TablesController*, és válassza a **Hozzáadás**lehetőséget.

    ![Az MVC-vezérlő neve](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adja hozzá a következő *alkalmazás* -irányelveket a `TablesController.cs` fájlhoz:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Modell osztály létrehozása

A cikk számos példája egy **CustomerEntity**nevű **TableEntity**-származtatott osztályt használ. A következő lépések végigvezetik ennek az osztálynak a modell osztályként való deklarálása során:

1. A **megoldáskezelő**kattintson a jobb gombbal a **modellek**elemre, és a helyi menüben válassza a **Add->osztály**elemet.

1. Az **új elem hozzáadása** párbeszédpanelen nevezze el a **CustomerEntity**osztályt.

1. Nyissa `CustomerEntity.cs` meg a fájlt, és adja hozzá a következő **using** direktívát:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Módosítsa úgy az osztályt, hogy ha elkészült, akkor az osztály a következő kódban van deklarálva. Az osztály deklarál egy **CustomerEntity** nevű Entity osztályt, amely az ügyfél utónevét és vezetéknevét használja a partíciós kulcsként.

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

A következő lépések bemutatják, hogyan hozhat létre táblát:

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállítása](#set-up-the-development-environment)című témakör lépéseit. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **CreateTable** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **CreateTable** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa * &lt;a Storage-Account-Name>t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beolvasása egy Table Service-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy **CloudTable** -objektumot, amely a kívánt táblázat nevére mutató hivatkozást jelöl. A **CloudTableClient. GetTableReference** metódus nem hajt végre kérelmet a Table Storage szolgáltatással szemben. A hivatkozás azt adja vissza, hogy létezik-e a tábla. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Ha még nem létezik, hívja meg a **CloudTable. createifnotexists metódust** metódust a tábla létrehozásához. A **CloudTable. createifnotexists metódust** metódus **igaz** értéket ad vissza, ha a tábla nem létezik, és a létrehozása sikeresen megtörtént. Ellenkező esetben a rendszer **Hamis értéket** ad vissza.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Frissítse a **ViewBag** a tábla nevével.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **táblák**elemre, majd a helyi menüben válassza a **Hozzáadás – >nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **CreateTable** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `CreateTable.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek->megosztott** mappát, és nyissa meg a parancsot `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és kattintson a **create Table (tábla létrehozása** ) elemre az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Tábla létrehozása](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Ahogy azt korábban említettük, a **CloudTable. createifnotexists metódust** metódus **igaz** értéket ad vissza, ha a tábla nem létezik, és létrejön. Ezért ha az alkalmazást a tábla létezése során futtatja, a metódus **hamis**értéket ad vissza. Ha többször szeretné futtatni az alkalmazást, törölnie kell a táblát az alkalmazás újbóli futtatása előtt. A tábla törlése a **CloudTable. Delete** metódussal végezhető el. A táblázatot a [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)használatával is törölheti.  

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Az *entitások* a\# **TableEntity**származtatott egyéni osztály használatával képezhetők le C-objektumokra. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Ebből a szakaszból megtudhatja, hogyan határozhat meg egy entitás osztályt, amely az ügyfél utónevét és vezetéknevét használja a partíciós kulcsként. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb fokú méretezhetőségét teszi lehetővé. A Table szolgáltatásban tárolni kívánt tulajdonságoknak a tulajdonságnak egy támogatott típusú nyilvános tulajdonságnak kell lennie, amely az értékek beállítását és beolvasását is lehetővé teszi.
Az Entity osztálynak deklarálnia *kell* egy nyilvános, paraméter nélküli konstruktort.

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállítása](#set-up-the-development-environment)című témakör lépéseit.

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a következő irányelvet, hogy a `TablesController.cs` fájlban lévő kód hozzáférhessen a **CustomerEntity** osztályhoz:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adjon hozzá egy **AddEntity** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **AddEntity** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa * &lt;a Storage-Account-Name>t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beolvasása egy Table Service-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy olyan **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyhez hozzá kívánja adni az új entitást. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozza létre és inicializálja a **CustomerEntity** osztályt.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Hozzon létre egy **TableOperation** objektumot, amely beszúrja az ügyfél entitást.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Hajtsa végre az INSERT műveletet a **CloudTable. Execute** metódus meghívásával. A művelet eredményét a **ableresult. HttpStatusCode** tulajdonság vizsgálatával ellenőrizheti. A 2xx állapotkód azt jelzi, hogy az ügyfél által kért művelet sikeresen feldolgozva. Az új entitások sikeres beszúrása például 204-as HTTP-állapotkódot eredményez, ami azt jelenti, hogy a művelet feldolgozása sikeres volt, és a kiszolgáló nem adott vissza tartalmat.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Frissítse a **ViewBag** a tábla nevével, és a beszúrási művelet eredményét.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **táblák**elemre, majd a helyi menüben válassza a **Hozzáadás – >nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **AddEntity** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `AddEntity.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. A **megoldáskezelő**bontsa ki a **nézetek->megosztott** mappát, és nyissa meg a parancsot `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **entitás hozzáadása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Entitás hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Ellenőrizze, hogy az entitás hozzá lett-e adva a szakasz lépéseit követve, [egyetlen entitás beolvasása](#get-a-single-entity). A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is használhatja a táblák összes entitásának megtekintéséhez.

## <a name="add-a-batch-of-entities-to-a-table"></a>Entitások kötegének hozzáadása egy táblához

Azon kívül, hogy [egyszerre hozzá tud adni egy entitást egy táblához](#add-an-entity-to-a-table), entitásokat is hozzáadhat a Batch szolgáltatáshoz. A Batch entitások hozzáadása csökkenti a kód és az Azure Table szolgáltatás közötti adatváltások számát. A következő lépések bemutatják, hogyan adhat hozzá több entitást egy táblához egyetlen INSERT művelettel:

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállítása](#set-up-the-development-environment)című témakör lépéseit.

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **AddEntities** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **AddEntities** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa * &lt;a Storage-Account-Name>t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beolvasása egy Table Service-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy olyan **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyhez hozzá kívánja adni az új entitásokat. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Néhány ügyfél-objektum példányának létrehozása a szakaszban bemutatott **CustomerEntity** modell osztály alapján, [entitás hozzáadása táblához](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. **Tablebatchoperation művelethez** objektum beolvasása.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Entitások hozzáadása a Batch INSERT művelet objektumhoz.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Futtassa a Batch INSERT műveletet a **CloudTable. ExecuteBatch** metódus meghívásával.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. A **CloudTable. ExecuteBatch** metódus olyan **ableresult** -objektumok listáját adja vissza, amelyekben minden egyes **ableresult** -objektum megvizsgálható az egyes műveletek sikerességének vagy meghibásodásának megállapításához. Ebben a példában adja át a listát egy nézetnek, és hagyja, hogy a nézet az egyes műveletek eredményeit jelenítse meg. 
 
    ```csharp
    return View(results);
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **táblák**elemre, majd a helyi menüben válassza a **Hozzáadás – >nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **AddEntities** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `AddEntities.cshtml`, és módosítsa úgy, hogy az a következőképpen néz ki.

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

1. A **megoldáskezelő**bontsa ki a **nézetek->megosztott** mappát, és nyissa meg a parancsot `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **entitások hozzáadása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Entitások hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Ellenőrizze, hogy az entitás hozzá lett-e adva a szakasz lépéseit követve, [egyetlen entitás beolvasása](#get-a-single-entity). A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is használhatja a táblák összes entitásának megtekintéséhez.

## <a name="get-a-single-entity"></a>Egyetlen entitás beolvasása

Ez a szakasz azt szemlélteti, hogyan lehet egyetlen entitást beolvasni egy táblából az entitás sormező és partíciós kulcsa alapján. 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [fejlesztési környezet beállítása](#set-up-the-development-environment)című témakör lépéseit, és az adatok egy [köteg hozzáadása egy táblához](#add-a-batch-of-entities-to-a-table)elemet használja. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **GetSingle** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **GetSingle** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa * &lt;a Storage-Account-Name>t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beolvasása egy Table Service-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Olyan **CloudTable** objektum beolvasása, amely az entitást lekérdező táblára mutató hivatkozást jelöl. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy lekérési művelet objektumot, amely a **TableEntity**származtatott entitás objektumot veszi fel. Az első paraméter a *partitionKey*, a második paraméter pedig a *rowKey*. A **CustomerEntity** osztály és az [entitások hozzáadása a táblához](#add-a-batch-of-entities-to-a-table)című szakaszban bemutatottak alapján a következő kódrészlet lekérdezi a **CustomerEntity** entitás tábláját a "Smith" *partitionKey* értékkel és a "ben" *rowKey* -értékkel:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. A lekérési művelet végrehajtása.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Adja át az eredményt a nézet megjelenítéséhez.

    ```csharp
    return View(result);
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **táblák**elemre, majd a helyi menüben válassza a **Hozzáadás – >nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **GetSingle** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `GetSingle.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

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

1. A **megoldáskezelő**bontsa ki a **nézetek->megosztott** mappát, és nyissa meg a parancsot `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **egyszeri beolvasás** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Egyszeri lekérdezés](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Egy partíció összes entitásának beolvasása

Ahogy azt a szakaszban is említettük, [vegyen fel egy entitást egy táblázatba](#add-an-entity-to-a-table), egy partíció és egy sor kulcsának kombinációját egyedileg azonosítva egy tábla entitását. Az ugyanazzal a partíciós kulccsal rendelkező entitások gyorsabban kérhetők le, mint a különböző partíciós kulcsokkal rendelkező entitások. Ez a szakasz azt szemlélteti, hogyan lehet lekérdezni egy táblát a megadott partíció összes entitására vonatkozóan.  

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [fejlesztési környezet beállítása](#set-up-the-development-environment)című témakör lépéseit, és az adatok egy [köteg hozzáadása egy táblához](#add-a-batch-of-entities-to-a-table)elemet használja. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **GetPartition** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **GetPartition** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa * &lt;a Storage-Account-Name>t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beolvasása egy Table Service-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy olyan **CloudTable** objektumot, amely az entitások beolvasására szolgáló táblára mutató hivatkozást jelöl. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **TableQuery** -objektum példányának létrehozása a **Where** záradékban megadott lekérdezéssel. A **CustomerEntity** osztály és az [entitások hozzáadása a táblához](#add-a-batch-of-entities-to-a-table)című szakaszban bemutatottak alapján a következő kódrészlet lekérdezi az összes olyan entitás tábláját, ahol a **PartitionKey** (az ügyfél vezetékneve) a "Kovács" értékkel rendelkezik:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Egy hurkon belül hívja meg a **CloudTable. ExecuteQuerySegmented** metódust az előző lépésben létrehozott lekérdezési objektum átadásával.  A **CloudTable. ExecuteQuerySegmented** metódus egy olyan **TableContinuationToken** objektumot ad vissza, amely – ha **Null** – azt jelzi, hogy nincsenek lekérdezhető entitások. A hurokon belül egy másik hurok használatával ismételje meg a visszaadott entitásokat. A következő példában a rendszer minden visszaadott entitást hozzáad egy listához. A hurok vége után a rendszer átadja a listát a megjelenítendő nézetnek: 

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

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **táblák**elemre, majd a helyi menüben válassza a **Hozzáadás – >nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **GetPartition** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `GetPartition.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

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

1. A **megoldáskezelő**bontsa ki a **nézetek->megosztott** mappát, és nyissa meg a parancsot `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **partíció beolvasása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Partíció beolvasása](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Entitás törlése

Ez a szakasz azt szemlélteti, hogyan törölhet entitásokat egy táblából.

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [fejlesztési környezet beállítása](#set-up-the-development-environment)című témakör lépéseit, és az adatok egy [köteg hozzáadása egy táblához](#add-a-batch-of-entities-to-a-table)elemet használja. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **DeleteEntity** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **DeleteEntity** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa * &lt;a Storage-Account-Name>t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beolvasása egy Table Service-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy olyan **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyről törölni kívánja az entitást. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy törlési művelet objektumot, amely a **TableEntity**származtatott entitás objektumot veszi fel. Ebben az esetben a **CustomerEntity** osztályt használjuk, és az [entitások köteg hozzáadása táblához](#add-a-batch-of-entities-to-a-table)című szakaszban ismertetett adatmennyiséget használjuk. Az entitás **ETAG** érvényes értékre kell beállítani.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Hajtsa végre a törlési műveletet.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Adja át az eredményt a nézet megjelenítéséhez.

    ```csharp
    return View(result);
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **táblák**elemre, majd a helyi menüben válassza a **Hozzáadás – >nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **DeleteEntity** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `DeleteEntity.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

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

1. A **megoldáskezelő**bontsa ki a **nézetek->megosztott** mappát, és nyissa meg a parancsot `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **entitás törlése** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Egyszeri lekérdezés](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure üzenetsor-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
