---
title: Ismerkedés a blob storage és a Visual Studio csatlakoztatott services (felhőszolgáltatások) |} Microsoft Docs
description: Ismerkedés az Azure Blob storage használata a Visual Studio felhőszolgáltatás-projekt egy tárfiókot, a Visual Studio használatával történő kapcsolódás után kapcsolódó szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 2df69d9f180303f6c14433140d862c017b97148d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Blob Storage és a Visual Studio kapcsolódó szolgáltatások (felhőalapú szolgáltatások projektek)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az első lépések az Azure Blob Storage szolgáltatással, miután létrehozott vagy egy Azure Storage-fiók hivatkozik a Visual Studio használatával **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel a Visual Studio felhő szolgáltatási projektet. Mutat be, elérése és blob tárolók létrehozása és feltöltése, listázása és blobok letöltése például gyakori feladatok elvégzéséhez. A Kódminták C nyelven íródtak\# , és használja a [Microsoft Azure Storage ügyféloldali kódtára a .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Az Azure Blob Storage egy olyan szolgáltatás nagy mennyiségű strukturálatlan adatok, amelyek elérhetők bárhol a világon HTTP vagy HTTPS PROTOKOLLON keresztül tárolásához. Egy blob bármilyen méretű lehet. Blobok lehetnek többek között a lemezképek, a hang- és fájlok, a nyers adatok és a fájlok.

Ugyanúgy, mint a él fájlok, mappák, a tárolási BLOB élő tárolókban lévő. Miután létrehozta a tárolási, egy vagy több tárolóban hoz létre a tárolóban. Például "Lapkivágások" nevű tárolási, az úgynevezett "képek" képek tárolására storage hozhat létre, és más néven "hang", illetve hang-fájlok tárolására. A tárolók létrehozása után feltöltheti egyes blob fájlok hozzájuk.

* Programozott módon kezelésére a blobok további információkért lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Azure Storage kapcsolatos általános információkért lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/).
* Azure Cloud Services kapcsolatos általános információkért lásd: [felhőalapú szolgáltatások dokumentációja](https://azure.microsoft.com/documentation/services/cloud-services/).
* ASP.NET-alkalmazások programozásával kapcsolatos további információkért lásd: [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Hozzáférés a blob tárolók kód
Való szoftveres hozzáféréshez blobot, amely a felhőszolgáltatás-projektekre nézve, kell hozzáadnia a következő elemek, ha azok már nem létezik.

1. A következő kód névtér-deklarációk hozzáadása bármely végre programon keresztüli eléréséhez az Azure Storage akarja C#-fájl elejéhez.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Első egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A következő kód segítségével beolvasása a a tárolási kapcsolati karakterlánc és a tárfiók adatait az Azure-szolgáltatások konfigurációjából.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Első egy **CloudBlobClient** való hivatkozáshoz egy meglévő tárolót a tárfiókban lévő objektum.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Első egy **CloudBlobContainer** való hivatkozáshoz egy adott blob tároló objektum.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Minden az előző eljárás előtt az alábbi szakaszok a kód látható kód használható.
> 
> 

## <a name="create-a-container-in-code"></a>A kód egy tároló létrehozása
> [!NOTE]
> Egyes hajtsa végre az Azure Storage hívásainak az ASP.NET API-k aszinkron jellegűek. Lásd: [aszinkron programozás az Async és Await](http://msdn.microsoft.com/library/hh191443.aspx) további információt. A kódot az alábbi példa azt feltételezi, hogy aszinkron programozási módszerek használatát.
> 
> 

Egy tároló létrehozásához a tárfiókban lévő, mindössze annyit kell tennie, adjon hozzá egy **CreateIfNotExistsAsync** hasonlóan a következő kódot:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Elérhetővé a tárolóban lévő fájlokat mindenki számára, hogy a tároló, a következő kód segítségével nyilvánossá állíthatja be.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Bárki láthatja a nyilvános tárolókban lévő blobokat, de módosítja vagy törli őket, csak akkor, ha rendelkezik a megfelelő hozzáférési kulccsal.

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Az Azure Storage támogatja a blokkblobokat és a lapblobokat. A legtöbb esetben a blokkblobok használata javasolt.

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha megszerezte a blobhivatkozást, akkor az **UploadFromStream** metódus hívásával bármilyen streamet feltölthet. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezt követően a tároló **ListBlobs** metódusának segítéségével lekérheti a benne lévő blobokat és/vagy könyvtárakat. Számos tulajdonságai és metódusai visszaadásakor eléréséhez **IListBlobItem**, kell alakítania azt egy **CloudBlockBlob**, **CloudPageBlob**, vagy **CloudBlobDirectory** objektum. Ha a típus ismereten, akkor a típusellenőrzés segítségével határozza meg, hogy milyen típussá kell átalakítani. Az alábbi kód bemutatja, hogyan kérhető le és küldhető el a **photos** tárolóban lévő egyes elemek URI azonosítója:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Amint az előző példakód, a blob szolgáltatás rendelkezik a tárolókat is beleértve levő könyvtár fogalmát. Ez az, hogy a blobokat több mappa hasonló struktúrában rendezhetők. Vegyük példaként az alábbi blokkblobokat a **photos** nevű tárolóban:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

A hívás esetén **ListBlobs** a tárolóban (a fenti példában), a visszaadott gyűjteményt tartalmaz **CloudBlobDirectory** és **CloudBlockBlob** illetve legfelső szintjén lévő blobokat képviselő objektum. Ennek kimenete a következő:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Beállíthatja a **ListBlobs** metódus **UseFlatBlobListing** paraméterét **true** értékre. Minden blob ad vissza az eredmény egy **CloudBlockBlob**directory függetlenül. Ez a hívás **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

és az alábbi történik:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

További információkért lásd: [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Blobok letöltése
Blobok letöltéséhez először kérjen le egy blobhivatkozást, majd hívja a **DownloadToStream** metódust. A következő példa a **DownloadToStream** metódus segítségével helyezi át a blob tartalmát egy stream objektumra, amelyet egy helyi fájlban megőrizhet.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

A **DownloadToStream** metódussal a blob tartalmát szöveges karakterláncként is letöltheti.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Blobok törlése
Blobok törléséhez először kérjen le egy blobhivatkozást, és, majd hívja a **törlése** metódust.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Blobok listázása több oldalon aszinkron módon
Nagyszámú blob listázásakor, vagy ha szabályozni szeretné a listázási művelet által megjelenített eredmények számát, a blobokat több eredményoldalra is listázhatja. Ez a példa bemutatja, hogyan kérhetők le eredmények több oldalon aszinkron módon úgy, hogy amíg egy nagy eredménykészletre várakozik, a rendszer ne tiltsa le a végrehajtást.

Ez a lista egy strukturálatlan bloblistát mutat be, de hierarchikus listát is kérhet, ha a **useFlatBlobListing** paramétert a **ListBlobsSegmentedAsync** metódusban átállítja **false** értékre.

Mivel a mintametódus aszinkrón metódust használ, előtagként az **async** kulcsszót kell megadni, és **Task** objektumot kell visszaadnia. A **ListBlobsSegmentedAsync** metódushoz megadott await kulcsszó a listázási feladat befejezéséig felfüggeszti a mintametódus futtatását.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>További lépések
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

