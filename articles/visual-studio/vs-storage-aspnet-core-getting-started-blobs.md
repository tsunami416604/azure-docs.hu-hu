---
title: Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core) | Microsoft Docs
description: Az Azure Blob Storage használatának első lépései egy ASP.NET Core-projektben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio csatlakoztatott szolgáltatásainak használatával
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
ms.openlocfilehash: 8ae82548d4976e505dae055445c71a5c12cbf263
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516170"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Az Azure Blob Storage egy olyan szolgáltatás, amely strukturálatlan adatmennyiséget tárol a felhőben objektumként vagy blobként. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. További információ a blob Storage-ról: [Az Azure Blob Storage bemutatása](../storage/blobs/storage-blobs-introduction.md).

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET Core kódot a blob Storage-t használó gyakori forgatókönyvekhez. A forgatókönyvek közé tartoznak a blob-tárolók létrehozása, valamint a Blobok feltöltése, listázása, letöltése és törlése.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztési környezet beállításán. Ez magában foglalja a ASP.NET Model-View-Controller (MVC) alkalmazás létrehozását, a csatlakoztatott szolgáltatások kapcsolatának hozzáadását, a vezérlő hozzáadását és a szükséges névtér-irányelvek megadását.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC-alkalmazás projekt létrehozása

1. Nyissa meg a Visual Studiót.

1. A főmenüben válassza a **fájl** > **új** > **projekt**lehetőséget.

1. Az **új projekt** párbeszédpanelen válassza a **web** > **ASP.net Core webalkalmazás** > **AspNetCoreStorage**lehetőséget. Ezután kattintson az **OK** gombra.

    ![A Visual Studio új projekt párbeszédpanelének képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Az **új ASP.net Core webalkalmazás** párbeszédpanelen válassza a **.net Core** > **ASP.net Core 2,0** > **Web Application (Model-View-Controller)** lehetőséget. Ezután kattintson az **OK** gombra.

    ![Képernyőkép az új ASP.NET Core webalkalmazás párbeszédpanelről](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Csatlakoztatott szolgáltatások használata Azure Storage-fiókhoz való csatlakozáshoz

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre.

2. A helyi menüben válassza a**csatlakoztatott szolgáltatás** **hozzáadása** > elemet.

1. A **csatlakoztatott szolgáltatások** párbeszédpanelen válassza a **felhőalapú tárolás az Azure Storage**-ban lehetőséget, majd válassza a **Konfigurálás**lehetőséget.

    ![A csatlakoztatott szolgáltatások párbeszédpanel képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Az **Azure Storage** párbeszédpanelen válassza ki az oktatóanyaghoz használni kívánt Azure Storage-fiókot. Új Azure Storage-fiók létrehozásához válassza az **új Storage-fiók létrehozása**lehetőséget, és fejezze be az űrlapot. Miután kiválasztotta a meglévő Storage-fiókot, vagy újat hoz létre, válassza a **Hozzáadás**lehetőséget. A Visual Studio telepíti a NuGet-csomagot az Azure Storage-hoz, valamint egy Storage-kapcsolódási karakterláncot a **appSettings. JSON**fájlhoz.

> [!TIP]
> Ha meg szeretné tudni, hogyan hozhat létre egy Storage-fiókot a [Azure Portal](https://portal.azure.com), tekintse meg a [Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md)című témakört.
>
> [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md)vagy [Azure Cloud Shell](../cloud-shell/overview.md)használatával is létrehozhat Storage-fiókot.


### <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **megoldáskezelő**kattintson a jobb gombbal a **vezérlők**elemre.

2. A helyi menüben válassza a**vezérlő** **hozzáadása** > elemet.

    ![Képernyőkép a Megoldáskezelőról](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Az **állvány hozzáadása** párbeszédpanelen válassza az **MVC vezérlő – üres**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    ![Képernyőfelvétel a állvány hozzáadása párbeszédpanelről](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Az **üres MVC-vezérlő hozzáadása** párbeszédpanelen nevezze el a vezérlő *BlobsController*, és válassza a **Hozzáadás**lehetőséget.

    ![Az üres MVC-vezérlő hozzáadása párbeszédpanel képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Adja hozzá a `using` következő irányelveket `BlobsController.cs` a fájlhoz:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Kapcsolódás Storage-fiókhoz és tároló-hivatkozás beszerzése

A blob-tároló blobok és mappák beágyazott hierarchiája. A jelen dokumentumban szereplő további lépések egy blob-tárolóra mutató hivatkozást igényelnek, így a kódot a saját módszerére kell helyezni a felhasználhatósághoz.

A következő lépésekkel hozhat létre egy módszert a Storage-fiókhoz való kapcsolódáshoz a **appSettings. JSON**kapcsolati karakterláncának használatával. A lépések egy tárolóra mutató hivatkozást is létrehoznak. A **appSettings. JSON** fájlhoz tartozó kapcsolatok karakterlánc-beállítása a ( `<storageaccountname>_AzureStorageConnectionString`z) formátumban van elnevezve. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy **GetCloudBlobContainer** nevű metódust, amely egy **CloudBlobContainer**ad vissza. Ügyeljen arra, hogy `<storageaccountname>_AzureStorageConnectionString` a a **web. config fájlban**a kulcs tényleges nevét cserélje le.
    
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
> Bár a *test-blob-Container* még nem létezik, ez a kód egy hivatkozást hoz létre. Így a tároló hozható létre `CreateIfNotExists` a következő lépésben látható módszerrel.

## <a name="create-a-blob-container"></a>Blobtároló létrehozása

A következő lépések bemutatják, hogyan hozhat létre BLOB-tárolót:

1. Adjon hozzá egy nevű `CreateBlobContainer` metódust, `ActionResult`amely egy értéket ad vissza.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. `CloudBlobContainer` Olyan objektum lekérése, amely a kívánt blob-tároló nevére mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ha még `CloudBlobContainer.CreateIfNotExists` nem létezik, hívja meg a metódust a tároló létrehozásához. A `CloudBlobContainer.CreateIfNotExists` metódus **igaz** értéket ad vissza, ha a tároló nem létezik, és sikeresen létrejött. Ellenkező esetben a metódus **hamis**értéket ad vissza.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Frissítse `ViewBag` a nevet a blob-tároló nevével.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A következő a befejezett `CreateBlobContainer` metódust mutatja:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. A **megoldáskezelő**kattintson a jobb gombbal a **nézetek** mappára.

2. A helyi menüben válassza az**új mappa** **hozzáadása** > elemet. Nevezze el az újmappa blobokat. 

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, majd kattintson a jobb gombbal a **Blobok**elemre.

4. A helyi menüben válassza a**nézet** **hozzáadása** > elemet.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **CreateBlobContainer** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `CreateBlobContainer.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** > **megosztott** mappáját, `_Layout.cshtml`és nyissa meg a parancsot.

1. Keresse meg a következőhöz hasonló rendezetlen listát: `<ul class="nav navbar-nav">`.  A lista utolsó `<li>` eleme után adja hozzá a következő HTML-t egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a **blob-tároló létrehozása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Képernyőkép a blob-tároló létrehozásáról](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Ahogy azt korábban említettük `CloudBlobContainer.CreateIfNotExists` , a metódus csak **igaz** értéket ad vissza, ha a tároló nem létezik, és létrejön. Ezért ha az alkalmazás akkor fut, amikor a tároló létezik, a metódus **hamis**értéket ad vissza.

## <a name="upload-a-blob-into-a-blob-container"></a>BLOB feltöltése blob-tárolóba

A [blob-tároló létrehozása](#create-a-blob-container)után töltse fel a fájlokat a tárolóba. Ez a szakasz végigvezeti egy helyi fájl blob-tárolóba való feltöltésének lépésén. A lépések azt feltételezik, hogy egy *test-blob-Container*nevű blob-tároló található. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy nevű `UploadBlob` metódust, amely egy karakterláncot ad vissza.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. A `UploadBlob` metóduson belül szerezzen `CloudBlobContainer` be egy objektumot, amely a kívánt blob-tároló nevére mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Az Azure Storage különböző blob-típusokat támogat. Ez az oktatóanyag blokk-blobokat használ. A blokkos blobokra mutató hivatkozás lekéréséhez hívja `CloudBlobContainer.GetBlockBlobReference` meg a metódust.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > A blob neve a blob lekéréséhez használt URL-cím része, és bármilyen karakterlánc lehet, beleértve a fájl nevét is.

1. A Blobok hivatkozása után bármilyen adatfolyamot feltölthet rá a blob Reference objektum `UploadFromStream` metódusának meghívásával. A `UploadFromStream` metódus létrehozza a blobot, ha az nem létezik, vagy felülírja, ha létezik. (  *&lt;Fájl – feltöltés >* módosítása a feltöltött fájl teljes elérési útjára.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Az alábbi ábrán a `UploadBlob` befejezett metódus látható (a feltöltött fájl teljes elérési útjával):

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

1. A **megoldáskezelő**bontsa ki a **nézetek** > **megosztott** mappáját, `_Layout.cshtml`és nyissa meg a parancsot.

1. A lista utolsó `<li>` eleme után adja hozzá a következő HTML-t egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a **blob feltöltése**lehetőséget. A szó *sikeres!* Ekkor meg kell jelennie.
    
    ![A sikeres ellenőrzés képernyőképe](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>BLOB-tárolóban lévő Blobok listázása

Ez a szakasz azt szemlélteti, hogyan lehet kilistázni a blobokat egy blob-tárolóban. A mintakód a szakaszban létrehozott *test-blob-tárolóra* hivatkozik, [létrehoz egy blob](#create-a-blob-container)-tárolót.

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy nevű `ListBlobs` metódust, `ActionResult`amely egy értéket ad vissza.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. A `ListBlobs` metóduson belül szerezzen `CloudBlobContainer` be egy objektumot, amely a blob-tárolóra mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. BLOB-tárolóban lévő Blobok listázásához használja a `CloudBlobContainer.ListBlobsSegmentedAsync` metódust. A `CloudBlobContainer.ListBlobsSegmentedAsync` metódus a `BlobResultSegment`értéket adja vissza. Ez olyan `IListBlobItem` objektumokat tartalmaz, amelyek elhelyezhetők `CloudPageBlob` `CloudBlockBlob`, vagy `CloudBlobDirectory` objektumokra. A következő kódrészlet a blob-tárolóban lévő összes blobot enumerálja. Minden blob a megfelelő objektumba kerül, a típusa alapján. A rendszer hozzáadja a nevét (vagy a esetén `CloudBlobDirectory`az URI-t) egy listához.

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
    A következő a befejezett `ListBlobs` metódust mutatja:

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

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, majd kattintson a jobb gombbal a **Blobok**elemre.

2. A helyi menüben válassza a**nézet** **hozzáadása** > elemet.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg `ListBlobs` a nézet nevét, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `ListBlobs.cshtml`a (z) programot, és cserélje le a tartalmát a következő kódra:

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

1. A **megoldáskezelő**bontsa ki a **nézetek** > **megosztott** mappáját, `_Layout.cshtml`és nyissa meg a parancsot.

1. A lista utolsó `<li>` eleme után adja hozzá a következő HTML-t egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a Blobok listázása lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Képernyőfelvétel a Blobok listázásáról](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Ez a szakasz bemutatja, hogyan tölthető le egy blob. Megtarthatja azt a helyi tárolóban, vagy beolvashatja a tartalmat egy sztringbe. A mintakód a szakaszban létrehozott *test-blob-tárolóra* hivatkozik, [létrehoz egy blob](#create-a-blob-container)-tárolót.

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy nevű `DownloadBlob` metódust, amely egy karakterláncot ad vissza.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. A `DownloadBlob` metóduson belül szerezzen `CloudBlobContainer` be egy objektumot, amely a blob-tárolóra mutató hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. A `CloudBlobContainer.GetBlockBlobReference` metódus meghívásával szerezzen be egy blob-hivatkozás objektumot. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. BLOB letöltéséhez használja a `CloudBlockBlob.DownloadToStream` metódust. A következő kód egy blob tartalmát továbbítja egy stream objektumra. Ezt az objektumot ezután megőrzi egy helyi fájl. (Módosítsa  *&lt;a helyi fájlnevet >* a teljes fájlnévre, amely megadja a blob letöltésének helyét.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Az alábbi ábrán a `ListBlobs` befejezett metódus látható (a létrehozandó helyi fájl teljes elérési útjával):
    
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

1. A **megoldáskezelő**bontsa ki a **nézetek** > **megosztott** mappáját, `_Layout.cshtml`és nyissa meg a parancsot.

1. A lista utolsó `<li>` eleme után adja hozzá a következő HTML-t egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a blob **letöltése** lehetőséget a blob letöltéséhez. A `CloudBlobContainer.GetBlockBlobReference` metódus hívásában megadott blob a `File.OpenWrite` metódus hívásában megadott helyre töltődik le. A szöveg *sikeres!* a böngészőben kell megjelennie. 

## <a name="delete-blobs"></a>Blobok törlése

A következő lépések bemutatják, hogyan törölhet egy blobot:

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy nevű `DeleteBlob` metódust, amely egy karakterláncot ad vissza.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. A `DeleteBlob` metóduson belül szerezzen `CloudBlobContainer` be egy objektumot, amely a blob-tárolóra mutató hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. A `CloudBlobContainer.GetBlockBlobReference` metódus meghívásával szerezzen be egy blob-hivatkozás objektumot. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. BLOB törléséhez használja a `Delete` metódust.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    A befejezett `DeleteBlob` metódusnak a következőképpen kell megjelennie:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** > **megosztott** mappáját, `_Layout.cshtml`és nyissa meg a parancsot.

1. A lista utolsó `<li>` eleme után adja hozzá a következő HTML-t egy másik navigációs menüelem hozzáadásához:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Futtassa az alkalmazást, és válassza a **blob törlése** elemet a `CloudBlobContainer.GetBlockBlobReference` metódus hívásában megadott blob törléséhez. A szöveg *sikeres!* a böngészőben kell megjelennie. Kattintson a böngésző **vissza** gombjára, majd válassza a Blobok listázása lehetőséget annak ellenőrzéséhez, hogy a blob már nem található a tárolóban.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan tárolhat, listázhat és kereshet blobokat az Azure Storage-ban ASP.NET Core használatával. Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Table Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure üzenetsor-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
