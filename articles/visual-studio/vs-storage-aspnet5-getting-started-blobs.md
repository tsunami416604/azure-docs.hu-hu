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
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 2e8060b44c395ad7c24e7778c0ef65148a3a45de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés az Azure Blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET mag)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az első lépések az Azure Blob Storage tárolót a Visual Studióban létrehozott vagy egy Azure storage-fiók, az ASP.NET Core projekt hivatkozik a Visual Studio kapcsolódó szolgáltatások hozzáadása párbeszédpanelen után.

Az Azure Blob storage egy olyan szolgáltatás nagy mennyiségű strukturálatlan adatok, amelyek elérhetők bárhol a világon HTTP vagy HTTPS PROTOKOLLON keresztül tárolásához. Egy blob bármilyen méretű lehet. Blobok lehetnek többek között a lemezképek, a hang- és fájlok, a nyers adatok és a fájlok. Ez a cikk ismerteti, hogyan lásson a blob storage a Visual Studio használatával Azure-tárfiók létrehozása után **kapcsolódó szolgáltatások hozzáadása** az ASP.NET Core projekt párbeszédpanel.

Ugyanúgy, mint a él fájlok, mappák, a tárolási BLOB élő tárolókban lévő. Miután létrehozta a tárolási, egy vagy több tárolóban hoz létre a tárolóban. Például "Lapkivágások" nevű tárolási, az úgynevezett "képek" képek tárolására storage hozhat létre, és más néven "hang", illetve hang-fájlok tárolására. A tárolók létrehozása után feltöltheti egyes blob fájlok hozzájuk. Lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md) programozott módon kezelésére a blobok további tájékoztatást.

## <a name="access-blob-containers-in-code"></a>Hozzáférés a blob tárolók kód
Programon keresztüli eléréséhez az ASP.NET Core projektek lévő blobokat, kell hozzáadnia a következő elemek, ha azok már nem létezik.

1. A következő kód névtér-deklarációk hozzáadása bármely kívánja programon keresztüli eléréséhez az Azure storage C#-fájl elejéhez.
   
        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;
2. Első egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A következő kódot használja a tárolási kapcsolati karakterlánc és a tárfiók adatait lekérése az Azure szolgáltatás konfigurációját.
   
         CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
            "<storage-account-name>",
            "<access-key>"), true);
   
    **Megjegyzés:** összes a fenti kódot a kód előtt használja a következő szakaszokban.
3. Használja a **CloudBlobClient** objektum lekérni egy **CloudBlobContainer** a tárfiókban lévő meglévő tároló mutató hivatkozás.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

## <a name="create-a-container-in-code"></a>A kód egy tároló létrehozása
Használhatja a **CloudBlobClient** létrehozni egy tárolót a tárfiókban lévő. Ehhez szüksége a adjon hozzá egy **CreateIfNotExistsAsync** hasonlóan a következő kódot:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "my-new-container."
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


**Megjegyzés:** az ASP.NET Core hajtsa végre az Azure storage hívásainak API-k aszinkron jellegűek. Lásd: [aszinkron programozás az Async és Await](http://msdn.microsoft.com/library/hh191443.aspx) további információt. Az alábbi kódot azt feltételezi, hogy aszinkron programozási módszerek vannak használatban.

Elérhetővé a tárolóban lévő fájlokat mindenki számára, hogy a tároló, a következő kód segítségével nyilvánossá állíthatja be.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
A blob-fájl feltöltése a tárolóba, beolvasni a tároló hivatkozását, és kérjen le egy blobhivatkozást segítségével. Után megszerezte a blobhivatkozást, akkor bármilyen streamet feltölthet adatok rá meghívásával a **UploadFromStreamAsync** metódust. Ez a művelet a blob hoz létre, ha már nem létezik, vagy felülírja, ha már létezik. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. A tároló majd hívása **ListBlobsSegmentedAsync** metódusának segítéségével lekérheti a blobokat és/vagy könyvtárakat belül. A **IListBlobItem** parancs visszaadásakor elérhető számos tulajdonság és metódus eléréséhez át kell alakítania azt **CloudBlockBlob**, **CloudPageBlob** vagy **CloudBlobDirectory** objektummá. Ha nem ismeri a blob típushoz, a TÍPUSELLENŐRZÉS segítségével határozza meg, amely típussá kell átalakítani. A következő kód bemutatja, hogyan kérhető le és küldhető a tárolóban lévő egyes elemek URI.

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

Mások módja van egy blob-tároló tartalmának megjelenítése. Lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) további információt.

## <a name="download-a-blob"></a>Blob letöltése
Egy blob letöltéséhez először kapnak a blobra mutató hivatkozást, és, majd hívja a **DownloadToStreamAsync** metódust. Az alábbi példában a **DownloadToStreamAsync** vihetők át a blob tartalmát egy stream objektumra, majd mentheti egy helyi fájl metódust.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named "myfile".
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Módon más blobok fájlokként menti. Lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) további információt.

## <a name="delete-a-blob"></a>Blob törlése
Blobok törléséhez először kapnak a blobra mutató hivatkozást, és, majd hívja a **DeleteAsync** metódust.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

