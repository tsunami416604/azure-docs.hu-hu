---
title: Az Azure Blob Storage használatának első lépései a Visual Studióval (ASP.NET)
description: Az Azure Blob Storage használatának első lépései egy ASP.NET-projektben a Visual Studióban, a Visual Studio csatlakoztatott szolgáltatásainak használatával történő csatlakozás után
services: storage
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6591cdb8f19484c87bb05f9007521adc34778f9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298866"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Az Azure Blob Storage egy olyan szolgáltatás, amely strukturálatlan adatmennyiséget tárol a felhőben objektumként vagy blobként. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. További információ a blob Storage-ról: [Az Azure Blob Storage bemutatása](../storage/blobs/storage-blobs-introduction.md).

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET-kódokat a blob Storage-t használó gyakori forgatókönyvek esetében. A forgatókönyvek közé tartoznak a blob-tárolók létrehozása, valamint a Blobok feltöltése, listázása, letöltése és törlése.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **megoldáskezelő**kattintson a jobb gombbal a **vezérlők**elemre.

2. A helyi menüben válassza a  > **vezérlő** **hozzáadása**elemet.

    ![Képernyőkép a Megoldáskezelőről, a Hozzáadás és a vezérlő kiemelve](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. A **állvány hozzáadása** párbeszédpanelen válassza az **MVC 5 vezérlő – üres**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    ![Képernyőfelvétel a állvány hozzáadása párbeszédpanelről](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. A **vezérlő hozzáadása** párbeszédpanelen nevezze el a vezérlő *BlobsController*, és válassza a **Hozzáadás**lehetőséget.

    ![A vezérlő hozzáadása párbeszédpanel képernyőképe](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Adja hozzá a következő `using` irányelveket a `BlobsController.cs` fájlhoz:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Kapcsolódás Storage-fiókhoz és tároló-hivatkozás beszerzése

A blob-tároló blobok és mappák beágyazott hierarchiája. A jelen dokumentumban szereplő további lépések egy blob-tárolóra mutató hivatkozást igényelnek, így a kódot a saját módszerére kell helyezni a felhasználhatósághoz.

A következő lépésekkel hozhat létre egy módszert a Storage-fiókhoz való kapcsolódáshoz a **web. config**fájl kapcsolati karakterláncának használatával. A lépések egy tárolóra mutató hivatkozást is létrehoznak.  A **web. config** fájl kapcsolati karakterláncának beállítása `<storageaccountname>_AzureStorageConnectionString` formátumú. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy **GetCloudBlobContainer** nevű metódust, amely egy **CloudBlobContainer**ad vissza.  Ügyeljen arra, hogy a **web. config fájlban**az `<storageaccountname>_AzureStorageConnectionString` értéket cserélje le a kulcs tényleges nevére.
    
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
> Bár a *test-blob-Container* még nem létezik, ez a kód egy hivatkozást hoz létre. Így a tároló hozható létre a következő lépésben látható `CreateIfNotExists` metódussal.

## <a name="create-a-blob-container"></a>Blobtároló létrehozása

A következő lépések bemutatják, hogyan hozhat létre BLOB-tárolót:

1. Adjon hozzá egy `CreateBlobContainer` nevű metódust, amely egy `ActionResult` értéket ad vissza.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Szerezzen be egy `CloudBlobContainer` objektumot, amely a kívánt blob-tároló nevére mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ha még nem létezik, hívja meg a `CloudBlobContainer.CreateIfNotExists` metódust a tároló létrehozásához. A `CloudBlobContainer.CreateIfNotExists` metódus **igaz** értéket ad vissza, ha a tároló nem létezik, és sikeresen létrejött. Ellenkező esetben a metódus **hamis**értéket ad vissza.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Frissítse a `ViewBag` értéket a blob-tároló nevével.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    A következő a befejezett `CreateBlobContainer` metódust mutatja:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. A **megoldáskezelő**kattintson a jobb gombbal a **nézetek** mappára.

1. Ha nincs **blob** -mappa, hozzon létre egyet. A helyi menüben válassza a **hozzáadás** > **új mappa**elemet. Nevezze el az új mappa *blobokat*. 
 
1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, majd kattintson a jobb gombbal a **Blobok**elemre.

1. A helyi menüben válassza a **hozzáadás** > **nézetet**.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **CreateBlobContainer** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `CreateBlobContainer.cshtml` értéket, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek**@no__t – 2**megosztott** mappát, és nyissa meg a `_Layout.cshtml` elemet.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **blob-tároló létrehozása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Képernyőkép a blob-tároló létrehozásáról](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Ahogy azt korábban említettük, a `CloudBlobContainer.CreateIfNotExists` metódus csak **igaz** értéket ad vissza, ha a tároló nem létezik, és létrejön. Ezért ha az alkalmazás akkor fut, amikor a tároló létezik, a metódus **hamis**értéket ad vissza.

## <a name="upload-a-blob-into-a-blob-container"></a>BLOB feltöltése blob-tárolóba

A [blob-tároló létrehozása](#create-a-blob-container)után töltse fel a fájlokat a tárolóba. Ez a szakasz végigvezeti egy helyi fájl blob-tárolóba való feltöltésének lépésén. A lépések azt feltételezik, hogy egy *test-blob-Container*nevű blob-tároló található. 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy `UploadBlob` nevű metódust, amely egy karakterláncot ad vissza.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. A `UploadBlob` metóduson belül szerezzen be egy `CloudBlobContainer` objektumot, amely a kívánt blob-tároló nevére mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Az Azure Storage különböző blob-típusokat támogat. Ez az oktatóanyag blokk-blobokat használ. Egy blokk blobra mutató hivatkozás lekéréséhez hívja meg a `CloudBlobContainer.GetBlockBlobReference` metódust.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > A blob neve a blob lekéréséhez használt URL-cím része, és bármilyen karakterlánc lehet, beleértve a fájl nevét is.

1. A Blobok hivatkozása után bármilyen adatfolyamot feltölthet rá a blob Reference objektum `UploadFromStream` metódusának meghívásával. A `UploadFromStream` metódus létrehozza a blobot, ha az nem létezik, vagy felülírja, ha létezik. (Módosítsa *&lt;file-to-upload >* a feltöltött fájl teljes elérési útjára.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Az alábbi ábrán a befejezett `UploadBlob` metódus látható (a feltöltött fájl teljes elérési útjával):

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

1. A **megoldáskezelő**bontsa ki a **nézetek**@no__t – 2**megosztott** mappát, és nyissa meg a `_Layout.cshtml` elemet.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **blob feltöltése**lehetőséget.  A szó *sikeres!* Ekkor meg kell jelennie.
    
    ![A sikeres ellenőrzés képernyőképe](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>BLOB-tárolóban lévő Blobok listázása

Ez a szakasz azt szemlélteti, hogyan lehet kilistázni a blobokat egy blob-tárolóban. A mintakód a szakaszban létrehozott *test-blob-tárolóra* hivatkozik, [létrehoz egy BLOB-tárolót](#create-a-blob-container).

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy `ListBlobs` nevű metódust, amely egy `ActionResult` értéket ad vissza.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. A `ListBlobs` metóduson belül szerezzen be egy `CloudBlobContainer` objektumot, amely a blob-tárolóra mutató hivatkozást jelöl. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. BLOB-tárolóban lévő Blobok listázásához használja a `CloudBlobContainer.ListBlobs` metódust. A `CloudBlobContainer.ListBlobs` metódus egy `IListBlobItem` objektumot ad vissza, amely egy `CloudBlockBlob`, `CloudPageBlob` vagy `CloudBlobDirectory` objektumba állítható be. A következő kódrészlet a blob-tárolóban lévő összes blobot enumerálja. Minden blob a megfelelő objektumba kerül, a típusa alapján. A rendszer hozzáadja a nevét (vagy **CloudBlobDirectory**esetén az URI-t) egy listához.

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

    A blobokon kívül a blob-tárolók könyvtárakat is tartalmazhatnak. Tegyük fel, hogy van egy *test-blob-Container*nevű blob-tároló, amelynek a hierarchiája a következő:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Az előző kódrészlet használatával a **Blobok** karakterlánc-listája a következőhöz hasonló értékeket tartalmaz:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Ahogy az ábrán látható, a lista csak a legfelső szintű entitásokat tartalmazza, nem pedig a beágyazott elemeket (a*Bar. png* és a *bAz. png*kiterjesztésű fájlokat). A blob-tárolón belüli összes entitás listázásához módosítsa a kódot úgy, hogy a **CloudBlobContainer. ListBlobs** metódus **igaz** legyen a **useflatbloblisting paraméterét** paraméter esetében.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Ha a **useflatbloblisting paraméterét** paramétert **true** értékre állítja, a blob tárolóban lévő összes entitáshoz tartozó egyszerű listát adja vissza. Ez a következő eredményeket eredményezi:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Az alábbi ábrán a befejezett **ListBlobs** metódus látható:

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

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, majd kattintson a jobb gombbal a **Blobok**elemre.

2. A helyi menüben válassza a **hozzáadás** > **nézetet**.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg az `ListBlobs` értéket a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `ListBlobs.cshtml` értéket, és cserélje le a tartalmát a következő kódra:

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

1. A **megoldáskezelő**bontsa ki a **nézetek**@no__t – 2**megosztott** mappát, és nyissa meg a `_Layout.cshtml` elemet.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **Blobok listázása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Képernyőfelvétel a Blobok listázásáról](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Ez a szakasz bemutatja, hogyan tölthető le egy blob. Megtarthatja azt a helyi tárolóban, vagy beolvashatja a tartalmat egy sztringbe. A mintakód a szakaszban létrehozott *test-blob-tárolóra* hivatkozik, [létrehoz egy BLOB-tárolót](#create-a-blob-container).

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy `DownloadBlob` nevű metódust, amely egy karakterláncot ad vissza.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. A `DownloadBlob` metóduson belül szerezzen be egy `CloudBlobContainer` objektumot, amely a blob-tárolóra mutató hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Szerezzen be egy blob Reference objektumot a `CloudBlobContainer.GetBlockBlobReference` metódus meghívásával. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. BLOB letöltéséhez használja a `CloudBlockBlob.DownloadToStream` metódust. A következő kód egy blob tartalmát továbbítja egy stream objektumra. Ezt az objektumot ezután megőrzi egy helyi fájl. (A *&lt;local-file-name >* a blob letöltését megadó teljes fájlnévre módosítsa.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Az alábbi ábrán a befejezett `DownloadBlob` metódus látható (a létrehozandó helyi fájl teljes elérési útjával):
    
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

1. A **megoldáskezelő**bontsa ki a **nézetek**@no__t – 2**megosztott** mappát, és nyissa meg a `_Layout.cshtml` elemet.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza a blob **letöltése** lehetőséget a blob letöltéséhez. A `CloudBlobContainer.GetBlockBlobReference` metódusban megadott blob letölti az `File.OpenWrite` metódus hívásában megadott helyre.  A szöveg *sikeres!* a böngészőben kell megjelennie. 

## <a name="delete-blobs"></a>Blobok törlése

A következő lépések bemutatják, hogyan törölhet egy blobot:

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adjon hozzá egy `DeleteBlob` nevű metódust, amely egy karakterláncot ad vissza.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. A `DeleteBlob` metóduson belül szerezzen be egy `CloudBlobContainer` objektumot, amely a blob-tárolóra mutató hivatkozást jelöl.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Szerezzen be egy blob Reference objektumot a `CloudBlobContainer.GetBlockBlobReference` metódus meghívásával. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. BLOB törléséhez használja a `Delete` metódust.

    ```csharp
    blob.Delete();
    ```
    
    A befejezett `DeleteBlob` metódusnak a következőképpen kell megjelennie:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek**@no__t – 2**megosztott** mappát, és nyissa meg a `_Layout.cshtml` elemet.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **blob törlése** elemet a `CloudBlobContainer.GetBlockBlobReference` metódus hívásában megadott blob törléséhez. A szöveg *sikeres!* a böngészőben kell megjelennie. Kattintson a böngésző **vissza** gombjára, majd válassza a Blobok **listázása** lehetőséget annak ellenőrzéséhez, hogy a blob már nem található a tárolóban.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan tárolhat, listázhat és kereshet blobokat az Azure Storage-ban a ASP.NET használatával. Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Table Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure üzenetsor-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
