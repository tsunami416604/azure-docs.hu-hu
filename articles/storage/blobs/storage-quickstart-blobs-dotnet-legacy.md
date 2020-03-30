---
title: 'Rövid útmutató: Azure Blob storage-ügyféltár a .NET-hez'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Blob storage-ügyfélkódtár .NET egy tároló takarásés egy blob (objektum) tároló. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b243d05619642e1dd3ad8dfe2bbe1d0a9661b773
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75351305"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Rövid útmutató: Azure Blob storage-ügyféltár v11 a .NET

Ismerkedés az Azure Blob Storage ügyfélkódtár v11-es használatával. Az Azure Blob Storage a Microsoft objektumtárolási megoldása a felhőszámára. A csomag telepítéséhez kövesse a lépéseket, és próbálja ki az alapvető feladatok példakódját. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

A .NET alkalmazáshoz az Azure Blob Storage ügyféltár használatával:

* Tároló létrehozása
* Engedélyek beállítása tárolóhoz
* Blob létrehozása az Azure Storage-ban
* A blob letöltése a helyi számítógépre
* Az összes blob listázása egy tárolóban
* Tároló törlése

[API-referenciadokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [– könyvtár forráskódjának](https://github.com/Azure/azure-storage-net/tree/master/Blob) | csomag[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [mintái](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Az operációs rendszer aktuális [.NET Core SDK-ja.](https://dotnet.microsoft.com/download/dotnet-core) Ügyeljen arra, hogy az SDK-t kapja, és ne a futásidőt.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Blob Storage-ügyfélkódtár .NET.

### <a name="create-the-project"></a>A projekt létrehozása

Először hozzon létre egy .NET Core alkalmazást *blob-quickstart*néven.

1. Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal hozzon létre egy új konzolalkalmazást *blob-quickstart*névvel. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Váltson át az újonnan létrehozott *blob-quickstart* mappára, és építse fel az alkalmazást annak ellenőrzéséhez, hogy minden rendben van-e.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

A build várható kimenetének valahogy így kell kinéznie:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>A csomag telepítése

Amíg az alkalmazáskönyvtárban van, telepítse az Azure Blob Storage-ügyfélkönyvtárat a .NET-csomaghoz a `dotnet add package` parancs használatával.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. A *Program.cs* fájl megnyitása a szerkesztőben
2. A `Console.WriteLine` kimutatás eltávolítása
3. Direktívák hozzáadása `using`
4. Hozzon `ProcessAsync` létre egy olyan módszert, amelyben a példa fő kódja
5. Aszinkron módon hívja meg `ProcessAsync` a metódust`Main`

Itt a kód:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

Amikor a mintaalkalmazás kérelmet küld az Azure Storage-nak, engedélyezni kell. Kérés engedélyezéséhez adja hozzá a tárfiók hitelesítő adatait az alkalmazáshoz kapcsolati karakterláncként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje `<yourconnectionstring>` le a tényleges kapcsolati karakterláncra.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Miután hozzáadja a környezeti változót a Windows rendszerben, el kell indítania a parancsablak egy új példányát.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

A környezeti változó hozzáadása után indítsa újra azösszes olyan futó programot, amelynek el kell olvasnia a környezeti változót. Például a folytatás előtt indítsa újra a fejlesztői környezetet vagy a szerkesztőt.

## <a name="object-model"></a>Objektummodell

Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob storage háromféle erőforrást kínál:

* A tárfiók.
* Tároló a tárfiókban
* Blob egy tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob-tárolási architektúra diagramja](./media/storage-quickstart-blobs-dotnet/blob1.png)

Az alábbi .NET osztályok segítségével használhatja ezeket az erőforrásokat:

* [CloudStorageAccount:](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) `CloudStorageAccount` Az osztály az Azure storage-fiókját jelöli. Ezzel az osztállyal engedélyezheti a hozzáférést a Blob storage-hoz a fiók hozzáférési kulcsaival.
* [CloudBlobClient:](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) `CloudBlobClient` Az osztály hozzáférést biztosít a Blob szolgáltatás a kódban.
* [CloudBlobContainer:](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) `CloudBlobContainer` Az osztály egy blob tárolót jelöl a kódban.
* [CloudBlockBlob:](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) `CloudBlockBlob` Az objektum egy blokkblobot jelöl a kódban. A blokkblobok önállóan felügyelhető adatblokkokból állnak.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan hajthatja végre a következőket az Azure Blob storage-ügyfélkódtárban .NET esetén:

   * [Az ügyfél hitelesítése](#authenticate-the-client)
   * [Tároló létrehozása](#create-a-container)
   * [Engedélyek beállítása tárolóhoz](#set-permissions-on-a-container)
   * [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
   * [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
   * [Blobok letöltése](#download-blobs)
   * [Tároló törlése](#delete-a-container)

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alábbi kód ellenőrzi, hogy a környezeti változó tartalmaz-e kapcsolati karakterláncot, amely elemezhető a storage-fiókra mutató [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) objektum létrehozásához. A kapcsolati sztring érvényességének ellenőrzéséhez alkalmazza a [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet) metódust. Ha `TryParse` sikeres, inicializálja `storageAccount` a `true`változót, és visszaadja a értékét.

Adja hozzá ezt `ProcessAsync` a kódot a metódushoz:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> A cikkben szereplő többi művelet végrehajtásához `// ADD OTHER OPERATIONS HERE` cserélje le a fenti kódot a következő szakaszokban szereplő kódrészletekre.

### <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához előbb hozza létre a [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) objektum egy példányát, amely a tárfiókban található Blob Storage-ra mutat. Ezután hozza létre a [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) objektum egy példányát, majd a tárolót.

Ebben az esetben a kód meghívja a [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metódust a tároló létrehozásához. A rendszer egy GUID azonosítót fűz a tároló nevének végéhez, hogy biztosítsa annak egyediségét. Éles környezetben gyakran célszerű a [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metódus t, hogy hozzon létre egy tárolót csak akkor, ha még nem létezik.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Engedélyek beállítása tárolóhoz

Állítsa be a tároló engedélyeit, hogy a tárolóban lévő blobok nyilvánosak. A nyilvános blobok névtelenül elérhetők bármely ügyfél által.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet egy `CloudBlockBlob` objektumra mutató hivatkozást kap az előző szakaszban létrehozott tároló [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metódusának meghívásával. Ezután feltölti a kijelölt helyi fájlt a blobba az [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) metódus hívásával. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Listázza a blobok a tárolóban a [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metódus használatával. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listaelem-művelet csak azt az egy blobot adja vissza.

Ha túl sok blobok egy hívás (alapértelmezés szerint több mint 5000), majd a `ListBlobsSegmentedAsync` módszer a teljes eredményhalmaz egy szegmensét adja vissza, és egy folytatási jogkivonatot. A blobhalmaz következő szeletének visszaadásához az előző hívás által visszaadott folytatási tokent kell megadnia. Egészen addig, amíg a folytatási token nullértékű nem lesz. A nullértékű folytatási token jelzi, hogy az összes blob le lett kérve. A kód bemutatja, hogyan kell használni a folytatási jogkivonatot a legjobb eljárások érdekében.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a helyi fájlrendszerhez a [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódus használatával. A példakód hozzáadja a "_DOWNLOADED" utótagot a blob nevéhez, így mindkét fájlt láthatja a helyi fájlrendszerben.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az okat az erőforrásokat, amelyeket az alkalmazás a [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)használatával a teljes tároló törlésével hozott létre. Ha szeretné, a helyi fájlokat is törölheti.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztfájlt a helyi *MyDocuments* mappában, és feltölti azt a Blob storage-ba. A példa ezután felsorolja a blobok a tárolóban, és letölti a fájlt egy új nevet, így össze lehet hasonlítani a régi és az új fájlokat.

Nyissa meg az alkalmazáskönyvtárat, majd építse fel és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Az **Enter** billentyű lenyomása után az alkalmazás törli a Storage-tárolót és a fájlokat. Mielőtt törölné ezeket, ellenőrizze a két fájlt a *MyDocuments* mappában. Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a blob tartalmának a megtekintéséhez.

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával.

Ha meg szeretné tudni, hogyan hozhat létre olyan webalkalmazást, amely képet tölt fel a Blob storage-ba, folytassa a következőkkel:

> [!div class="nextstepaction"]
> [Rendszerképek feltöltése és feldolgozása](storage-upload-process-images.md)

* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
* Ha szeretne megtekinteni egy olyan mintaalkalmazást, amely a Windowshoz készült Visual Studióból helyezhető üzembe, tekintse meg [az Azure Blob Storage-et használó .NET fényképgaléria-mintawebalkalmazást](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
