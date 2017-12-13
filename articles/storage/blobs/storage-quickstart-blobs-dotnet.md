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
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: ca4cb2dea9cdd2e46c3aef042e525acdfc09de8e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Objektumok továbbítása Azure Blob-tárolókra és -tárolókról a .NET-tel

A rövid útmutató azt ismerteti, hogyan használható az Azure Storage-hez készült .NET ügyfélkódtár a blokkblobok feltöltésére, letöltésére és listázására egy tárolóban.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse a [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) szoftvert a következő számítási feladatokkal:

- **Azure-fejlesztés**

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

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Nyissa meg az `app.config` fájlt a Visual Studio Megoldáskezelőjében. Keresse meg a `StorageConnectionString` bejegyzést. Az **érték** mezőben cserélje le a kapcsolati karakterlánc teljes értékét arra, amelyet az Azure Portalról elmentett. A `storageConnectionString` ekkor a következőképpen néz ki:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=<NameHere>;
    AccountKey=<KeyHere>" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta létrehoz egy tesztfájlt a Dokumentumok mappában, feltölti a Blob-tárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt új néven, hogy össze lehessen hasonlítani a régebbi fájllal. 

Futtassa a mintát az F5 billentyű lenyomásával. A kimenet egy konzolablakban jelenik meg, és a következőhöz hasonlóan fog kinézni: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

Amikor lenyom egy billentyűt a folytatáshoz, a rendszer törli a Storage-tárolót és a fájlokat. A folytatás előtt ellenőrizze a két fájlt a Dokumentumok mappában. Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a fájl tartalmának Blob-tárolóban való megtekintéséhez.

Az [Azure Storage Explorert](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat a fájl megtekintéséhez a Blob-tárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg a Program.cs fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első teendő a referenciák létrehozása a Blob-tárolóhoz való hozzáféréshez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a **CloudStorageAccount** objektum egy példányát, amely a tárfiókra mutat. 

* Hozza létre a **CloudBlobClient** objektum példányát, amely a tárfiókja Blob szolgáltatására mutat. 

* Hozza létre a **CloudBlobContainer** objektum egy példányát. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

A **CloudBlobContainer** létrehozása után létrehozhatja a **CloudBlockBlob** objektum egy példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

Ebben a szakaszban létre fogja hozni az objektumok egy-egy példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak és mindössze egy URL-cím alapján elérhetőek legyenek. A tároló neve **quickstartblobs**. 

Ez a példa a **CreateIfNotExists** metódust használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Éles környezetben, ahol mindig ugyanazt a tárolót használja egy alkalmazásban, célravezetőbb megoldás, ha csak egyszer hívja meg a **CreateIfNotExists** metódust. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
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
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
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
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
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
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) paranccsal. A létrehozott fájlokat is törölje, ha már nincs szüksége rájuk.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a .NET használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-dotnet-how-to-use-blobs.md)

További letölthető és futtatható Azure Storage-kódmintákért tekintse meg a [.NET-et használó Azure Storage-minták](../common/storage-samples-dotnet.md) listáját.

További információk a Storage Explorerről és a blobokról: [Azure Blob Storage-erőforrások kezelése a Storage Explorer használatával](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
