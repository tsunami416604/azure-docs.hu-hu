---
title: Az Azure Blob storage használatának első lépései a Visual Studio használatával (ASP.NET Core)
description: Az Azure Blob storage használatának első lépései egy ASP.NET Core projektben a Visual Studióban, miután a Visual Studio csatlakoztatott szolgáltatásaival csatlakozott egy tárfiókhoz
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff221a32ff6c995d019b13f20ca2c3f9e2027f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980731"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob storage-szal és a Visual Studio-hoz kapcsolódó szolgáltatásokkal (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Az Azure Blob storage egy olyan szolgáltatás, amely a felhőben strukturálatlan adatokat tárol objektumokként vagy blobokként. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. A Blob storage bemutatása című témakörben olvashat [bővebben.](../storage/blobs/storage-blobs-introduction.md)

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET Core kódot néhány gyakori forgatókönyv, amely blob storage-t használ. Forgatókönyvek közé tartozik egy blob tároló létrehozása, és feltöltése, listázása, letöltése és törlése blobok.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztői környezet beállítását. Ez magában foglalja egy ASP.NET Model-View-Controller (MVC) alkalmazás létrehozását, a csatlakoztatott szolgáltatások kapcsolatának hozzáadását, a vezérlő hozzáadását és a szükséges névtér-irányelvek megadását.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC alkalmazásprojekt létrehozása

1. Nyissa meg a Visual Studiót.

1. A főmenüben válassza az**Új** > projekt **fájlja** > **parancsot.**

1. Az **Új projekt** párbeszédpanelen válassza a **Web** > **ASP.NET Core Web Application** > **AspNetCoreStorage**lehetőséget. Ezután kattintson az **OK** gombra.

    ![Képernyőkép a Visual Studio Új projekt párbeszédpaneléről](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Az **Új ASP.NET core webalkalmazás** párbeszédpanelen válassza a **.NET Core** > **ASP.NET Core 2.0** > **webalkalmazás (Model-View-Controller) lehetőséget.** Ezután kattintson az **OK** gombra.

    ![Képernyőkép: Új ASP.NET Core webalkalmazás párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Kapcsolatban álló szolgáltatások használata azure-tárfiókhoz való csatlakozáshoz

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre.

2. A helyi menüben válassza a Csatlakoztatott szolgáltatás **hozzáadása** > **parancsot.**

1. A **Csatlakoztatott szolgáltatások** párbeszédpanelen válassza a **Cloud Storage with Azure Storage (Felhőalapú tárolás az Azure Storage szolgáltatással)** lehetőséget, majd a **Konfigurálás**lehetőséget.

    ![Képernyőkép: Csatlakoztatott szolgáltatások párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Az **Azure Storage** párbeszédpanelen válassza ki az ehhez az oktatóanyaghoz használandó Azure storage-fiókot. Új Azure-tárfiók létrehozásához válassza **az Új tárfiók létrehozása**lehetőséget, és töltse ki az űrlapot. Miután kiválasztottegy meglévő tárfiókot, vagy létrehozott egy újat, válassza **a Hozzáadás**lehetőséget. A Visual Studio telepíti a NuGet csomagot az Azure Storage számára, és egy tárolási kapcsolati karakterláncot **az appsettings.json webhelyre.**

> [!TIP]
> Ha tudni szeretné, hogyan hozhat létre tárfiókot az [Azure Portalon,](https://portal.azure.com)olvassa el a Tárfiók létrehozása című [témakört.](../storage/common/storage-account-create.md)
>
> [Az Azure PowerShell,](../storage/common/storage-powershell-guide-full.md) [az Azure CLI](../storage/common/storage-azure-cli.md)vagy az [Azure Cloud Shell](../cloud-shell/overview.md)használatával is létrehozhat tárfiókot.


### <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **Megoldáskezelőben**kattintson a jobb gombbal **a Vezérlők elemre.**

2. A helyi menüben válassza a **Vezérlő hozzáadása** > **parancsot.**

    ![Képernyőkép a Megoldáskezelőről](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Az **Állványhozzáadása** párbeszédpanelen válassza az **MVC-vezérlő – Üres**lehetőséget, majd a **Hozzáadás**lehetőséget.

    ![Képernyőkép: Állványhozzáadása párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Az **Üres MVC-vezérlő hozzáadása** párbeszédpanelen nevezze el a *BlobsController vezérlőt,* és válassza **a Hozzáadás**lehetőséget.

    ![Képernyőkép: Üres MVC-vezérlő hozzáadása párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Adja hozzá `using` a fájlhoz a `BlobsController.cs` következő irányelveket:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Csatlakozás tárfiókhoz, és egy tárolóhivatkozás bekésezése

A blobtároló blobok és mappák beágyazott hierarchiája. A jelen dokumentum ban leírt lépések további lépéseiben egy blobtárolóra mutató hivatkozást igényel, ezért a kódot a saját újrafelhasználhatósági módszerében kell elhelyezni.

A következő lépések létrehoznak egy módszert a tárfiókhoz való csatlakozáshoz az **appsettings.json**kapcsolati karakterláncával. A lépések egy tárolóra mutató hivatkozást is létrehoznak. Az **appsettings.json** kapcsolati karakterlánc-beállítása `<storageaccountname>_AzureStorageConnectionString`a formátummal van elnevezve. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy **GetCloudBlobContainer** nevű metódust, amely **egy CloudBlobContainer értéket**ad vissza. Ügyeljen arra, `<storageaccountname>_AzureStorageConnectionString` hogy cserélje ki a tényleges nevét a kulcs **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Annak ellenére, *hogy a test-blob-tároló* még nem létezik, ez a kód létrehoz egy hivatkozást. Ez azért van így, hogy `CreateIfNotExists` a tároló a következő lépésben látható módszerrel hozható létre.

## <a name="create-a-blob-container"></a>Blobtároló létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre blobtárolót:

1. Adjon hozzá `CreateBlobContainer` egy metódust, amely egy értéket `ActionResult`ad vissza.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Szerezzen `CloudBlobContainer` be egy objektumot, amely a kívánt blobtároló nevére mutató hivatkozást jelöli. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hívja `CloudBlobContainer.CreateIfNotExists` meg a metódust a tároló létrehozásához, ha még nem létezik. A `CloudBlobContainer.CreateIfNotExists` metódus **igaz értéket** ad vissza, ha a tároló nem létezik, és sikeresen létrehozva. Ellenkező esetben a metódus **hamis**értéket ad vissza.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Frissítse `ViewBag` a blob tároló nevét.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A következőkben `CreateBlobContainer` a befejezett módszer látható:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Nézetek** mappára.

2. A helyi menüben válassza az Új mappa **hozzáadása** > **parancsot.** Nevezze el az új *mappát Blobs*. 

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, és kattintson a jobb gombbal **a Blobs**elemre.

4. A helyi menüben válassza a **Nézet hozzáadása** > **parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a Nézet nevéhez a **CreateBlobContainer** parancsot, és válassza a **Hozzáadás gombot.**

1. Nyissa `CreateBlobContainer.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **Megoldáskezelőben**bontsa ki `_Layout.cshtml`a**Megosztott** **nézetek** > mappát, és nyissa meg a programot.

1. Keresse meg a rendezetlen listát, `<ul class="nav navbar-nav">`hogy néz ki, mint ez: .  A lista `<li>` utolsó eleme után adja hozzá a következő HTML-kódot egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a **Blob Container létrehozása lehetőséget** az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Képernyőkép: Blob-tároló létrehozása](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Mint korábban `CloudBlobContainer.CreateIfNotExists` említettük, a metódus csak akkor ad vissza **igaz,** ha a tároló nem létezik, és jön létre. Ezért ha az alkalmazás akkor fut, amikor a tároló létezik, a metódus **hamis**értéket ad vissza.

## <a name="upload-a-blob-into-a-blob-container"></a>Blob feltöltése blobtárolóba

A [blob tároló létrehozásakor](#create-a-blob-container)töltsön fel fájlokat a tárolóba. Ez a szakasz végigvezeti egy helyi fájl feltöltése egy blob tárolóba. A lépések feltételezik, hogy van egy *teszt-blob-tároló*nevű blobtároló. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Karakterláncot visszaadó `UploadBlob` metódus hozzáadása.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. A `UploadBlob` metóduson belül `CloudBlobContainer` kap egy objektumot, amely a kívánt blob tároló nevére mutató hivatkozást jelöli. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Az Azure Storage támogatja a különböző blobtípusokat. Ez az oktatóanyag blokkblobokat használ. Blokkblobra mutató hivatkozás beolvasásához `CloudBlobContainer.GetBlockBlobReference` hívja meg a metódust.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > A blob neve része a blob lekéréséhez használt URL-címet, és lehet bármilyen karakterlánc, beleértve a fájl nevét.

1. Miután van egy blob hivatkozás, feltöltheti bármilyen adatfolyamot, hívja `UploadFromStream` meg a blob referenciaobjektum metódusát. A `UploadFromStream` metódus létrehozza a blobot, ha nem létezik, vagy felülírja, ha létezik. (Módosítsa * &lt;a feltöltendő fájl-feltöltési>* a feltöltendő fájl teljesen minősített elérési útjának.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    A következőkben `UploadBlob` látható a kitöltött módszer (a feltöltendő fájl teljesen minősített elérési úttal):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**bontsa ki `_Layout.cshtml`a**Megosztott** **nézetek** > mappát, és nyissa meg a programot.

1. A lista `<li>` utolsó eleme után adja hozzá a következő HTML-kódot egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza **a Blob feltöltése**lehetőséget. A szó *siker!* meg kell jelennie.
    
    ![A sikeres ellenőrzés képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>A blobok listázása egy blobtárolóban

Ez a szakasz bemutatja, hogyan listázza a blobok egy blob tárolóban. A mintakód hivatkozik a *test-blob-tároló* létre a szakaszban létrehozott, [Hozzon létre egy blob tárolót.](#create-a-blob-container)

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá `ListBlobs` egy metódust, amely egy értéket `ActionResult`ad vissza.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. A `ListBlobs` metóduson belül `CloudBlobContainer` lekell szereznie egy objektumot, amely a blob-tárolóra mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. A blobok listázásához egy blob `CloudBlobContainer.ListBlobsSegmentedAsync` tárolóban, használja a módszert. A `CloudBlobContainer.ListBlobsSegmentedAsync` metódus `BlobResultSegment`a . Ez `IListBlobItem` olyan objektumokat tartalmaz, `CloudPageBlob`amelyek `CloudBlobDirectory` a , vagy objektumokba `CloudBlockBlob`is eladhatók. A következő kódrészlet számba veszi a blobok egy blob tárolóban. Minden blob a megfelelő objektumra kerül, a típusa alapján. A neve (vagy URI esetén `CloudBlobDirectory`a) hozzáadódik egy listához.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    A következőkben `ListBlobs` a befejezett módszer látható:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, és kattintson a jobb gombbal **a Blobs**elemre.

2. A helyi menüben válassza a **Nézet hozzáadása** > **parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen `ListBlobs` írja be a nézet nevét, és válassza a **Hozzáadás gombot.**

1. Nyissa `ListBlobs.cshtml`meg a tartalmat, és cserélje le a tartalmát a következő kódra:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. A **Megoldáskezelőben**bontsa ki `_Layout.cshtml`a**Megosztott** **nézetek** > mappát, és nyissa meg a programot.

1. A lista `<li>` utolsó eleme után adja hozzá a következő HTML-kódot egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Futtassa az alkalmazást, és válassza **a Blobok listázása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Képernyőkép a Listablobokról](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Ez a szakasz bemutatja, hogyan tölthet le egy blobot. Megtarthatja a helyi tárolóban, vagy beolvashatja a tartalmat egy karakterláncba. A mintakód hivatkozik a *test-blob-tároló* létre a szakaszban létrehozott, [Hozzon létre egy blob tárolót.](#create-a-blob-container)

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Karakterláncot visszaadó `DownloadBlob` metódus hozzáadása.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. A `DownloadBlob` metóduson belül `CloudBlobContainer` lekell szereznie egy objektumot, amely a blob-tárolóra mutató hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Blob referenciaobjektum beszerezni `CloudBlobContainer.GetBlockBlobReference` a metódus hívása. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Blob letöltéséhez használja `CloudBlockBlob.DownloadToStream` a módszert. A következő kód átviszi a blob tartalmát egy adatfolyam-objektumba. Ezt az objektumot ezután egy helyi fájlban tartják meg. (Módosítsa * &lt;a helyi fájlnevet>* a blob letöltési helye szerinti pontosan minősített fájlnévre.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    A következőkben `ListBlobs` látható a befejezett módszer (a létrehozás alatt következő helyi fájl teljesen minősített elérési úttal):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**bontsa ki `_Layout.cshtml`a**Megosztott** **nézetek** > mappát, és nyissa meg a programot.

1. A lista `<li>` utolsó eleme után adja hozzá a következő HTML-kódot egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza **a Blob letöltése** a blob letöltéséhez. A `CloudBlobContainer.GetBlockBlobReference` metódusban megadott blob letöltődik a `File.OpenWrite` metódushívásban megadott helyre. A szöveg *siker!* meg kell jelennie a böngészőben. 

## <a name="delete-blobs"></a>Blobok törlése

A következő lépések bemutatják, hogyan lehet törölni egy blobot:

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Karakterláncot visszaadó `DeleteBlob` metódus hozzáadása.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. A `DeleteBlob` metóduson belül `CloudBlobContainer` lekell szereznie egy objektumot, amely a blob-tárolóra mutató hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Blob referenciaobjektum beszerezni `CloudBlobContainer.GetBlockBlobReference` a metódus hívása. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Blob törléséhez használja `Delete` a módszert.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    A `DeleteBlob` befejezett módszernek a következőképpen kell megjelennie:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**bontsa ki `_Layout.cshtml`a**Megosztott** **nézetek** > mappát, és nyissa meg a programot.

1. A lista `<li>` utolsó eleme után adja hozzá a következő HTML-kódot egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a Blob `CloudBlobContainer.GetBlockBlobReference` törlése **a** metódushívásban megadott blob törléséhez. A szöveg *siker!* meg kell jelennie a böngészőben. Válassza a böngésző **Vissza** gombját, majd a **Blobok listázása** lehetőséget, és ellenőrizze, hogy a blob már nincs-e a tárolóban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan tárolhatja, listázhatja és lekérheti a blobokat az Azure Storage-ban ASP.NET Core használatával. Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Table storage-szal és a Visual Studio-hoz kapcsolódó szolgáltatásokkal (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure Queue storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
