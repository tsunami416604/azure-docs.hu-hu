---
title: Ismerkedés az Azure table storage használatával Visual Studio (ASP.NET)
description: Az Azure table storage használatának első lépései egy ASP.NET projektben a Visual Studióban, miután a Visual Studio Connected Services használatával egy tárfiókhoz csatlakoztak
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979628"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure table storage és a Visual Studio Connected Services használatával (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés

Az Azure Table storage lehetővé teszi, hogy nagy mennyiségű strukturált adatok tárolására. A szolgáltatás egy NoSQL-adattár, amely fogadja a hitelesített hívásokat az Azure-felhőn belülről és kívülről. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET kódot néhány gyakori forgatókönyvhez az Azure table storage entitások használatával. Ezek a forgatókönyvek közé tartozik a tábla létrehozása, valamint táblaentitások hozzáadása, lekérdezése és törlése. 

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure tárfiók](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Vezérlők parancsra,** és a helyi menüben válassza a **Hozzáadás >vezérlő parancsot.**

    ![Vezérlő hozzáadása ASP.NET MVC-alkalmazáshoz](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Az **Állvány hozzáadása** párbeszédpanelen válassza az **MVC 5 vezérlő – Üres**lehetőséget, majd a **Hozzáadás**lehetőséget.

    ![MVC-vezérlő típusának megadása](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. A **Vezérlő hozzáadása** párbeszédpanelen nevezze el a Controller *TablesControllert*, és válassza a **Hozzáadás**lehetőséget.

    ![Az MVC-vezérlő elnevezése](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Adja hozzá a következő `TablesController.cs` *irányelveket* a fájlhoz:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Modellosztály létrehozása

A cikkben szereplő példák közül sok egy **TableEntity**-derived class nevű **CustomerEntity**nevű. A következő lépések végigvezetik az osztály mintaosztályként történő deklarálásán:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Modellek**parancsra, és a helyi menüben válassza a **Hozzáadás >osztály parancsát.**

1. Az **Új elem hozzáadása** párbeszédpanelen nevezze el az osztályt **CustomerEntity**.

1. Nyissa `CustomerEntity.cs` meg a fájlt, és adja hozzá a következőt az irányelv **használatával:**

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Módosítsa az osztályt úgy, hogy ha elkészült, az osztály a következő kódban megadott módon deklarálva lesz. Az osztály deklarál egy **CustomerEntity** nevű entitásosztályt, amely az ügyfél utónevét használja sorkulcsként, a vezetéknevet pedig partíciókulcsként.

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

Az alábbi lépések bemutatják, hogyan hozhat létre táblázatot:

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a Fejlesztői környezet beállítása című szakasz [lépéseit.](#set-up-the-development-environment) 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **CreateTable** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **CreateTable** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beszereznie egy táblaszolgáltatás-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy **CloudTable** objektumot, amely a kívánt táblanévre való hivatkozást jelöli. A **CloudTableClient.GetTableReference** metódus nem kér a táblatárolóval szemben. A hivatkozás arról van-e szó, hogy a tábla létezik-e vagy sem. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hívja meg a **CloudTable.CreateIfNotExists** metódust a tábla létrehozásához, ha még nem létezik. A **CloudTable.CreateIfNotExists** metódus **igaz** értéket ad vissza, ha a tábla nem létezik, és sikeresen létrehozva. Ellenkező esetben **a rendszer hamis** értéket ad vissza.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Frissítse a **ViewBag-ot** a tábla nevével.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Táblázatok parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a nézet nevéhez a **CreateTable** parancsot, és válassza a **Hozzáadás gombot.**

1. Nyissa `CreateTable.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és a **Táblázat létrehozása gombra** a következő képernyőképhez hasonló eredmények megtekintéséhez válassza a táblázat létrehozása lehetőséget:
  
    ![Tábla létrehozása](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Mint korábban említettük, a **CloudTable.CreateIfNotExists** metódus csak akkor ad vissza **igaz értéket,** ha a tábla nem létezik, és létrejön. Ezért ha az alkalmazást akkor futtatja, amikor a tábla létezik, a metódus **hamis**értéket ad vissza. Az alkalmazás többszöri futtatásához törölnie kell a táblát, mielőtt újra futtatna az alkalmazást. A tábla törlése a **CloudTable.Delete** metódussal végezhető el. A táblázatot az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)ben is törölheti.  

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

*Az entitások* \# a **TableEntity**objektumból származtatott egyéni osztály használatával lesznek c objektumokra. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Ebben a szakaszban láthatja, hogyan definiálhat egy entitásosztályt, amely az ügyfél utónevét használja sorkulcsként és vezetéknevet partíciókulcsként. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb fokú méretezhetőségét teszi lehetővé. A table szolgáltatásban tárolandó bármely tulajdonság esetében a tulajdonságnak támogatott típusú nyilvános tulajdonságnak kell lennie, amely mind a beállítási, mind a beolvasási értékeket elérhetővé teszi.
Az *entitásosztálynak* nyilvános paraméter nélküli konstruktot kell deklarálnia.

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a Fejlesztői környezet beállítása című szakasz [lépéseit.](#set-up-the-development-environment)

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adja hozzá a következő direktívát, hogy a `TablesController.cs` fájlban lévő kód hozzáférhessen a **CustomerEntity** osztályhoz:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Adjon hozzá egy **AddEntity** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Az **AddEntity** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beszereznie egy táblaszolgáltatás-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Kapjon egy **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyhez hozzá adja az új entitást. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. A **CustomerEntity** osztály példányosítása és inicializálása.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Hozzon létre egy **TableOperation** objektumot, amely beszúrja a vevő entitást.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. A beszúrási művelet végrehajtása a **CloudTable.Execute** metódus hívásával. A művelet eredményét a **TableResult.HttpStatusCode** tulajdonság vizsgálatával ellenőrizheti. A 2xx állapotkód azt jelzi, hogy az ügyfél által kért művelet feldolgozása sikeresen megtörtént. Például az új entitások sikeres beszúrása 204-es HTTP-állapotkódot eredményez, ami azt jelenti, hogy a művelet feldolgozása sikeresen megtörtént, és a kiszolgáló nem adott vissza tartalmat.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Frissítse a **ViewBag-ot** a tábla nevével és a beszúrási művelet eredményeivel.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Táblázatok parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a Nézet nevéhez tartozó **Hozzáadás entitás** t, majd kattintson a **Hozzáadás gombra.**

1. Nyissa `AddEntity.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **Entitás hozzáadása lehetőséget** az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Entitás hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Ellenőrizheti, hogy az entitás taválya a szakaszban található, [egyetlen entitás beszerez és győződjön meg.](#get-a-single-entity) A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) segítségével is megtekintheti a táblák összes entitását.

## <a name="add-a-batch-of-entities-to-a-table"></a>Entitások kötegének hozzáadása táblához

Amellett, hogy egyszerre csak [entitást adhat hozzá a táblához,](#add-an-entity-to-a-table)entitásokat is hozzáadhat a köteghez. Entitások kötegben hozzáadása csökkenti a kód és az Azure table szolgáltatás közötti oda-vissza utak számát. A következő lépések azt mutatják be, hogyan lehet több entitást hozzáadni egy táblához egyetlen beszúrási művelettel:

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a Fejlesztői környezet beállítása című szakasz [lépéseit.](#set-up-the-development-environment)

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **AddEntities** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Az **AddEntities** metóduson belül szerezzen be egy **CloudStorageAccount-objektumot,** amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beszereznie egy táblaszolgáltatás-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyhez hozzá adja az új entitásokat. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Egyes ügyfélobjektumok példányosítson a **csoportban** bemutatott CustomerEntity modellosztály alapján, amely [entitás hozzáadása táblához](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. **TableBatchOperation** objektum beszereznie.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Entitások hozzáadása a kötegbeszúrási műveletobjektumhoz.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. A batch insert művelet végrehajtása a **CloudTable.ExecuteBatch** metódus hívásával.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. A **CloudTable.ExecuteBatch** metódus a **TableResult** objektumok listáját adja vissza, ahol minden **TableResult** objektum megvizsgálható az egyes műveletek sikeresse vagy sikertelenssé tétele érdekében. Ebben a példában adja át a listát egy nézetnek, és hagyja, hogy a nézet megjelenítse az egyes műveletek eredményeit. 
 
    ```csharp
    return View(results);
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Táblázatok parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a Nézet nevéhez **tartozó Entitások** parancsot, és válassza a **Hozzáadás gombot.**

1. Nyissa `AddEntities.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következőképpen jelenjenek meg.

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

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **Entitások hozzáadása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Entitások hozzáadása](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Ellenőrizheti, hogy az entitás taválya a szakaszban található, [egyetlen entitás beszerez és győződjön meg.](#get-a-single-entity) A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) segítségével is megtekintheti a táblák összes entitását.

## <a name="get-a-single-entity"></a>Egyetlen entitás beszereznie

Ez a szakasz bemutatja, hogyan lehet egyetlen entitást beszerezni egy táblából az entitás sorkulcsa és partíciókulcsa használatával. 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte [a Fejlesztői környezet beállítása](#set-up-the-development-environment)című szakasz lépéseit , és az [Entitások kötegének hozzáadása táblához](#add-a-batch-of-entities-to-a-table)című adatokat használja. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **GetSingle** nevű metódust, amely **actionresult-ot**ad vissza.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **GetSingle** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beszereznie egy táblaszolgáltatás-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Beszerezni egy **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyből az entitást beszerzi. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Olyan lekérési műveletobjektum létrehozása, amely a **TableEntity**objektumból származtatott entitásobjektumot veszi fel. Az első paraméter a *partitionKey*, a második paraméter pedig a *rowKey*. A **Csoportban** bemutatott CustomerEntity osztály és adatok használatával [entitások kötegének hozzáadása táblához](#add-a-batch-of-entities-to-a-table)a következő kódrészlet lekérdezi egy "Kovács" *partíciókulcs-értékkel* rendelkező **CustomerEntity** entitás tábláját, és "Ben" *sorkulcs-értékkel:*

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. A beolvasási művelet végrehajtása.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Adja át az eredményt a nézetnek a megjelenítéshez.

    ```csharp
    return View(result);
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Táblázatok parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a **GetSingle** értéket a nézet nevéhez, és válassza a **Hozzáadás gombot.**

1. Nyissa `GetSingle.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

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

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **Egyetlen beszerezni** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Egyetlen személyes beszerezés](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>A partíció összes entitásának beszereznie

Aszakaszban említettek szerint [egy entitás hozzáadása egy táblához,](#add-an-entity-to-a-table)egy partíció és egy sorkulcs kombinációja egyedileg azonosítja a táblában lévő entitást. Az azonos partíciókulccsal rendelkező entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsokkal rendelkező entitások. Ez a szakasz bemutatja, hogyan lehet egy táblát lekérdezni egy adott partíció összes entitásához.  

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte [a Fejlesztői környezet beállítása](#set-up-the-development-environment)című szakasz lépéseit , és az [Entitások kötegének hozzáadása táblához](#add-a-batch-of-entities-to-a-table)című adatokat használja. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **GetPartition** nevű metódust, amely **ActionResult eredményt**ad vissza.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **GetPartition** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beszereznie egy táblaszolgáltatás-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Szerezzen be egy **CloudTable** objektumot, amely arra a táblára hivatkozik, amelyből az entitásokat beszerzi. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. A Query **objektum** példányosítása a **Hol** záradékban a lekérdezést adja meg. A **CustomerEntity** osztály és a szakaszban bemutatott adatok [használatával Entitások kötegének hozzáadása egy táblához](#add-a-batch-of-entities-to-a-table), a következő kódrészlet lekérdezi a táblát minden olyan entitásra, ahol a **PartitionKey** (ügyfél vezetékneve) értéke "Kovács":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Egy cikluson belül hívja meg a **CloudTable.ExecuteQuerySegmented** metódust, amely az előző lépésben példányosított lekérdezési objektumot adja át.  A **CloudTable.ExecuteQuerysegmented** metódus egy **TableContinuationToken** objektumot ad vissza, amely - ha **null** - azt jelzi, hogy nincs több beolvasni kívánt entitás. A cikluson belül használjon egy másik hurkot a visszaadott entitások ismétléséhez. A következő kódpéldában minden visszaadott entitás hozzáadódik egy listához. Miután a hurok véget ér, a lista átkerül egy nézetbe a következő nézetben: 

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

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Táblázatok parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **GetPartition** értéket a nézet nevéhez, és válassza a **Hozzáadás gombot.**

1. Nyissa `GetPartition.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

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

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és a **Partíció bekerülése gombra** a következő képernyőképhez hasonló eredmények megtekintéséhez válassza a Partíció beszerezhető jelölőnégyzetet:
  
    ![Partíció bekéselése](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Entitás törlése

Ez a szakasz azt mutatja be, hogyan lehet egy entitást törölni egy táblából.

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte [a Fejlesztői környezet beállítása](#set-up-the-development-environment)című szakasz lépéseit , és az [Entitások kötegének hozzáadása táblához](#add-a-batch-of-entities-to-a-table)című adatokat használja. 

1. Nyissa meg az `TablesController.cs` fájlt.

1. Adjon hozzá egy **DeleteEntity** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **DeleteEntity** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudTableClient** objektum beszereznie egy táblaszolgáltatás-ügyfelet jelöl.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Kapjon egy **CloudTable** objektumot, amely arra a táblára mutató hivatkozást jelöl, amelyből az entitást törlésre kerül. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Hozzon létre egy törlési műveletobjektumot, amely a **TableEntity**objektumból származtatott entitásobjektumot veszi át. Ebben az esetben a **CustomerEntity** osztályt és a [táblához entitások kötegének hozzáadása](#add-a-batch-of-entities-to-a-table)című szakaszban bemutatott adatokat használjuk. Az entitás **ETag** értékét érvényes értékre kell állítani.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. A törlési művelet végrehajtása.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Adja át az eredményt a nézetnek a megjelenítéshez.

    ```csharp
    return View(result);
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Táblázatok parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a Nézet nevéhez tartozó **DeleteEntity** parancsot, és válassza a **Hozzáadás gombot.**

1. Nyissa `DeleteEntity.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

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

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **Entitás törlése lehetőséget** az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Egyetlen személyes beszerezés](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure blob storage-szal és a Visual Studio Connected Services szolgáltatással (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure várólista-tárolásával és a Visual Studio Connected Services szolgáltatással (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
