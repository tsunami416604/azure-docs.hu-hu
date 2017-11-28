---
title: "Ismerkedés a blob storage és a Visual Studio kapcsolódó szolgáltatások (az ASP.NET Core) |} Microsoft Docs"
description: "Első lépések a Visual Studio ASP.NET Core projektben Azure Blob storage használatával, a Visual Studio használatával storage-fiók létrehozása után kapcsolódó szolgáltatások"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: afd73bd0fd041a53fbe31aa3a5c23b3e27d7a9ec
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET mag)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Ez a cikk ismerteti az első lépések az Azure Blob storage a Visual Studióban létrehozott vagy a Visual Studio használatával Azure-tárfiók az ASP.NET Core projekt hivatkozik után **kapcsolódó szolgáltatások** szolgáltatás. A **kapcsolódó szolgáltatások** műveletet a megfelelő NuGet-csomagok az Azure storage a projekt eléréséhez telepíti, és a tárfiók kapcsolati karakterlánca ad hozzá a projekt konfigurációs fájlokat. (Lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkhoz.)

Az Azure Blob storage egy olyan szolgáltatás nagy mennyiségű strukturálatlan adatok, amelyek elérhetők bárhol a világon HTTP vagy HTTPS PROTOKOLLON keresztül tárolásához. Egy blob bármilyen méretű lehet. Blobok lehetnek többek között a lemezképek, a hang- és fájlok, a nyers adatok és a fájlok. Ez a cikk ismerteti, hogyan lásson a blob storage a Visual Studio használatával Azure-tárfiók létrehozása után **kapcsolódó szolgáltatások** ASP.NET Core projektben.

Ugyanúgy, mint a él fájlok, mappák, a tárolási BLOB élő tárolókban lévő. Miután létrehozta a blob, egy vagy több tárolóban, hogy a blob hoz létre. Például egy blobba "Lapkivágások" nevű, "képek" képek tárolására nevű tárolókat hozhat létre, és más néven "hang", illetve hang-fájlok tárolására. A tárolók létrehozása után feltöltheti a fájlokat hozzájuk. Lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md) programozott módon kezelésére a blobok további tájékoztatást.

Az Azure Storage API-k között aszinkron, és ebben a cikkben a kód azt feltételezi, hogy aszinkron metódusok használatban van. Lásd: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) további információt.

## <a name="access-blob-containers-in-code"></a>Hozzáférés a blob tárolók kód

Az ASP.NET Core projektek blobok programon keresztüli eléréséhez, kell adja hozzá a következő kódot Ha még nincs telepítve:

1. Adja hozzá a szükséges `using` utasításokat:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Első egy `CloudStorageAccount` objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs használja a következő kódot:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Használja a `CloudBlobClient` objektum eléréséhez a `CloudBlobContainer` a tárfiókban lévő meglévő tároló hivatkozást:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>A kód egy tároló létrehozása

Használhatja a `CloudBlobClient` hozhat létre tárolót a tárfiókban lévő meghívásával `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Elérhetővé a tárolóban lévő fájlokat mindenki, állítsa be a tároló nyilvánosak legyenek:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba

A blob-fájl feltöltése a tárolóba, beolvasni a tároló hivatkozását, és kérjen le egy blobhivatkozást segítségével. Majd feltölteni meghívásával a hivatkozás bármilyen streamet a `UploadFromStreamAsync` metódust. Ez a művelet a blob hoz létre, ha már nem létezik, és felülírja a meglévő blob. 

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

A tárolóban lévő blobok listázásához először get a tároló hivatkozását, majd hívja a `ListBlobsSegmentedAsync` metódusának segítéségével lekérheti a blobokat és/vagy könyvtárakat belül. Számos tulajdonságai és metódusai visszaadásakor eléréséhez `IListBlobItem`, alakítania azt egy `CloudBlockBlob`, `CloudPageBlob`, vagy `CloudBlobDirectory` objektum. Ha nem ismeri a blob típus, akkor a TÍPUSELLENŐRZÉS segítségével határozza meg, amely típussá kell átalakítani.

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

Lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) az egyéb módjai a blob-tároló tartalmának megjelenítése.

## <a name="download-a-blob"></a>Blob letöltése

Töltse le a blob, a blobra mutató hivatkozás egy első get, majd hívja a `DownloadToStreamAsync` metódust. Az alábbi példában a `DownloadToStreamAsync` vihetők át a blob tartalmát egy stream objektumra, majd mentheti egy helyi fájl metódust.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) az egyéb módjai blobok fájlokként menti.

## <a name="delete-a-blob"></a>Blob törlése

A blob, a blobra mutató hivatkozás egy első get törli, majd hívja a `DeleteAsync` módszert:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
