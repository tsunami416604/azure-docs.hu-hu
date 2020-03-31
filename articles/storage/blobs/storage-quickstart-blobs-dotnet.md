---
title: 'Rövid útmutató: Azure Blob storage-tár v12 - .NET'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Blob storage-beli ügyfélkódtár 12-es verziója a .NET-hez egy tárolót és egy blobot a Blob (objektum) tárolójában. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5cfb0430bc94d347afd75bc01170a71a7ad53565
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240505"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Rövid útmutató: Azure Blob storage-ügyféltár v12-es a .NET-hez

Ismerkedés az Azure Blob storage ügyfélkódtár v12-es . Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A csomag telepítéséhez kövesse a lépéseket, és próbálja ki az alapvető feladatok példakódját. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

> [!NOTE]
> Az előző SDK-verzió ismerkedéséhez olvassa el a [rövid útmutató: Azure Blob storage-ügyféltár a .NET.For](storage-quickstart-blobs-dotnet-legacy.md)started with the previous SDK version, for for start quickstart: Azure Blob storage client library for .NET .

A .NET-hez való Azure Blob storage-ügyfélkódtár 12-es használata:

* Tároló létrehozása
* Blob feltöltése az Azure Storage-ba
* Az összes blob listázása egy tárolóban
* A blob letöltése a helyi számítógépre
* Tároló törlése

[API-referenciadokumentáció](/dotnet/api/azure.storage.blobs) | [– könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | csomag[(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | [mintái](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure storage-fiók – [tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Az operációs rendszer aktuális [.NET Core SDK-ja.](https://dotnet.microsoft.com/download/dotnet-core) Ügyeljen arra, hogy az SDK-t kapja, és ne a futásidőt.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Blob storage-ügyfélkódtár v12-es .

### <a name="create-the-project"></a>A projekt létrehozása

*BlobQuickstartV12*nevű .NET Core alkalmazás létrehozása.

1. Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal hozzon létre egy új konzolalkalmazást *BlobQuickstartV12*néven. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Váltson az újonnan létrehozott *BlobQuickstartV12* könyvtárra.

   ```console
   cd BlobQuickstartV12
   ```

1. A BlobQuickstartV12 könyvtár oldalán hozzon létre egy másik *könyvtárat,* az úgynevezett data .In side the *BlobQuickstartV12* directory, create another directory called data . Ez az, ahol a blob adatfájlok jönnek létre és tárolják.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Amíg az alkalmazáskönyvtárban van, telepítse az Azure Blob storage-ügyféltár .NET-csomag a `dotnet add package` parancs használatával.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. A *Program.cs* fájl megnyitása a szerkesztőben
1. A `Console.WriteLine("Hello World!");` kimutatás eltávolítása
1. Direktívák hozzáadása `using`
1. A `Main` metódusdeklaráció frissítése az aszinkronkód támogatásához

Itt a kód:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob storage háromféle erőforrást kínál:

* A tárfiók
* Tároló a tárfiókban
* Blob a tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob-tárolási architektúra diagramja](./media/storage-blobs-introduction/blob1.png)

Az alábbi .NET osztályok segítségével használhatja ezeket az erőforrásokat:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient) `BlobServiceClient` Az osztály lehetővé teszi az Azure Storage-erőforrások és blobtárolók kezelésére.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient) `BlobContainerClient` Az osztály lehetővé teszi az Azure Storage-tárolók és azok blobjainak kezelésére.
* [BlobClient:](/dotnet/api/azure.storage.blobs.blobclient) `BlobClient` Az osztály lehetővé teszi az Azure Storage-blobok kezelésére.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo) `BlobDownloadInfo` Az osztály a blob letöltéséből visszaadott tulajdonságokat és tartalmat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan hajthatja végre a következőket az Azure Blob storage-ügyfélkódtárban .NET esetén:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát a [tárolási kapcsolat karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból.

Adja hozzá ezt `Main` a kódot a metódushoz:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód hozzáfűz egy GUID értéket a tároló nevéhez annak érdekében, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozzon létre egy példányt a [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) osztály. Ezután hívja meg a [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) metódust a tároló létrehozásához a tárfiókban.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Szövegfájlt hoz létre *data* a helyi adatkönyvtárban.
1. Egy [BlobClient-objektumra](/dotnet/api/azure.storage.blobs.blobclient) mutató hivatkozást kér le a [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) metódus hívásával a tárolón a [Tároló létrehozása](#create-a-container) szakaszból.
1. Feltölti a helyi szöveges fájlt a blobba az [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) metódus hívásával. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A blobok a tárolóban a [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) metódus hívásával. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listaelem-művelet csak azt az egy blobot adja vissza.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) metódus hívásával. A példakód hozzáadja a "DOWNLOADED" utótagot a fájlnévhez, így mindkét fájl látható a helyi fájlrendszerben.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az okat az erőforrásokat, amelyeket az alkalmazás a teljes tároló [deleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)használatával történő törlésével hozott létre. Törli az alkalmazás által létrehozott helyi fájlokat is.

Az alkalmazás szünetela felhasználói `Console.ReadLine` bevitel a hívás, mielőtt törli a blob, tároló és a helyi fájlokat. Ez jó alkalom annak ellenőrzésére, hogy az erőforrások valóban megfelelően jöttek-e létre, mielőtt törölné őket.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy *data* tesztfájlt a helyi adatmappában, és feltölti azt a Blob storage-ba. A példa ezután felsorolja a blobok a tárolóban, és letölti a fájlt egy új nevet, így össze lehet hasonlítani a régi és az új fájlokat.

Nyissa meg az alkalmazáskönyvtárat, majd építse fel és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

A tisztítási folyamat megkezdése előtt ellenőrizze, hogy az *adatmappában* van-e a két fájl. Ha megnyitja őket, láthatja, hogy megegyeznek.

Miután ellenőrizte a fájlokat, nyomja le az **Enter** billentyűt a tesztfájlok törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával.

A Blob storage mintaalkalmazásának megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob storage SDK-v12.NET-minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Oktatóanyagok, minták, gyorsindítások és egyéb dokumentációk esetén látogasson el [az Azure for .NET és a .NET Core fejlesztői webhelyre.](/dotnet/azure/)
* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
