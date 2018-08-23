---
title: Ismerkedés a blob storage és a Visual Studio csatlakoztatott szolgáltatásainak (az ASP.NET Core) |} A Microsoft Docs
description: Ismerkedés az Azure Blob storage egy Visual Studio az ASP.NET Core-projektet a Visual Studio kapcsolódó szolgáltatásaival használata a storage-fiók létrehozása után
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: b2b707585df4a7ec26f689b4213be74bdaab680d
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054916"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob storage és a Visual Studio csatlakoztatott szolgáltatásainak (ASP.NET-mag)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Blob storage a Visual Studióban létrehozott vagy hivatkozott Azure storage-fiók egy ASP.NET Core-projektet a Visual Studio használatával után **csatlakoztatott szolgáltatás** funkció. A **csatlakoztatott szolgáltatás** művelet telepíti a megfelelő NuGet-csomagokat a projekthez az Azure storage eléréséhez, és a projekt konfigurációs fájlokat ad hozzá a tárfiók kapcsolati karakterláncát. (Lásd: [Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkhoz.)

Az Azure Blob storage szolgáltatás nagy mennyiségű strukturálatlan adat, amely segítségével bárhonnan elérhetők HTTP- vagy HTTPS keresztül a világon tárolásához. Egy blob bármilyen méretű lehet. Blobok lehet többek között a képeket, hang- és videofájlok, nyers adatokat és fájlokat. Ez a cikk bemutatja, hogyan kezdheti el a blob storage, a Visual Studio használatával Azure storage-fiók létrehozása után **csatlakoztatott szolgáltatás** ASP.NET Core-projektben.

Fájlok él, mappák, mint a tárolóblobok élő tárolókban. Miután létrehozott egy blobot, létrehoz egy vagy több tárolók, hogy a blob. Például egy blobba "Lapkivágások" nevű, "rendszerképek" képek tárolásához nevű tárolókat hozhat létre, és más néven "hang" zenei fájlok tárolására. Miután létrehozta a tárolók, egyes fájlokat tölthet fel őket. Lásd: [a rövid útmutató: blobok feltöltése, letöltése, és lista .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md) programozott módon a blobok módosítása további tájékoztatást.

Az Azure Storage API-k némelyike aszinkron, és ebben a cikkben a kód azt feltételezi, hogy az aszinkron módszereket használ. Lásd: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) további információt.

## <a name="access-blob-containers-in-code"></a>Blob-tárolók hozzáférési kód

Programozott módon érheti el az ASP.NET Core-projektek a blobok, meg kell az alábbi kódot, ha nincs már:

1. Adja hozzá a szükséges `using` utasításokat:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Get- `CloudStorageAccount` objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure-szolgáltatás konfigurációs használja a következő kódot:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Használja a `CloudBlobClient` objektum lekérése egy `CloudBlobContainer` egy meglévő tárolót, a tárfiókban található hivatkozást:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Hozzon létre egy tárolót a code-ban

Is használhatja a `CloudBlobClient` hoz létre a tárolót a tárfiókjában meghívásával `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Ahhoz, hogy a tárolóban lévő fájlokat érhető el mindenki számára elérhető, állítsa be a tároló nyilvános:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba

Fájlfeltöltés blob egy tárolóba, kérje le a tároló hivatkozását, és annak segítségével kérje le egy blobhivatkozást. Ezután töltse fel bármilyen streamet hivatkozó meghívásával a `UploadFromStreamAsync` metódust. Ez a művelet létrehozza a blobot, ha még nem szerepel ott, és felülírja a meglévő blobokhoz. 

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

A tárolóban lévő blobok listázásához először get a tároló hivatkozását, majd hívja a `ListBlobsSegmentedAsync` metódusának segítéségével lekérheti a blobok és/vagy a benne található könyvtárak. Számos tulajdonság és metódus visszaadásakor eléréséhez `IListBlobItem`, alakítania azt egy `CloudBlockBlob`, `CloudPageBlob`, vagy `CloudBlobDirectory` objektum. Ha nem ismeri a blob Ismereten adja meg, hogy mely típussá kell átalakítani.

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

Lásd: [a rövid útmutató: blobok feltöltése, letöltése, és lista .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) blob-tároló tartalmának listázását egyéb módjaira vonatkozóan.

## <a name="download-a-blob"></a>Blob letöltése

A blob, az első get egy hivatkozást a blob letöltéséhez, majd hívja a `DownloadToStreamAsync` metódust. Az alábbi példában a `DownloadToStreamAsync` módszerrel kell továbbítania a blob tartalmát egy stream objektumra, majd mentheti egy helyi fájlba.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Lásd: [a rövid útmutató: feltöltése, letöltése és blobok listázása a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) blobok Mentés másként fájlok egyéb módjaira vonatkozóan.

## <a name="delete-a-blob"></a>Blob törlése

Töröl egy blobot, egy hivatkozást a blob első get, majd hívja a `DeleteAsync` módszer:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
