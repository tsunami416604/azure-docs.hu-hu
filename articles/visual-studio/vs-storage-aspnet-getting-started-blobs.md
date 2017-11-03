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
ms.date: 12/21/2016
ms.author: kraig
ms.openlocfilehash: e953c7978705379a28581213e8f1c665473ddd60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés

Az Azure blob storage egy olyan szolgáltatás, hogy a felhő strukturálatlan adatokat objektumként/blobként tárolja. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez az oktatóanyag bemutatja, hogyan írhat kódot ASP.NET olyan gyakori forgatókönyveket tartalmaz, az Azure blob storage használatával. Forgatókönyvek például a blob tároló, létrehozása és feltöltése, listázása, letöltése és blobok törlése.

##<a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

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
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>A blob-tároló létrehozása

Egy blob-tároló blobokat és mappák beágyazott hierarchiája. A következő lépések bemutatják egy blob-tároló létrehozása:

> [!NOTE]
> 
> Ebben a szakaszban a kód feltételezi, hogy végrehajtotta a lépéseket a területen [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus **CreateBlobContainer** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **CreateBlobContainer** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A következő kódot használja a tárolási kapcsolati karakterlánc és a tárfiók adatait lekérése az Azure szolgáltatás konfigurációját. (Változás  *&lt;-tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudBlobClient** objektum egy blob szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Első egy **CloudBlobContainer** hivatkozni kell a kívánt blobtároló neve képviselő objektum. A **CloudBlobClient.GetContainerReference** metódus nem tesz egy kérelmet a blob storage. A hivatkozás adja vissza a blob-tároló létezik-e. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Hívja a **CloudBlobContainer.CreateIfNotExists** metódust létrehozni a tárolót, ha még nem létezik. A **CloudBlobContainer.CreateIfNotExists** metódus beolvasása **igaz** , ha a tároló nem létezik, és sikeresen létrejött. Ellenkező esetben **hamis** adja vissza.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Frissítés a **ViewBag** a blob-tároló nevét.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

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
  
    ![A blob-tároló létrehozása](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Ahogy korábban említettük a **CloudBlobContainer.CreateIfNotExists** metódus beolvasása **igaz** csak a tároló nem létezik és jön létre. Ezért, ha futtatja a az alkalmazás a tároló létezik, a metódus visszaadja **hamis**. Az alkalmazás többször is lefuthat, törölnie kell a tároló az alkalmazás ismételt futtatása előtt. A tároló törlése megteheti a **CloudBlobContainer.Delete** metódust. Törölheti is a tárolót használja a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Egy blob feltöltése a blob-tárolóba

Miután megismerte [létrejött a blob-tároló](#create-a-blob-container), feltöltheti a fájlokat, hogy tárolóba. Ez a szakasz végigvezeti egy helyi fájl feltöltése a blob-tároló. A lépések azt feltételezik, hogy létrehozta a következő nevű blobtárolóban *-blob-tároló*. 

> [!NOTE]
> 
> Ebben a szakaszban a kód feltételezi, hogy végrehajtotta a lépéseket a területen [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus **UploadBlob** , amely visszaadja az **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Belül a **UploadBlob** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudBlobClient** objektum egy blob szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Első egy **CloudBlobContainer** hivatkozni kell a blobtároló neve képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Ahogy korábban, az Azure storage támogatja különböző blob típusok. Oldalakra vonatkozó blob mutató hivatkozás lekéréséhez hívja meg a **CloudBlobContainer.GetPageBlobReference** metódust. Egy hivatkozást a blokkblob lekéréséhez hívja meg a **CloudBlobContainer.GetBlockBlobReference** metódust. Blokkblob általában a blokkblobok használata javasolt. (< Blob-név > módosítása * egyszer feltöltése a blob hozzárendelni kívánt nevét.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Miután egy blobhivatkozást, feltöltheti bármely adatfolyam azt a blob referenciaobjektum meghívásával **UploadFromStream** metódust. A **UploadFromStream** hoz létre a blob nem létezik, vagy felülírja, ha már létezik. (Változás  *&lt;fájlfeltöltés >* egy teljesen minősített a feltölteni kívánt fájl elérési útját.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. Az a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **Blobok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **feltöltése a blob**.  
  
A szakasz - [a blob-tárolóban lévő blobok listázása](#list-the-blobs-in-a-blob-container) -bemutatja, hogyan a blob-tárolóban lévő blobok listázásához.    

## <a name="list-the-blobs-in-a-blob-container"></a>A blob-tárolóban lévő blobok listázása

Ez a szakasz bemutatja, hogyan a blob-tárolóban lévő blobok listázásához. A minta kód hivatkozásokat a *-blob-tároló* az szakaszban létrehozott [blob tárolókat hozhat létre](#create-a-blob-container).

> [!NOTE]
> 
> Ebben a szakaszban a kód feltételezi, hogy végrehajtotta a lépéseket a területen [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus **ListBlobs** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **ListBlobs** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudBlobClient** objektum egy blob szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Első egy **CloudBlobContainer** hivatkozni kell a blobtároló neve képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. A blob-tárolóban lévő blobok listázásához, használja a **CloudBlobContainer.ListBlobs** metódust. A **CloudBlobContainer.ListBlobs** metódus értéket ad vissza egy **IListBlobItem** objektum, amely a adatoszlopon egy **CloudBlockBlob**, **CloudPageBlob**, vagy **CloudBlobDirectory** objektum. A következő kódrészletet a blob-tároló összes blobjának enumerálása. Minden egyes blob típusúvá a megfelelő objektumot, típusa és a neve alapján van (vagy az URI egy **CloudBlobDirectory**) hozzá van adva egy listájához.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

    Blob tárolók, blobok mellett könyvtárak tartalmazhat. Most tegyük fel, a következő nevű blobtárolóban *-blob-tároló* , a következő hierarchiája:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Az előző kód példát a **blobok** karakterlánc lista tartalmaz értéket a következőhöz hasonló:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Ahogy látja, a lista tartalmazza-e a csak a legfelső szintű entitások; nem beágyazott megfelelően (*bar.png* és *baz.png*). A blob-tárolóban lévő összes entitás listázásához, meg kell hívnia a **CloudBlobContainer.ListBlobs** metódus és pass **igaz** a a **Listblobs** paraméter.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    Beállítás a **Listblobs** paramétert **igaz** adja vissza egy strukturálatlan lista összes entitások a blob-tárolóban, és az alábbi eredményeket eredményez:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. Az a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **Blobok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **ListBlobs** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `ListBlobs.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

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

1. Adja hozzá a hívott metódus **DownloadBlob** , amely visszaadja az **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Belül a **DownloadBlob** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudBlobClient** objektum egy blob szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Első egy **CloudBlobContainer** hivatkozni kell a blobtároló neve képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Egy blob referenciaobjektum beolvasása meghívásával **CloudBlobContainer.GetBlockBlobReference** vagy **CloudBlobContainer.GetPageBlobReference** metódust. (Változás  *&lt;blob-neve >* a blob nevével tölti le.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Egy blob letöltéséhez használjon a **CloudBlockBlob.DownloadToStream** vagy **CloudPageBlob.DownloadToStream** módszer, attól függően, hogy a blob típushoz. A következő kódban részlet a **CloudBlockBlob.DownloadToStream** módszerrel kell továbbítania a blob tartalmát egy stream objektumra, majd egy helyi fájlba fennállásának: (változás  *&lt;helyi fájlnév >* a teljesen minősített fájl nevét képviselő, ha azt szeretné, hogy a blob letöltött.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **letöltési blob** letölteni a blob. A megadott blob a **CloudBlobContainer.GetBlockBlobReference** metódus hívása a megadott helyen letölti a **File.OpenWrite** metódus hívása. 

## <a name="delete-blobs"></a>Blobok törlése

A következő lépések bemutatják egy blob törlése:

> [!NOTE]
> 
> Ebben a szakaszban a kód feltételezi, hogy végrehajtotta a lépéseket a területen [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `BlobsController.cs` fájlt.

1. Adja hozzá a hívott metódus **DeleteBlob** , amely visszaadja az **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Első egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudBlobClient** objektum egy blob szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Első egy **CloudBlobContainer** hivatkozni kell a blobtároló neve képviselő objektum. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Egy blob referenciaobjektum beolvasása meghívásával **CloudBlobContainer.GetBlockBlobReference** vagy **CloudBlobContainer.GetPageBlobReference** metódust. (Változás  *&lt;blob-neve >* nevére, a blob törli.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Delete blob** a megadott blob törlése a **CloudBlobContainer.GetBlockBlobReference** metódus hívása. 

## <a name="next-steps"></a>Következő lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure table storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Ismerkedés az Azure várólista-tároló és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
