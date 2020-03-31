---
title: A blobstorage használatának első lépései a Visual Studio használatával (felhőszolgáltatások)
description: Az Azure Blob storage használatának első lépései egy felhőalapú szolgáltatásprojektben a Visual Studióban, miután a Visual Studio csatlakoztatott szolgáltatásaival csatlakoztak egy tárfiókhoz
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d151e55f627166d8ad7d8affa53740e86cd1e501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298807"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Blob Storage-hez és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Blob Storage használatát, miután létrehozott vagy hivatkozott egy Azure Storage-fiókra a Visual Studio **Csatlakoztatott szolgáltatások hozzáadása** párbeszédpanelen egy Visual Studio felhőszolgáltatási projektben. Bemutatjuk, hogyan érheti el és hozhat létre blobtárolókat, és hogyan hajthat végre olyan gyakori feladatokat, mint a blobok feltöltése, listázása és letöltése. A minták C\# nyelven íródnak, és a Microsoft Azure Storage Client Library for [.NET programot](https://msdn.microsoft.com/library/azure/dn261237.aspx)használják.

Az Azure Blob Storage egy olyan szolgáltatás, amely nagy mennyiségű strukturálatlan adatok tárolására, amelyek a világ bármely pontjáról http-n vagy HTTPS-en keresztül érhetők el. Egyetlen blob bármilyen méretű lehet. A blobok lehetnek például képek, hang- és videofájlok, nyers adatok és dokumentumfájlok.

Ahogy a fájlok mappákban élnek, a tárolóblobok is tárolókban élnek. Miután létrehozott egy tárolót, hozzon létre egy vagy több tárolót a tárolóban. Például egy "Scrapbook" nevű tárolóban létrehozhat tárolókat a "képek" nevű tárolóban a képek tárolására, és egy másik úgynevezett "hang" a hangfájlok tárolására. A tárolók létrehozása után feltöltheti az egyes blobfájlokat.

* A blobok programozott módon történő kezelésére vonatkozó további információkért olvassa el az [Azure Blob storage használatának első lépéseit a .NET használatával című témakörben.](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* Az Azure Storage szolgáltatással kapcsolatos általános tudnivalókat a [Storage dokumentációjában](https://azure.microsoft.com/documentation/services/storage/)találja.
* Az Azure Cloud Services szolgáltatással kapcsolatos általános tudnivalókért tanulmányozza a [Cloud Services dokumentációját.](https://azure.microsoft.com/documentation/services/cloud-services/)
* Az alkalmazások programozásáról ASP.NET programozásáról a ASP.NET című témakörben [talál](https://www.asp.net)további információt.

## <a name="access-blob-containers-in-code"></a>Blob-tárolók elérése kódban
A felhőszolgáltatási projektek ben lévő blobok programozott eléréséhez hozzá kell adnia a következő elemeket, ha még nincsenek jelen.

1. Adja hozzá a következő kód névtér deklarációk a tetején minden C# fájl, amelyben szeretné programozott módon elérni az Azure Storage.Add the following code namespace declarations to the top of any C# file in you you wish to programmatically access Azure Storage.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával lejuthat a tárolási kapcsolat ihúr- és tárfiók-adataihoz az Azure szolgáltatáskonfigurációjából.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Egy **CloudBlobClient** objektum ot a tárfiókban lévő meglévő tárolóhivatkozáshoz.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Egy **CloudBlobContainer** objektum beszereznie egy adott blobtárolóra hivatkozva.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Használja az előző eljárásban a következő szakaszokban látható kód előtt látható összes kódot.
> 
> 

## <a name="create-a-container-in-code"></a>Tároló létrehozása kódban
> [!NOTE]
> Egyes API-k, amelyek hívásokat az Azure Storage-ASP.NET aszinkron. További információt lásd: [Aszinkron programozás az Async és a Várakozás.](https://msdn.microsoft.com/library/hh191443.aspx) A következő példában található kód feltételezi, hogy aszinkron programozási módszereket használ.
> 
> 

Ha tárolót szeretne létrehozni a tárfiókban, mindössze annyit kell tennie, hogy hozzáad egy hívást a **CreateIfNotExistsAsync-hez,** akövetkező kódnak a szerint:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Ha a tárolóban lévő fájlokat mindenki számára elérhetővé szeretné tenni, beállíthatja, hogy a tároló nyilvános legyen a következő kód használatával.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Az interneten bárki láthatja a blobokat egy nyilvános tárolóban, de csak akkor módosíthatja vagy törölheti őket, ha rendelkezik a megfelelő hozzáférési kulccsal.

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
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezt követően a tároló **ListBlobs** metódusának segítéségével lekérheti a benne lévő blobokat és/vagy könyvtárakat. A visszaadott **IListBlobItem**tulajdonságok és metódusok gazdag készletének eléréséhez **egy CloudBlockBlob**, **CloudPageBlob**vagy **CloudBlobDirectory** objektumra kell vetni. Ha a típus ismereten, akkor a típusellenőrzés segítségével határozza meg, hogy milyen típussá kell átalakítani. Az alábbi kód bemutatja, hogyan kérhető le és küldhető el a **photos** tárolóban lévő egyes elemek URI azonosítója:

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

Amint az előző kódminta, a blob szolgáltatás a könyvtárak a tárolókon belüli fogalmat is tartalmaz. Ez azért van így, hogy a blobokat egy mappaszerűbb struktúrába rendezhesse. Vegyük példaként az alábbi blokkblobokat a **photos** nevű tárolóban:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Amikor **a tárolóban listblobokat** hív meg (mint az előző mintában), a visszaadott gyűjtemény tartalmazza a **CloudBlobDirectory** és **a CloudBlockBlob** objektumokat, amelyek a legfelső szinten található könyvtárakat és blobokat képviselik. Itt van az eredményül kapott kimenet:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Beállíthatja a **ListBlobs** metódus **UseFlatBlobListing** paraméterét **true** értékre. Ennek eredményeképpen minden blob **cloudblockblobként**kerül vissza, könyvtártól függetlenül. Itt van a hívás **ListBlobs:**

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

és itt vannak az eredmények:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

További információ: [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Blobok letöltése
Blobok letöltéséhez először kérjen le egy blobhivatkozást, majd hívja a **DownloadToStream** metódust. A következő példa a **DownloadToStream** metódus segítségével helyezi át a blob tartalmát egy stream objektumra, amelyet egy helyi fájlban megőrizhet.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

A **DownloadToStream** metódussal a blob tartalmát szöveges sztringként is letöltheti.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Blobok törlése
Blob törléséhez először kap egy blob referencia, majd hívja meg a **Törlés** metódust.

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

