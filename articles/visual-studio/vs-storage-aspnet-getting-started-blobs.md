---
title: Ismerkedés az Azure Blob storage és a Visual Studio csatlakoztatott szolgáltatásainak (ASP.NET) |} A Microsoft Docs
description: Első lépések az Azure Blob storage, ASP.NET-projekt a Visual Studióban, a Visual studióval és storage-fiók csatlakoztatása után csatlakoztatott szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: fd13dc94e0199aac04ae86aaf003ea9a70fa50d5
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070555"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure Blob storage és a Visual Studio csatlakoztatott szolgáltatásainak (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Az Azure Blob storage szolgáltatás, amely strukturálatlan adatokat tárol a felhőben objektumok vagy blobok. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez az oktatóanyag bemutatja, hogyan írhat kódot néhány gyakori forgatókönyvet, amely a Blob storage használata az ASP.NET. Forgatókönyvek között megtalálható a blob-tároló létrehozása és feltöltése, listázása, letöltése és blobok törlése.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Hozzon létre egy MVC-vezérlő 

1. A **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**.

2. A helyi menüből válassza ki a **Hozzáadás** > **vezérlő**.

    ![Képernyőkép a megoldáskezelő, hozzáadása és a tartományvezérlő kiemelésével](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Az a **Scaffold hozzáadása** párbeszédpanelen jelölje ki **MVC 5 vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![A párbeszédpanel Scaffold hozzáadása képernyőképe](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Az a **vezérlő hozzáadása** párbeszédpanelen, a vezérlő neve *BlobsController*, és válassza ki **Hozzáadás**.

    ![Vezérlő hozzáadása képernyőkép párbeszédpanel](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Adja hozzá a következő `using` irányelveket a `BlobsController.cs` fájlt:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Csatlakozás egy tárfiókhoz, és kérje le a tároló hivatkozását

Egy blob-tárolóba egy egymásba ágyazott hierarchiák a blobok és mappák. A jelen dokumentumban leírt lépésekben a többi szükséges blob-tárolóba, egy hivatkozás, hogy a kód a saját újrahasznosíthatóság módszert kell helyezni.

Az alábbi lépéseket a kapcsolati karakterlánc használatával csatlakozni a tárfiók metódus létrehozása **Web.config**. A lépések egy tárolót egy hivatkozást is létrehozhat.  A kapcsolati karakterlánc beállítása a **Web.config** formátumban nevű `<storageaccountname>_AzureStorageConnectionString`. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon meg egy metódust nevű **GetCloudBlobContainer** , amely adja vissza egy **CloudBlobContainer**.  Ne felejtse el `<storageaccountname>_AzureStorageConnectionString` a kulcsot a tényleges nevét **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Annak ellenére, hogy *teszt blobtároló* nem létezik, ez a kód egy hivatkozást hoz létre. Erre azért van, hogy a tároló lehet létrehozni a `CreateIfNotExists` módszer a következő lépésben látható.

## <a name="create-a-blob-container"></a>Blobtároló létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy blobtárolót:

1. Adjon meg egy metódust nevű `CreateBlobContainer` , amely adja vissza egy `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Get- `CloudBlobContainer` egy hivatkozást a kívánt blobtároló neve képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hívja a `CloudBlobContainer.CreateIfNotExists` metódussal hoz létre a tárolót, ha még nem található. A `CloudBlobContainer.CreateIfNotExists` metódus visszatért **igaz** , ha a tároló nem létezik, és sikeresen létrehozva. Ellenkező esetben adja vissza a metódus **hamis**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Frissítés `ViewBag` a blobtároló nevét.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A következő példa a kitöltött `CreateBlobContainer` módszer:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **nézetek** mappát.

1. Ha nem talál egy **Blobok** mappában hozzon létre egyet. A helyi menüből válassza ki a **Hozzáadás** > **új mappa**. Az új mappa neve *Blobok*. 
 
1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, és kattintson a jobb gombbal **Blobok**.

1. A helyi menüből válassza ki a **Hozzáadás** > **nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **CreateBlobContainer** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `CreateBlobContainer.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Blobtároló létrehozása** eredmények az alábbi képernyőfelvételhez hasonlóan megtekintéséhez:
  
    ![Blobtároló létrehozása képernyőképe](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Ahogy korábban említettük a `CloudBlobContainer.CreateIfNotExists` metódus visszatért **igaz** csak a tároló nem létezik a és jön létre. Ezért, ha az alkalmazás fut, amikor a tároló létezik, a metódus visszaadja **hamis**.

## <a name="upload-a-blob-into-a-blob-container"></a>Feltölt egy blobot egy blob-tárolóba

Ha a [blob-tároló létrehozása](#create-a-blob-container), fájlok feltöltése a tárolóba. Ez a szakasz végigvezeti egy helyi fájl feltöltése a blob-tárolóba. A lépések azt feltételezik, hogy van egy blobtárolót *teszt blobtároló*. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon meg egy metódust nevű `UploadBlob` , amely egy karakterláncot ad vissza.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Belül a `UploadBlob` metódus első egy `CloudBlobContainer` egy hivatkozást a kívánt blobtároló neve képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Az Azure storage támogatja a különböző blobtípusok. Ebben az oktatóanyagban a blokkblobok használatát támogatják. Egy hivatkozást a blokkblobok lekéréséhez hívja meg a `CloudBlobContainer.GetBlockBlobReference` metódust.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > A blob neve része az URL-cím beolvasása egy blobot, és lehet bármely karakterlánc, a fájl nevét is.

1. Miután egy blobhivatkozást, feltölthet bármely streamből, a blob referenciaobjektum meghívásával `UploadFromStream` metódust. A `UploadFromStream` metódus létrehozza a blobot, ha nem létezik, vagy felülírja, ha létezik. (Változás  *&lt;fájlfeltöltés >* , egy feltölteni kívánt fájl teljes elérési útja.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    A következő példa a kitöltött `UploadBlob` metódus (a feltölteni kívánt fájl teljes elérési útja):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **blob feltöltése**.  A word *sikeres!* meg kell jelennie.
    
    ![Sikeres ellenőrzés képernyőképe](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>A blob-tárolóban lévő blobok listázása

Ez a szakasz bemutatja, hogyan a blob-tárolóban lévő blobok listázása. A mintául szolgáló kód hivatkozik a *teszt blobtároló* az szakaszban létrehozott [hozzon létre egy blobtárolót](#create-a-blob-container).

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon meg egy metódust nevű `ListBlobs` , amely adja vissza egy `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Belül a `ListBlobs` metódus beolvasása egy `CloudBlobContainer` egy hivatkozást a blob-tároló képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. A blob-tárolóban lévő blobok listázása, használja a `CloudBlobContainer.ListBlobs` metódust. A `CloudBlobContainer.ListBlobs` metódus adja vissza egy `IListBlobItem` objektum, amely is konvertálható egy `CloudBlockBlob`, `CloudPageBlob`, vagy `CloudBlobDirectory` objektum. Az alábbi kódrészlet egy blob-tárolóban lévő összes BLOB enumerálása. Minden egyes blob típusa alapján a megfelelő objektum van konvertálni. A nevét (vagy az URI egy **CloudBlobDirectory**) egy listához való hozzáadásakor.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
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

    Blobok, valamint a blob-tárolók könyvtárak is tartalmazhat. Tegyük fel, hogy van egy nevű blob-tárolóban *teszt blobtároló*, az a következő hierarchia:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Az előző kód példa a **blobok** karakterlánc-lista tartalmaz értéket a következőhöz hasonló:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Amint látható, a lista tartalmazza-e a csak a legfelső szintű entitásokat, nem a beágyazott kapcsolatok (*bar.png* és *baz.png*). Egy blob tárolóból az entitások listájában, módosítsa a kódot, hogy a **CloudBlobContainer.ListBlobs** metódus kap **igaz** számára a **Listblobs** a paraméter.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Beállítás a **Listblobs** paramétert **igaz** adja vissza egy strukturálatlan lista az összes entitások blob-tárolóban. A következő eredményeket ez eredményez:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    A következő példa a kitöltött **ListBlobs** módszer:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
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

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, és kattintson a jobb gombbal **Blobok**.

2. A helyi menüből válassza ki a **Hozzáadás** > **nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg `ListBlobs` a nézet nevét, és válassza a **Hozzáadás**.

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

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **blobok listázása** eredmények az alábbi képernyőfelvételhez hasonlóan megtekintéséhez:
  
    ![Képernyőfelvétel: a lista blobok](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Ez a szakasz bemutatja, hogyan letölt egy blobot. A helyi tároló továbbra is fennáll, vagy olvassa el a tartalmat egy karakterláncban. A mintául szolgáló kód hivatkozik a *teszt blobtároló* az szakaszban létrehozott [hozzon létre egy blobtárolót](#create-a-blob-container).

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon meg egy metódust nevű `DownloadBlob` , amely egy karakterláncot ad vissza.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Belül a `DownloadBlob` metódus beolvasása egy `CloudBlobContainer` egy hivatkozást a blob-tároló képviselő objektum.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Egy blob referenciaobjektum első meghívásával a `CloudBlobContainer.GetBlockBlobReference` metódust. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Blobok letöltéséhez használja a `CloudBlockBlob.DownloadToStream` metódust. A következő kódot egy blob tartalmát egy stream objektumra továbbítja. Az objektum a rendszer ezután megőrzi a helyi fájl. (Változás  *&lt;helyi-fájlnév >* , a teljesen minősített nevet jelölő a blob esetén le kell tölteni.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    A következő példa a kitöltött `ListBlobs` metódus (a teljes elérési útját a helyi fájl létrehozása folyamatban):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **letöltési blob** a blob letöltéséhez. A megadott blob a `CloudBlobContainer.GetBlockBlobReference` metódus hívása a megadott helyre letölti a `File.OpenWrite` metódus hívása.  A szöveg *sikeres!* meg kell jelennie a böngészőben. 

## <a name="delete-blobs"></a>Blobok törlése

A következő lépések bemutatják, hogyan törölhet egy blob:

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon meg egy metódust nevű `DeleteBlob` , amely egy karakterláncot ad vissza.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Belül a `DeleteBlob` metódus beolvasása egy `CloudBlobContainer` egy hivatkozást a blob-tároló képviselő objektum.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Egy blob referenciaobjektum első meghívásával a `CloudBlobContainer.GetBlockBlobReference` metódust. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. A blob törléséhez használja a `Delete` metódust.

    ```csharp
    blob.Delete();
    ```
    
    A befejezett `DeleteBlob` metódus módon kell megjelennie:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** > **megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Delete blob** a megadott blob törlése a `CloudBlobContainer.GetBlockBlobReference` metódus hívása. A szöveg *sikeres!* meg kell jelennie a böngészőben. Válassza ki a böngésző **vissza** gombra, és válassza ki **blobok listázása** annak ellenőrzéséhez, hogy a blob már nem a tárolóban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan tárolhatja, listázása és az Azure Storage-blobok lekérni az ASP.NET használatával. Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Table storage és a Visual Studio csatlakoztatott szolgáltatásainak (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure Queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
