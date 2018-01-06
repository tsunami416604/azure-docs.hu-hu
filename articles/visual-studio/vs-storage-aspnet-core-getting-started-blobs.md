---
title: "Ismerkedés az Azure Blob storage és a Visual Studio kapcsolódó szolgáltatások (az ASP.NET Core) |} Microsoft Docs"
description: "Első lépések a Visual Studióban, az ASP.NET Core projektben Azure Blob storage használatával csatlakoztassa egy tárfiókot a Visual Studio használatával kapcsolódó szolgáltatások"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET mag)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Az Azure Blob storage egy olyan szolgáltatás, a felhőben strukturálatlan adatokat tároló objektumokat vagy a BLOB. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez az oktatóanyag bemutatja, hogyan írhat kódot az ASP.NET Core olyan gyakori forgatókönyveket tartalmaz, amelyek használják a Blob Storage tárolóban. Forgatókönyvek például a blob tároló, létrehozása és feltöltése, listázása, letöltése és blobok törlése.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztési környezet létrehozása. Ez magában foglalja az ASP.NET Model-View-Controller (MVC) alkalmazás létrehozása, kapcsolódó szolgáltatások-kapcsolat hozzáadása, a vezérlő hozzáadása, és adja meg a szükséges névtér irányelvek.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC alkalmazás projekt létrehozása

1. Nyissa meg a Visual Studiót.

1. Válassza ki a főmenü **fájl** > **új** > **projekt**.

1. Az a **új projekt** párbeszédpanelen jelölje ki **webes** > **ASP.NET Core webalkalmazás** > **AspNetCoreStorage**. Válassza ki **OK**.

    ![Képernyőfelvétel a Visual Studio új projekt párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Az a **új ASP.NET Core webalkalmazás** párbeszédpanelen jelölje ki **.NET Core** > **ASP.NET Core 2.0** > **webalkalmazás () Model-View-Controller)**. Válassza ki **OK**.

    ![Képernyőfelvétel az új ASP.NET Core webalkalmazás párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Kapcsolódó szolgáltatások segítségével csatlakozzon az Azure storage-fiók

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre.

2. Válassza ki a helyi menüből **Hozzáadás** > **kapcsolódó szolgáltatás**.

1. Az a **kapcsolódó szolgáltatások** párbeszédpanelen jelölje ki **felhőalapú tárolás az Azure Storage**, majd válassza ki **konfigurálása**.

    ![Szolgáltatások csatlakoztatott képernyőkép párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Az a **Azure Storage** párbeszédpanelen jelölje ki az Azure storage-fiók ebben az oktatóanyagban használt. Egy új Azure storage-fiók létrehozásához válassza **hozzon létre egy új Tárfiókot**, töltse ki az űrlapot. Válasszon egy meglévő tárfiókot használ, vagy új létrehozása után **Hozzáadás**. A Visual Studio telepíti a NuGet-csomagot az Azure Storage és a tároló kapcsolati karakterláncának **appsettings.json**.

> [!TIP]
> További információt a storage-fiók létrehozása a [Azure-portálon](https://portal.azure.com), lásd: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> A storage-fiók használatával is létrehozhat [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md), vagy [Azure Cloud rendszerhéj](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Hozzon létre az MVC-vezérlő 

1. A **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**.

2. Válassza ki a helyi menüből **Hozzáadás** > **vezérlő**.

    ![Képernyőkép a Megoldáskezelőben](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Az a **hozzáadása Scaffold** párbeszédpanelen jelölje ki **MVC-vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![Scaffold hozzáadása képernyőkép párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Az a **üres MVC-vezérlő hozzáadása** párbeszédablakban nevezze el a tartományvezérlő *BlobsController*, és válassza ki **Hozzáadás**.

    ![Képernyőfelvétel a hozzáadása üres MVC-vezérlő párbeszédpanel](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Adja hozzá a következő `using` irányelvek a `BlobsController.cs` fájlt:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>A tárfiók csatlakozhat, és a tároló hivatkozását beolvasása

Egy blob-tároló blobokat és mappák beágyazott hierarchiája. A jelen dokumentumban leírt lépések többi elő egy blob-tárolóba, hogy a kód a saját módszer újrahasznosításának kell helyezni.

Az alábbi lépéseket a kapcsolati karakterlánc használatával kapcsolódni a tárfiók metódus létrehozása **appsettings.json**. A lépéseket is létrehozhat egy tárolót mutató hivatkozás. A kapcsolati karakterlánc beállítása a **appsettings.json** formátumban nevű `<storageaccountname>_AzureStorageConnectionString`. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus **GetCloudBlobContainer** , amely adja vissza egy **CloudBlobContainer**. Ügyeljen arra, hogy a csere `<storageaccountname>_AzureStorageConnectionString` a kulcsot a tényleges nevével **Web.config**.
    
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
> Annak ellenére, hogy *-blob-tároló* nem létezik, ez a kód egy hivatkozást hoz létre. Ez a helyzet a tároló hozhatja létre a `CreateIfNotExists` metódus a következő lépésben látható.

## <a name="create-a-blob-container"></a>A blob-tároló létrehozása

A következő lépések bemutatják egy blob-tároló létrehozása:

1. Adja hozzá a hívott metódus `CreateBlobContainer` , amely visszaadja az `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Első egy `CloudBlobContainer` objektum, amely a kívánt blobtároló neve hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hívja a `CloudBlobContainer.CreateIfNotExists` metódus a tároló létrehozása, ha még nem létezik. A `CloudBlobContainer.CreateIfNotExists` metódus beolvasása **igaz** , ha a tároló nem létezik, és sikeresen létrejött. Ellenkező esetben a metódus visszaadja **hamis**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Frissítés `ViewBag` a blob-tároló nevét.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A következő példa a befejezett `CreateBlobContainer` módszert:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **nézetek** mappát.

2. Válassza ki a helyi menüből **Hozzáadás** > **új mappa**. Az új mappa neve *Blobok*. 

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappára, majd kattintson a jobb gombbal **Blobok**.

4. Válassza ki a helyi menüből **Hozzáadás** > **nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg a **CreateBlobContainer** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `CreateBlobContainer.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Keresse meg a rendezetlen lista, amely a következőképpen néz ki: `<ul class="nav navbar-nav">`.  Után utolsó `<li>` elem a listában, adja hozzá egy másik navigációs menü elem hozzáadása a következő HTML-KÓDBAN:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Blob-tároló létrehozása** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Blob-tároló létrehozása képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Ahogy korábban említettük a `CloudBlobContainer.CreateIfNotExists` metódus beolvasása **igaz** csak a tároló nem létezik és jön létre. Ezért, ha az alkalmazás fut, ha a tároló létezik, a metódus visszaadja **hamis**.

## <a name="upload-a-blob-into-a-blob-container"></a>Egy blob feltöltése a blob-tárolóba

Ha a [blob tároló jön létre](#create-a-blob-container), fájlok feltöltése a tárolóba. Ez a szakasz végigvezeti egy helyi fájl feltöltése a blob-tároló. A lépések azt feltételezik, hogy van egy blob-tároló nevű *-blob-tároló*. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus `UploadBlob` , amely egy karakterláncot ad vissza.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Belül a `UploadBlob` módszer, lekérni egy `CloudBlobContainer` objektum, amely a kívánt blobtároló neve hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Az Azure storage másik blob-típusokat támogatja. Ez az oktatóanyag a blokkblobokhoz használja. Egy hivatkozást a blokkblob lekéréséhez hívja meg a `CloudBlobContainer.GetBlockBlobReference` metódust.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > A blob nevének beolvasása a blob URL-CÍMÉT részét képezi, és lehet bármilyen karakterlánc, beleértve a fájl nevét.

1. Miután egy blobhivatkozást, feltöltheti bármely adatfolyam azt a blob referenciaobjektum meghívásával `UploadFromStream` metódust. A `UploadFromStream` hoz létre a blob nem létezik, vagy felülírja, ha már létezik. (Változás  *&lt;fájlfeltöltés >* egy teljesen minősített feltölteni kívánt fájl elérési útját.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    A következő példa a befejezett `UploadBlob` metódust használ (a feltölteni kívánt fájl teljes elérési útja):

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

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó `<li>` elem a listában, adja hozzá egy másik navigációs menü elem hozzáadása a következő HTML-KÓDBAN:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza ki **feltöltése a blob**. A word *sikeres!* meg kell jelennie.
    
    ![Sikeres ellenőrzés képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>A blob-tárolóban lévő blobok listázása

Ez a szakasz bemutatja, hogyan a blob-tárolóban lévő blobok listázásához. A minta kód hivatkozásokat a *-blob-tároló* az szakaszban létrehozott [blob tárolókat hozhat létre](#create-a-blob-container).

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus `ListBlobs` , amely visszaadja az `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Belül a `ListBlobs` módszer, lekérni egy `CloudBlobContainer` objektum, amely a blob-tárolóba való hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. A blob-tárolóban lévő blobok listázásához, használja a `CloudBlobContainer.ListBlobsSegmentedAsync` metódust. A `CloudBlobContainer.ListBlobsSegmentedAsync` metódus értéket ad vissza egy `BlobResultSegment`. Ez tartalmazza `IListBlobItem` is konvertálható objektumok `CloudBlockBlob`, `CloudPageBlob`, vagy `CloudBlobDirectory` objektumok. A következő kódrészletet a blob-tároló összes blobjának enumerálása. Minden egyes blob típusúvá van a megfelelő objektumot, a típusa alapján. A nevét (vagy az URI egy `CloudBlobDirectory`) hozzá van adva egy listájához.

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
    A következő példa a befejezett `ListBlobs` módszert:

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

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappára, majd kattintson a jobb gombbal **Blobok**.

2. Válassza ki a helyi menüből **Hozzáadás** > **nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg a `ListBlobs` a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `ListBlobs.cshtml`, és cserélje ki annak tartalmát az alábbira:

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

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó `<li>` elem a listában, adja hozzá egy másik navigációs menü elem hozzáadása a következő HTML-KÓDBAN:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Futtassa az alkalmazást, és válassza ki **blobok listázása** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Képernyőfelvétel a lista blobok](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Ez a szakasz bemutatja, hogyan töltse le a blob. Helyi tárterülethez továbbra is fennáll, vagy olvassa el a tartalom egy karakterlánccá egyesít. A minta kód hivatkozásokat a *-blob-tároló* az szakaszban létrehozott [blob tárolókat hozhat létre](#create-a-blob-container).

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus `DownloadBlob` , amely egy karakterláncot ad vissza.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Belül a `DownloadBlob` módszer, lekérni egy `CloudBlobContainer` objektum, amely a blob-tárolóba való hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Egy blob referenciaobjektum meghívásával beolvasása a `CloudBlobContainer.GetBlockBlobReference` metódust. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Egy blob letöltéséhez használjon a `CloudBlockBlob.DownloadToStream` metódust. A következő kódot a blob tartalmát egy stream objektumra visz át. Egy helyi fájlba is, hogy az objektum majd megőrződjenek. (Változás  *&lt;helyi fájlnév >* számára a teljesen minősített név képviselő le kell tölteni a blob esetén.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    A következő példa a befejezett `ListBlobs` metódust használ (a létrehozás alatt áll, helyi fájl teljes elérési útja):
    
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

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó `<li>` elem a listában, adja hozzá egy másik navigációs menü elem hozzáadása a következő HTML-KÓDBAN:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza ki **letöltési blob** letölteni a blob. A megadott blob a `CloudBlobContainer.GetBlockBlobReference` metódus hívása a megadott helyről tölti le a `File.OpenWrite` metódus hívása. A szöveg *sikeres!* meg kell jelennie a böngészőben. 

## <a name="delete-blobs"></a>Blobok törlése

A következő lépések bemutatják egy blob törlése:

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus `DeleteBlob` , amely egy karakterláncot ad vissza.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Belül a `DeleteBlob` módszer, lekérni egy `CloudBlobContainer` objektum, amely a blob-tárolóba való hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Egy blob referenciaobjektum meghívásával beolvasása a `CloudBlobContainer.GetBlockBlobReference` metódust. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Egy blob törléséhez használja a `Delete` metódust.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    A befejezett `DeleteBlob` metódust meg kell jelennie az alábbiak szerint:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó `<li>` elem a listában, adja hozzá egy másik navigációs menü elem hozzáadása a következő HTML-KÓDBAN:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Delete blob** a megadott blob törlése a `CloudBlobContainer.GetBlockBlobReference` metódus hívása. A szöveg *sikeres!* meg kell jelennie a böngészőben. Válassza ki a böngésző **vissza** gombra, és válassza **blobok listázása** annak ellenőrzéséhez, hogy a blob már nem a tárolóban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan tárolhatja, listában, és az Azure Storage blobs beolvasása az ASP.NET Core segítségével. Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Table storage és a Visual Studio csatlakoztatva (ASP.NET) szolgáltatások](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure Queue storage és a Visual Studio csatlakoztatva (ASP.NET) szolgáltatások](vs-storage-aspnet-getting-started-queues.md)
