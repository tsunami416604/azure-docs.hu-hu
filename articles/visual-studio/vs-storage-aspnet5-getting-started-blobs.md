---
title: A blobstorage használatának első lépései a Visual Studio (ASP.NET Core) használatával
description: Az Azure Blob storage használatának első lépései egy Visual Studio ASP.NET Core projektben, miután létrehozott egy tárfiókot a Visual Studio csatlakoztatott szolgáltatásaival
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298834"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob storage-szal és a Visual Studio-hoz kapcsolódó szolgáltatásokkal (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Ez a cikk bemutatja, hogyan kezdheti el használni az Azure Blob storage-t a Visual Studióban, miután létrehozott vagy hivatkozott egy Azure-tárfiókra egy ASP.NET Core projektben a Visual Studio **Connected Services** szolgáltatás használatával. A **Connected Services** művelet telepíti a megfelelő NuGet csomagokat az Azure storage eléréséhez a projektben, és hozzáadja a tárfiók kapcsolati karakterláncát a projekt konfigurációs fájljaihoz. (Az Azure Storage szolgáltatással kapcsolatos általános tudnivalókat a [Storage dokumentációjában](https://azure.microsoft.com/documentation/services/storage/) találja.)

Az Azure Blob storage olyan szolgáltatás, amely nagy mennyiségű strukturálatlan adatot tárol, amelyek a világ bármely pontjáról http-n vagy HTTPS-en keresztül érhetők el. Egyetlen blob bármilyen méretű lehet. A blobok lehetnek például képek, hang- és videofájlok, nyers adatok és dokumentumfájlok. Ez a cikk ismerteti, hogyan kell elkezdeni a blob storage létrehozása után egy Azure-tárfiókot a Visual Studio **Connected Services** egy ASP.NET Core projekt használatával.

Ahogy a fájlok mappákban élnek, a tárolóblobok is tárolókban élnek. Miután létrehozott egy blobot, hozzon létre egy vagy több tárolót a blobban. Például egy blob úgynevezett "Scrapbook", hozhat létre tárolók úgynevezett "képek" képek tárolására és egy másik úgynevezett "audio" tárolására audio fájlokat. A tárolók létrehozása után feltöltheti az egyes fájlokat. A blobok programozott használatával történő [feltöltés, letöltés és listablobok feltöltése, letöltése és listázása](../storage/blobs/storage-quickstart-blobs-dotnet.md) című témakörben talál további információt.

Az Azure Storage API-k egy része aszinkron, és a jelen cikkben szereplő kód feltételezi, hogy aszinkron metódusok használatosak. További információt az [Aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) című témakörben talál.

## <a name="access-blob-containers-in-code"></a>Blob-tárolók elérése kódban

A blobok programozott eléréséhez ASP.NET alapprojektekben, hozzá kell adnia a következő kódot, ha még nincs meg:

1. Adja hozzá `using` a szükséges állításokat:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Szerezzen `CloudStorageAccount` be egy objektumot, amely a tárfiók adatait jelöli. A következő kód segítségével lejuthat a tárolási kapcsolat ihúrjára és a tárfiókadataira az Azure szolgáltatáskonfigurációjából:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. `CloudBlobClient` Objektum használatával hivatkozást `CloudBlobContainer` kaphat a tárfiókban lévő meglévő tárolóra:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Tároló létrehozása kódban

A segítségével `CloudBlobClient` tárolót is létrehozhat a tárfiókban a következő hívással: `CreateIfNotExistsAsync`

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Ha a tárolóban lévő fájlokat mindenki számára elérhetővé szeretné tenni, állítsa a tárolót nyilvánosra:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba

Blob-fájl feltöltése egy tárolóba, szerezzen be egy tárolóreferencia-t, és használja azt blob-hivatkozás lefoglalásához. Ezután töltse fel az adatfolyamot `UploadFromStreamAsync` erre a hivatkozásra a metódus hívásával. Ez a művelet létrehozza a blobot, ha még nincs ott, és felülír egy meglévő blobot. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A blobok egy tárolóban való listázásához először `ListBlobsSegmentedAsync` szerezzen be egy tárolóhivatkozást, majd hívja meg a metódusát a blobok és/vagy könyvtárak lekéréséhez. `IListBlobItem`A visszaadott tulajdonságok és metódusok gazdag készletének `CloudBlockBlob`eléréséhez adja azt egy , `CloudPageBlob`vagy `CloudBlobDirectory` objektumra. Ha nem ismeri a blob típusát, használjon típusellenőrzést annak meghatározásához, hogy melyikre szeretné azt vetni.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
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
} while (token != null);
```

Lásd: [Gyorsútmutató: Blobok feltöltése, letöltése és listablobok feltöltése](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) a .NET használatával a blobtároló tartalmának listázásához.

## <a name="download-a-blob"></a>Blob letöltése

Blob letöltéséhez először kap egy hivatkozást a `DownloadToStreamAsync` blob, majd hívja meg a metódust. A következő példa `DownloadToStreamAsync` a módszer rel átad hatja át a blob tartalmát egy adatfolyam-objektumba, amelyet ezután helyi fájlként menthet.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Olvassa [el a gyorsútmutatót: Blobok feltöltése, letöltése és listázása a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) a blobok fájlként való mentésének egyéb módjaiért.

## <a name="delete-a-blob"></a>Blob törlése

Blob törléséhez először kapjon hivatkozást a `DeleteAsync` blobra, majd hívja meg a metódust:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
