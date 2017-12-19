---
title: "Ismerkedés az Azure blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET) |} Microsoft Docs"
description: "Ismerkedés az Azure blob storage használatával egy ASP.NET-projekt, a Visual Studio egy tárfiókot, a Visual Studio kapcsolódó szolgáltatások használatával történő kapcsolódás után"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: kraig
ms.openlocfilehash: f0d0815e71149749cb52efe21e1f0af3cabae21c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Az Azure blob storage egy olyan szolgáltatás, hogy a felhő strukturálatlan adatokat objektumként/blobként tárolja. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez az oktatóanyag bemutatja, hogyan írhat kódot ASP.NET olyan gyakori forgatókönyveket tartalmaz, az Azure blob storage használatával. Forgatókönyvek például a blob tároló, létrehozása és feltöltése, listázása, letöltése és blobok törlése.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Hozzon létre az MVC-vezérlő 

1. A a **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**, és a helyi menüből válassza ki a **Hozzáadás -> tartományvezérlő**.

    ![Vezérlő hozzáadása az ASP.NET MVC alkalmazások számára](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. A a **hozzáadása Scaffold** párbeszédablakban válassza **MVC 5 vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![Adja meg az MVC-vezérlő típusa](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Az a **vezérlő hozzáadása** párbeszédpanelen, a tartományvezérlő nevét *BlobsController*, és válassza ki **Hozzáadás**.

    ![Neve az MVC-vezérlő](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Adja hozzá a következő *használatával* irányelvek a `BlobsController.cs` fájlt:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>A tárfiók csatlakozhat, és a tároló hivatkozását beolvasása

Egy blob-tároló blobokat és mappák beágyazott hierarchiája.  A jelen dokumentumban leírt lépések többi elő egy blob-tárolóba, hogy a saját módszer újrahasznosításának kódot kell elhelyezni.

Az alábbi lépéseket a tárfiókot, a kapcsolati karakterlánc használatával kapcsolódni metódus létrehozása **Web.config** , és hozzon létre egy hivatkozást egy tárolót.  A kapcsolati karakterlánc beállítása a **Web.config** formátumban lesznek elnevezve `<storageaccountname>_AzureStorageConnectionString`. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus **GetCloudBlobContainer** , amely adja vissza egy **CloudBlobContainer**.  Ügyeljen arra, hogy a csere `<storageaccountname>_AzureStorageConnectionString` a kulcsot a tényleges nevével **Web.config**.
    
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
> Annak ellenére, hogy *-blob-tároló* nem létezik még, ezt a kódot létrehozza-e egy hivatkozást, a tároló hozhatja létre a `CreateIfNotExists` metódus a következő lépésben látható.

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

1. Hívja a `CloudBlobContainer.CreateIfNotExists` metódust létrehozni a tárolót, ha még nem létezik. A `CloudBlobContainer.CreateIfNotExists` metódus beolvasása **igaz** , ha a tároló nem létezik, és sikeresen létrejött. Ellenkező esetben **hamis** adja vissza.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Frissítés a `ViewBag` nevű, a blob-tároló.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A következő példa a befejezett `CreateBlobContainer` módszert:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Az a **Solution Explorer**, kattintson a jobb gombbal a **nézetek** mappa, és válassza a helyi menüben a **Hozzáadás -> Új mappa**. Az új mappa neve *Blobok*. 
 
1. Az a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **Blobok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **CreateBlobContainer** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `CreateBlobContainer.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Blob-tároló létrehozása** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Blobtároló létrehozása](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Ahogy korábban említettük a `CloudBlobContainer.CreateIfNotExists` metódus beolvasása **igaz** csak a tároló nem létezik és jön létre. Ezért, ha az alkalmazás fut, ha a tároló létezik, a metódus visszaadja **hamis**.

## <a name="upload-a-blob-into-a-blob-container"></a>Egy blob feltöltése a blob-tárolóba

Egyszer a [blob tároló jön létre](#create-a-blob-container), fájlok feltöltése a tárolóba. Ez a szakasz végigvezeti egy helyi fájl feltöltése a blob-tároló. A lépések azt feltételezik, hogy van egy blob-tároló nevű *-blob-tároló*. 

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

1. Ahogy korábban, az Azure storage támogatja különböző blob típusok. Ez az oktatóanyag a blokkblobokhoz használja.  Egy hivatkozást a blokkblob lekéréséhez hívja meg a `CloudBlobContainer.GetBlockBlobReference` metódust.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > A blob nevének beolvasása a blob URL-CÍMÉT részét képezi, és lehet bármilyen karakterlánc, beleértve a fájl nevét.

1. Amikor egy blobhivatkozást, töltse fel bármely adatfolyam azt a blob referenciaobjektum meghívásával `UploadFromStream` metódust. A `UploadFromStream` hoz létre a blob nem létezik, vagy felülírja, ha már létezik. (Változás  *&lt;fájlfeltöltés >* egy teljesen minősített feltölteni kívánt fájl elérési útját.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
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
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **feltöltése a blob**.  A "success!" szót megjelenjen-e.
    
    ![Sikeres ellenőrzés](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
A szakasz - [a blob-tárolóban lévő blobok listázása](#list-the-blobs-in-a-blob-container) -bemutatja, hogyan a blob-tárolóban lévő blobok listázásához.    

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
   
1. A blob-tárolóban lévő blobok listázásához, használja a `CloudBlobContainer.ListBlobs` metódust. A `CloudBlobContainer.ListBlobs` metódus értéket ad vissza egy `IListBlobItem` objektum, amely képes konvertálható egy `CloudBlockBlob`, `CloudPageBlob`, vagy `CloudBlobDirectory` objektum. A következő kódrészletet a blob-tároló összes blobjának enumerálása. Minden egyes blob típusúvá a megfelelő objektumot, típusa és a neve alapján van (vagy az URI egy **CloudBlobDirectory**) hozzá van adva egy listájához.

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

    Blob tárolók, blobok mellett könyvtárak tartalmazhat. Tegyük fel, hogy van egy blob-tároló nevű *-blob-tároló* , a következő hierarchiája:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Az előző kód példát a **blobok** karakterlánc lista tartalmaz értéket a következőhöz hasonló:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Látható, a lista tartalmazza-e a csak a legfelső szintű entitások; nem beágyazott megfelelően (*bar.png* és *baz.png*). A blob-tárolóban lévő összes entitás listájában, módosítsa a kódot, hogy a **CloudBlobContainer.ListBlobs** metódus kap **igaz** a a **Listblobs** a paraméter.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Beállítás a **Listblobs** paramétert **igaz** adja vissza egy strukturálatlan lista összes entitások a blob-tárolóban, és az alábbi eredményeket eredményez:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    A következő példa a befejezett **ListBlobs** módszert:

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

1. Az a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **Blobok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg `ListBlobs` a nézet nevét, majd válassza a **Hozzáadás**.

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **blobok listázása** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![A BLOB listázása](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Ez a szakasz bemutatja, hogyan töltse le a blob, és vagy megmaradnak, helyi tárolóhoz vagy olvassa el a tartalom egy karakterlánccá egyesít. A minta kód hivatkozásokat a *-blob-tároló* az szakaszban létrehozott [blob tárolókat hozhat létre](#create-a-blob-container).

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

1. Egy blob referenciaobjektum beolvasása meghívásával `CloudBlobContainer.GetBlockBlobReference` metódust. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Egy blob letöltéséhez használjon a `CloudBlockBlob.DownloadToStream` metódust. Az alábbi kód átviszi a blob tartalmát egy stream objektumra, majd egy helyi fájlba is megőrződjenek (módosítása  *&lt;helyi fájlnév >* számára a teljesen minősített név képviselő le kell tölteni a blob esetén.): 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
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
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **letöltési blob** letölteni a blob. A megadott blob a `CloudBlobContainer.GetBlockBlobReference` metódus hívása a megadott helyről tölti le a `File.OpenWrite` metódus hívása.  A szöveg a "success!" kell megjelenítenie a böngészőben. 

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

1. Egy blob referenciaobjektum beolvasása meghívásával `CloudBlobContainer.GetBlockBlobReference` metódust. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Egy blob törléséhez használja a `Delete` metódust.

    ```csharp
    blob.Delete();
    ```
    
    A befejezett `DeleteBlob` metódust meg kell jelennie az alábbiak szerint:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Delete blob** a megadott blob törlése a `CloudBlobContainer.GetBlockBlobReference` metódus hívása.  A szöveg a "success!" meg kell jelennie a böngészőben.  Kattintson a böngésző **vissza** gombra, majd válasszon **blobok listázása** ellenőrzése a blob már nem a tárolóban.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan tárolhatja, listában, és az ASP.NET használatával Azure Storage blobs beolvasása.  Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure table storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure várólista-tároló és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
