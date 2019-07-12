---
title: 'Gyors útmutató: Az Azure Blob storage ügyféloldali kódtára a .NET-hez'
description: Ez a rövid útmutatóban megismerheti, hogyan hozzon létre egy tárolót, és a egy blob (objektum) a Blob storage-ban az Azure Blob storage ügyféloldali kódtára a .NET használatával. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 70afb39be696b660d3f85ae0c4b2c108bceb318c
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625676"
---
# <a name="quickstart-azure-blob-storage-client-library-for-net"></a>Gyors útmutató: Az Azure Blob storage ügyféloldali kódtára a .NET-hez

Ismerkedés az Azure Blob Storage ügyféloldali kódtára a .NET-hez. Az Azure Blob Storage a Microsoft objektum tárolási megoldása a felhőben. Telepítse a csomagot, és próbálja ki kódpéldákat biztosít alapvető feladatok a lépéseket követve. A BLOB storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

Használja az Azure Blob Storage ügyféloldali kódtára a .NET-keretrendszerhez készült:

* Tároló létrehozása
* A tároló engedélyeinek beállítása
* Blob létrehozása az Azure Storage-ban
* A blob letöltése a helyi számítógépre
* Összes tárolóban lévő blobok listázása
* Tároló törlése

[API-referenciadokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [kódtár forráskódját](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [minták](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Az Azure Storage-fiók – [storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [NET Core SDK-t 2.1](https://dotnet.microsoft.com/download/dotnet-core), vagy újabb, az operációs rendszer

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy használható az az Azure Blob Storage ügyféloldali kódtára a .NET projekt előkészítése.

### <a name="create-the-project"></a>A projekt létrehozása

Először hozzon létre egy .NET Core-alkalmazást nevű **blob-quickstart**.

1. A konzolablakban (például a cmd parancsot, a PowerShell vagy a Bash), használja a `dotnet new` paranccsal hozzon létre egy új konzolalkalmazást nevű **blob-quickstart**. Ez a parancs létrehoz egy egyszerű "Hello World" C# a projekt egy forrásfájl: **Program.cs**.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Váltson át az újonnan létrehozott **blob-quickstart** mappát és annak ellenőrzéséhez, hogy minden jól az alkalmazást a build.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

A build várható kimenete következőhöz hasonlóan kell kinéznie:

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

Miközben továbbra is az alkalmazás könyvtárában, telepítse az Azure Blob Storage ügyféloldali kódtára a .NET-csomaghoz a `dotnet add package` parancsot.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Állítsa be az alkalmazás-keretrendszer

A projekt könyvtárból:

1. Nyissa meg a Program.cs fájlra a szerkesztőben
2. Távolítsa el a **Console.WriteLine("a(z)** utasítás
3. Adjon hozzá **használatával** irányelvek
4. Hozzon létre egy **ProcessAsync** hol helyezkednek el a fő példa kódja a következő metódus
5. Aszinkron módon hívja a **ProcessAsync** módszerrel **fő**

A kód itt látható:

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

A mintaalkalmazásnak hitelesítenie kell a tárfiókhoz való hozzáférést. A hitelesítéshez adja meg az alkalmazásnak a tárfiókja hitelesítő adatait kapcsolati sztring formájában. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le `<yourconnectionstring>` tényleges kapcsolati karakterláncra.

Miután hozzáadta a környezeti változó, szükség lehet indítania minden futó programot, amelyeket a környezeti változó beolvasására kell. Például ha szerkesztőként a Visual Studio használ, indítsa újra a Visual Studio a példa futtatása előtt.

#### <a name="windows"></a>Windows

```cmd
setx STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

#### <a name="linux"></a>Linux

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

## <a name="object-model"></a>Hálózatiobjektum-modellje

Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Strukturálatlan adatok olyan adat, amelyet nem követi a egy adott adatmodellhez vagy egy definíciót, például szöveg vagy bináris adatok. A BLOB storage három típusú erőforrásokat nyújt:

* A storage-fiók.
* Egy tároló, a storage-fiókban
* A blob-tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Blob storage-architektúra ábrája](./media/storage-quickstart-blobs-dotnet/blob1.png)

A következő .NET-osztályok használhatja ezeket az erőforrásokat:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): A **CloudStorageAccount** osztály jelöli az Azure storage-fiókjában. Ez az osztály használatával hozzáférést a Blob storage használatával a fiók hozzáférési kulcsait.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): A **CloudBlobClient** az osztály a Blob szolgáltatáshoz, a kódban hozzáférési pontot biztosít.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): A **CloudBlobContainer** osztály jelöli egy blobtárolót a kódban.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): A **CloudBlockBlob** objektum képviseli a kódban a blokkblobok. A blokkblobok önállóan felügyelhető adatblokkokból állnak.

## <a name="code-examples"></a>Hitelesítésikód-példák

Ezeket a példa kódtöredékek bemutatják, hogyan hajthat végre a következő, az Azure Blob storage ügyféloldali kódtára a .NET-hez készült:

   * [Az ügyfél hitelesítése](#authenticate-the-client)
   * [Tároló létrehozása](#create-a-container)
   * [A tároló engedélyeinek beállítása](#set-permissions-on-a-container)
   * [BLOB feltöltése tárolóba](#upload-blobs-to-a-container)
   * [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
   * [Blobok letöltése](#download-blobs)
   * [Tároló törlése](#delete-a-container)

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alábbi kódot ellenőrzi, hogy a környezeti változó létrehozásához elemezhető kapcsolati karakterláncot tartalmaz egy [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) objektum a tárfiókra mutat. A kapcsolati sztring érvényességének ellenőrzéséhez alkalmazza a [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet) metódust. Ha a **TryParse** sikeres, a minta inicializálja a *storageAccount* változót, és **igaz** értéket ad vissza.

Adja hozzá a kódot a **ProcessAsync** módszer:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("STORAGE_CONNECTION_STRING");

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
        "Add an environment variable named 'STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Ez a cikk a többi művelet végrehajtásához cserélje le a **/ / egyéb műveletek itt hozzáadása** a következő szakaszokban szereplő kódrészletek a fenti kódban.

### <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához előbb hozza létre a [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) objektum egy példányát, amely a tárfiókban található Blob Storage-ra mutat. Ezután hozza létre a [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) objektum egy példányát, majd a tárolót.

Ebben az esetben a kód meghívja a [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metódussal hoz létre a tárolót. A rendszer egy GUID azonosítót fűz a tároló nevének végéhez, hogy biztosítsa annak egyediségét. Éles környezetben általában célszerű inkább a [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metódus csak akkor, ha azt nem létezik, hozzon létre egy tárolót.

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

### <a name="set-permissions-on-a-container"></a>A tároló engedélyeinek beállítása

Engedélyek beállítása a tárolón, hogy minden a tárolóban lévő blobok nyilvánosak legyenek. A nyilvános blobok névtelenül elérhetők bármely ügyfél által.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>BLOB feltöltése tárolóba

Az alábbi kódrészlet lekér egy hivatkozást egy **CloudBlockBlob** meghívásával objektum a [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metódus az előző szakaszban létrehozott tárolón. Majd feltölti a kiválasztott helyi fájlt a blobba meghívásával a [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) metódust. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

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

A tárolóban található blobok listájának a használatával a [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metódust. Ebben az esetben csak egy blobbal bővült a tárolóhoz, így a listázási művelet csak ezt az egy blobot adja vissza.

Ha túl sok (alapértelmezés szerint több mint 5000) blob van egy hívás válaszában, akkor a **ListBlobsSegmentedAsync** metódus visszaadja a teljes eredményhalmaz egy szeletét, valamint egy folytatási tokent. A blobhalmaz következő szeletének visszaadásához az előző hívás által visszaadott folytatási tokent kell megadnia. Egészen addig, amíg a folytatási token nullértékű nem lesz. A nullértékű folytatási token jelzi, hogy az összes blob le lett kérve. A kód bemutatja, hogyan használható a folytatási jogkivonat, amely ajánlott eljárásként alkalmazandó.

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

Töltse le a korábban a helyi fájlrendszerbe használatával létrehozott blob az [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódust. Példakód hozzáadja a blob nevét utótagot a "_DOWNLOADED", így mindkét fájlt láthatja a helyi fájlrendszerben.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kódot a létrehozott törlésével a teljes tárolót használó alkalmazást megtisztítja az erőforrások [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Ha szeretné, a helyi fájlokat is törölheti.

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

Ez az alkalmazás létrehoz egy tesztfájlt a helyi **MyDocuments** mappát, és feltölti a Blob storage. A példa ezután listázza a tárolóban található blobokat, és letölti a fájlt új néven, hogy össze lehessen hasonlítani a régi és új fájlokat.

Ha Visual Studiót használt szerkesztőként, a futtatáshoz nyomja le az **F5** billentyűt.

Ellenkező esetben lépjen az alkalmazás könyvtárába, majd hozhat létre, és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

A mintaalkalmazás kimenete az alábbi példához hasonló:

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

Az **Enter** billentyű lenyomása után az alkalmazás törli a Storage-tárolót és a fájlokat. Mielőtt törölné ezeket, ellenőrizze a két fájlt a **MyDocuments** mappában. Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a blob tartalmának a megtekintéséhez.

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával.

Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely feltölt egy képet a Blob storage, továbbra is:

> [!div class="nextstepaction"]
> [Feltöltése és feldolgozása a kép](storage-upload-process-images.md)

* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
* Ha szeretne megtekinteni egy olyan mintaalkalmazást, amely a Windowshoz készült Visual Studióból helyezhető üzembe, tekintse meg [az Azure Blob Storage-et használó .NET fényképgaléria-mintawebalkalmazást](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
