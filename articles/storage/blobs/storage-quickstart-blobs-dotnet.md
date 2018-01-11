---
title: "Azure rövid – Objektumok továbbítása Azure Blob-tárolókra és -tárolókról a .NET-tel | Microsoft Docs"
description: "Gyors áttekintést kaphat arról, hogyan továbbíthat objektumokat az Azure Blob-tárolókra és -tárolókról a .NET használatával"
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2017
ms.author: tamram
ms.openlocfilehash: 5020f070a8eb9215f175fc3ff3a905cff28ce37f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Objektumok továbbítása Azure Blob-tárolókra és -tárolókról a .NET-tel

A rövid útmutató azt ismerteti, hogyan használható az Azure Storage-hez készült .NET ügyfélkódtár a blokkblobok feltöltésére, letöltésére és listázására egy tárolóban.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* A .NET Core 2.0 telepítése [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) vagy [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x) rendszeren

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt mintaalkalmazás egy egyszerű konzolalkalmazás. 

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a storage-blobs-node-quickstart mappát, nyissa meg, és kattintson duplán a storage-blobs-dotnet-quickstart.sln fájlra. 

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Javasoljuk, hogy ezt a kapcsolati karakterláncot egy környezeti változóban tárolja az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.  Cserélje le a \<yourconnectionstring\> kifejezést a tényleges kapcsolati karakterláncra.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta létrehoz egy tesztfájlt a Dokumentumok mappában, feltölti a Blob-tárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt új néven, hogy össze lehessen hasonlítani a régebbi fájllal. 

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

A megjelenő kimenet a következő példához hasonló:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Amikor lenyom egy billentyűt a folytatáshoz, a rendszer törli a Storage-tárolót és a fájlokat. A folytatás előtt ellenőrizze a két fájlt a Dokumentumok mappában. Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a fájl tartalmának Blob-tárolóban való megtekintéséhez.

Az [Azure Storage Explorert](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat a fájl megtekintéséhez a Blob-tárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg a Program.cs fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első teendő a referenciák létrehozása a Blob-tárolóhoz való hozzáféréshez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek, mindegyiket a listában utánuk következő használja.

* Hozza létre a [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) objektum egy példányát, amely a tárfiókra mutat.

* Hozza létre a [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet) objektum példányát, amely a tárfiókja Blob szolgáltatására mutat.

* Hozza létre a [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) objektum egy példányát. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

A [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) létrehozása után létrehozhatja a [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) objektum egy példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

Ebben a szakaszban létre fogja hozni az objektumok egy-egy példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak és mindössze egy URL-cím alapján elérhetőek legyenek. A tároló neve **quickstartblobs**.

Ez a példa a [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet) metódust használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Éles környezetben, ahol mindig ugyanazt a tárolót használja egy alkalmazásban, célravezetőbb megoldás, ha csak egyszer hívja meg a **CreateIfNotExists** metódust. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a rövid útmutató is ezeket használja.

Egy fájl adott blobba való feltöltéséhez szerezze be a blob céltárolón belüli hivatkozását. Ha megszerezte a blobhivatkozást, adatokat a [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) használatával tölthet fel rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja el, hogy az a **fileAndPath** névvel és a **localFileName** blobnévvel legyen feltöltve. A következő példa feltölti a fájlt a **quickstartblobs** nevű tárolóba.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

A Blob-tárolóval többféle feltöltési módszer használható. Ha például memóriastreamje van, használhatja az UploadFromStreamAsync metódust az UploadFromFileAsync helyett.

A blokkblobok akármilyen típusú szöveges vagy bináris fájlok lehetnek. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használatosak. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban található fájlok listáját a [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) paranccsal szerezheti be. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a talált blobok URI-ját. Az URI-t kimásolhatja a parancsablakból, és a fájl megtekintéséhez beillesztheti egy böngészőbe.

Ha 5000 vagy kevesebb blob található a tárolóban, az összes blobnevet lekérheti a ListBlobsSegmentedAsync egyetlen hívásával. Ha több mint 5000 blob található a tárolóban, a szolgáltatás 5000-es csomagokban kéri le a blobnevek listáját, amíg a végükre nem ér. Ennek megfelelően az API az első hívásakor az első 5000 blobnevet és egy folytatási kódot ad vissza. A második alkalommal meg kell adnia ezt a kódot, és a szolgáltatás lekéri a blobnevek következő készletét, és így tovább, amíg a folytatási kód értéke nulla nem lesz – ez jelzi, hogy a rendszer az összes blobnevet lekérte.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>Blobok letöltése

Blobokat a helyi lemezre a [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) metódussal tölthet le.

A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot, és hozzáadja a „_DOWNLOADED” (Letöltve) utótagot a nevéhez, így mindkét fájlt láthatja majd a helyi lemezen.

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) paranccsal. A létrehozott fájlokat is törölje, ha már nincs szüksége rájuk.

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

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a .NET használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-dotnet-how-to-use-blobs.md)

További letölthető és futtatható Azure Storage-kódmintákért tekintse meg a [.NET-et használó Azure Storage-minták](../common/storage-samples-dotnet.md) listáját.

További információk a Storage Explorerről és a blobokról: [Azure Blob Storage-erőforrások kezelése a Storage Explorer használatával](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
