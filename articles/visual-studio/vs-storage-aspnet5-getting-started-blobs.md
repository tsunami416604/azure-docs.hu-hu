---
title: Ismerkedés a blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core) | Microsoft Docs
description: Az Azure Blob Storage használatának első lépései a Visual Studio ASP.NET Core-projektben, miután létrehozott egy Storage-fiókot a Visual Studio Connected Services használatával
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
ms.openlocfilehash: c0f4124ffdb03a7a193791515444c4c7001378d7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511572"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Blob Storage használatát a Visual Studióban, miután létrehozta vagy hivatkozott egy Azure Storage-fiókot egy ASP.NET Core-projektben a Visual Studio **csatlakoztatott szolgáltatások** funkciójának használatával. A **csatlakoztatott szolgáltatások** művelet telepíti a megfelelő NuGet-csomagokat az Azure Storage-ban a projektben, és hozzáadja a Storage-fiókhoz tartozó kapcsolati karakterláncot a projekt konfigurációs fájljaihoz. (Lásd az Azure Storage szolgáltatással kapcsolatos általános információk [tárolási dokumentációját](https://azure.microsoft.com/documentation/services/storage/) .)

Az Azure Blob Storage egy olyan szolgáltatás, amely a világ bármely pontjáról HTTP vagy HTTPS használatával elérhető nagy mennyiségű strukturálatlan adat tárolására szolgál. Egyetlen blob lehet bármilyen méretű. A Blobok olyan dolgok, mint például a képek, a hang-és videofájlok, a nyers adatok és a dokumentumok fájljai. Ez a cikk a blob Storage használatának első lépéseit ismerteti, miután létrehozta az Azure Storage-fiókot a Visual Studio **csatlakoztatott szolgáltatásaival** egy ASP.net Core projektben.

Ahogy a mappákban élő fájlok, a Storage-Blobok élő tárolókban. A blob létrehozása után egy vagy több tárolót kell létrehoznia a blobban. Például egy "scrapbook" nevű blobban létrehozhat egy "images" nevű tárolót a képek tárolásához, és egy másikat a hangfájlok tárolásához. A tárolók létrehozása után külön fájlokat tölthet fel rájuk. Lásd [: gyors útmutató: A Blobok programozott kezelésével kapcsolatos további információkért](../storage/blobs/storage-quickstart-blobs-dotnet.md) töltse fel, töltse le és listázza a blobokat a .NET használatával.

Egyes Azure Storage API-k aszinkron módon működnek, és a cikkben szereplő kód feltételezi az aszinkron metódusok használatát. További információ: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-blob-containers-in-code"></a>BLOB-tárolók elérése a kódban

ASP.NET Core-projektekben lévő Blobok programozott eléréséhez hozzá kell adnia a következő kódot, ha még nem létezik:

1. Adja hozzá a `using` szükséges utasításokat:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Szerezzen `CloudStorageAccount` be egy objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-beli kapcsolódási karakterlánc és a Storage-fiók adatait az Azure-szolgáltatás konfigurációjától:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. A Storage `CloudBlobClient` -fiókban lévő `CloudBlobContainer` meglévő tárolóra mutató hivatkozás beszerzése egy objektum használatával:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Tároló létrehozása a kódban

A `CloudBlobClient` (z) használatával is létrehozhatja a tárolót a Storage-fiókban a következő hívásával `CreateIfNotExistsAsync`:

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

Egy blob-fájl tárolóba való feltöltéséhez szerezzen be egy tároló-referenciát, és használja a blob-hivatkozás beszerzéséhez. Ezután töltse fel az adatstreamet erre a hivatkozásra a `UploadFromStreamAsync` metódus meghívásával. Ez a művelet létrehozza a blobot, ha még nem létezik, és felülírja a meglévő blobot. 

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

A tárolóban lévő Blobok listázásához először szerezzen be egy tároló-hivatkozást, `ListBlobsSegmentedAsync` majd hívja meg a metódust, hogy lekérje a blobokat és/vagy címtárakat. Ha a tulajdonságok és metódusok gazdag készletét szeretné elérni a `IListBlobItem`visszaadott értékre, akkor `CloudPageBlob`azt egy `CloudBlobDirectory` , vagy egy `CloudBlockBlob`objektumba. Ha nem ismeri a blob típusát, használjon egy típus-ellenőrzési lehetőséget annak meghatározásához, hogy melyik legyen az.

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

Lásd [: gyors útmutató: Blobok feltöltése, letöltése és listázása a](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) .net-tel a blob-tárolók tartalmának listázásához.

## <a name="download-a-blob"></a>Blob letöltése

Egy blob letöltéséhez először kérjen egy hivatkozást a blobra, majd hívja meg a `DownloadToStreamAsync` metódust. A következő példa a `DownloadToStreamAsync` metódus használatával továbbítja a blob tartalmát egy stream-objektumba, amelyet később helyi fájlként menthet.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Lásd [: gyors útmutató: Blobok feltöltése, letöltése és listázása a](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) .NET használatával a Blobok fájlként való mentésének egyéb módjaival.

## <a name="delete-a-blob"></a>Blob törlése

BLOB törléséhez először kérjen meg egy hivatkozást a blobra, majd hívja meg a `DeleteAsync` metódust:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
