---
title: Azure rövid útmutató – Blobok feltöltése, letöltése és listázása az Azure Storage-ban a .NET használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre. Majd a Storage .NET-hez készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: b84a56996a335f8a137c4219c55b9878e39b5a3b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a .NET használatával

A rövid útmutató azt ismerteti, hogyan használható az Azure Storage-hez készült .NET ügyfélkódtár a blokkblobok feltöltésére, letöltésére és listázására egy tárolóban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez előbb hozzon létre egy Azure-tárfiókot az [Azure Portalon](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). A fiók létrehozásával kapcsolatos útmutatóért lásd a [tárfiók létrehozását](../common/storage-quickstart-create-account.md) ismertető szakaszt.

Ezután töltse le és telepítse a .NET Core 2.0 az operációs rendszernek megfelelő verzióját. Ha Windows rendszert használ, igény szerint telepítheti a Visual Studiót és használhatja a .NET-keretrendszert is. Emellett telepíthet egy, az operációs rendszerrel használható szerkesztőprogramot is.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- A [.NET Core for Windows](https://www.microsoft.com/net/download/windows) vagy a [.NET-keretrendszer](https://www.microsoft.com/net/download/windows) telepítése (a Windows rendszerhez készült Visual Studio része)
- Telepítse [a Windowshoz készült Visual Studio programot](https://www.visualstudio.com/). Ha .NET Core keretrendszert használ, a Visual Studio telepítése nem kötelező.  

A .NET Core és a .NET-keretrendszer közötti választással kapcsolatos további információért lásd a [.NET Core és a .NET-keretrendszer közötti, kiszolgálóalkalmazások esetén való választásról](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) szóló részt.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Telepítse a [.NET Core for Linux keretrendszert](https://www.microsoft.com/net/download/linux).
- Emellett telepítheti [a Visual Studio Code programot](https://www.visualstudio.com/) és a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068) is.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Telepítse a [.NET Core for macOS keretrendszert](https://www.microsoft.com/net/download/macos).
- Emellett telepítheti [a Visual Studio for Mac programot](https://www.visualstudio.com/vs/visual-studio-mac/) is.

---

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt mintaalkalmazás egy egyszerű konzolalkalmazás. A mintaalkalmazást megismerheti a [GitHubon](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a storage-blobs-node-quickstart mappát, nyissa meg, és kattintson duplán a storage-blobs-dotnet-quickstart.sln fájlra. 

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása

Az alkalmazás futtatásához meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Másolja a kapcsolati karakterláncát az Azure Portalról, és írja be egy új környezeti változóba. A minta beolvassa a kapcsolati karakterláncot a környezeti változóból, és annak használatával hitelesíti az Azure Storage felé intézett kéréseit.

### <a name="copy-your-connection-string-from-the-azure-portal"></a>A kapcsolati karakterlánc másolása az Azure Portalról

A kapcsolati karakterlánc másolása:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet.
4. Keresse meg a **Kapcsolati karakterlánc** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati karakterlánc másolásához.  

    ![A kapcsolati karakterlánc az Azure Portalról történő másolását bemutató képernyőkép](media/storage-quickstart-blobs-dotnet/portal-connection-string.png)

## <a name="write-your-connection-string-to-an-environment-variable"></a>A kapcsolati karakterlánc írása egy környezeti változóba

Ezt követően írja az új környezeti változót az alkalmazást futtató helyi gépre. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le a `<yourconnectionstring>` kifejezést a tényleges kapcsolati karakterláncra:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Ha például Visual Studiót használt szerkesztőként, indítsa újra a minta futtatása előtt. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

---

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta egy tesztfájlt hoz létre a helyi **MyDocuments** mappában, és feltölti a Blob Storage-ba. A minta ezután kilistázza a tárolóban található blobokat, majd letölti a fájlt egy új néven, hogy össze lehessen hasonlítani a régi és az új fájlt. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Ha Visual Studiót használt szerkesztőként, a futtatáshoz nyomja le az **F5** billentyűt. 

Máskülönben lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

---

A mintaalkalmazás kimenete az alábbihoz hasonló lesz:

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

Az **Enter** billentyű lenyomása után az alkalmazás törli a Storage-tárolót és a fájlokat. Mielőtt törölné ezeket, ellenőrizze a két fájlt a **MyDocuments** mappában. Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a blob tartalmának a megtekintéséhez.

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg a Program.cs fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="try-parsing-the-connection-string"></a>Kapcsolati karakterlánc elemzése

A minta az első lépésben ellenőrzi, hogy a környezeti változó tartalmaz-e egy olyan kapcsolati karakterláncot, amelynek elemzésével létrehozható egy, a tárfiókra mutató [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) objektum. A kapcsolati karakterlánc érvényességének ellenőrzéséhez alkalmazza a [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) metódust. Ha a **TryParse** sikeres, a minta inicializálja a *storageAccount* változót, és **igaz** értéket ad vissza.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>A tároló létrehozása és az engedélyek beállítása

A minta ezután létrehoz egy tárolót, és beállítja annak engedélyeit, hogy a tárolóban lévő minden blob nyilvános legyen. A nyilvános blobok névtelenül elérhetők bármely ügyfél által.

Tároló létrehozásához előbb hozza létre a [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) objektum egy példányát, amely a tárfiókban található Blob Storage-ra mutat. Ezután hozza létre a [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) objektum egy példányát, majd a tárolót. 

Esetünkben a minta a [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) metódus meghívásával hozza létre a tárolót. A rendszer egy GUID azonosítót fűz a tároló nevének végéhez, hogy biztosítsa annak egyediségét. Éles környezetben érdemes inkább a [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) metódust alkalmazni, mivel ez csak akkor hozza létre a tárolót, ha az még nem létezik, és így elkerülhetők a névütközések.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A minta ezután feltölt egy helyi fájlt egy blokkblobba. A példakód lekér egy **CloudBlockBlob** objektumra mutató hivatkozást a [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) metódus az előző szakaszban létrehozott tárolón történő meghívásával. Ezután az [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) metódus meghívásával feltölti a kiválasztott fájlt a blobba. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
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

A minta a [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metódus használatával kilistázza a tárolóban található blobokat. A minta esetében a tároló csak egy blobbal bővült, így a listázási művelet csak ezt az egy blobot adja vissza.

Ha túl sok (alapértelmezés szerint több mint 5000) blob van egy hívás válaszában, akkor a **ListBlobsSegmentedAsync** metódus visszaadja a teljes eredményhalmaz egy szeletét, valamint egy folytatási tokent. A blobhalmaz következő szeletének visszaadásához az előző hívás által visszaadott folytatási tokent kell megadnia. Egészen addig, amíg a folytatási token nullértékű nem lesz. A nullértékű folytatási token jelzi, hogy az összes blob le lett kérve. A mintakód bemutatja a folytatási token használatát, amely ajánlott eljárásként alkalmazandó.

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
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Blobok letöltése

Ezután a [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) metódus használatával töltse le az imént létrehozott blobokat a helyi fájlrendszerre. A mintakód a blob nevét kiegészíti a „_DOWNLOADED” (Letöltve) utótaggal, így mindkét fájl látható lesz a helyi fájlrendszeren.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A minta a létrehozott erőforrások eltávolításához a [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) metódus meghívásával törli a teljes tárolót. Ha szeretné, a helyi fájlokat is törölheti.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
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

## <a name="resources-for-developing-net-applications-with-blobs"></a>Blobokkal rendelkező .NET-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó .NET-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz készült [.NET ügyféloldali kódtár](https://www.nuget.org/packages/WindowsAzure.Storage/) legújabb verziójáért töltse le a NuGet-csomagot. 
- A [.NET ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-net) a GitHubon tekintheti meg.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A .NET ügyféloldali kódtárral kapcsolatos további információért lásd a [.NET API-referenciáját](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Tekintse át a .NET ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob).

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával. 

Ha szeretné megtudni, hogyan lehet olyan webalkalmazásokat létrehozni, amelyek rendszerképeket töltenek fel Blob Storage-tárolókba, lépjen tovább a [rendszerképadatok az Azure Storage segítségével a felhőbe történő feltöltését](storage-upload-process-images.md) ismertető szakaszra.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-dotnet-how-to-use-blobs.md)

- A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
- Ha szeretne megtekinteni egy olyan mintaalkalmazást, amely a Windowshoz készült Visual Studióból helyezhető üzembe, tekintse meg [az Azure Blob Storage-et használó .NET fényképgaléria-mintawebalkalmazást](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 