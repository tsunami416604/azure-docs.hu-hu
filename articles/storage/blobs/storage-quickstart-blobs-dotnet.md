---
title: "Azure gyors üzembe helyezés – az Azure Blob storage .NET használatának átviteli objektumok |} Microsoft Docs"
description: "Gyorsan ismerje meg, és a .NET használatával Azure Blob-tároló objektumok átvitele"
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
ms.openlocfilehash: 1eac4165c35cb116a359c074bd629c918b58097c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Átviteli objektumok az Azure Blob storage .NET használatával

A gyors üzembe helyezés megismerheti, hogyan használható az Azure Storage a .NET ügyféloldali kódtár feltöltése, töltse le és blokk a tárolóban lévő blobok listázása.

## <a name="prerequisites"></a>Előfeltételek

A gyors üzembe helyezés befejeződik, telepítse a [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) következő munkaterheléssel:
    
    - **Azure-fejlesztés**

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A mintaalkalmazást a gyors üzembe helyezés használatban egy alapszintű konzolalkalmazást. 

Használjon [git](https://git-scm.com/) letölteni az alkalmazást a fejlesztési környezet egy példányát. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Ez a parancs klónokat a tárházat a helyi git-mappába. Nyissa meg a Visual Studio-megoldást, keresse meg a storage-BLOB-dotnet-gyors üzembe helyezés mappát, nyissa meg, majd kattintson duplán arra a storage-BLOB-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása

Az alkalmazás a kapcsolati karakterláncot kell megadnia a tárfiók. Nyissa meg a `app.config` fájlt a Visual Studio megoldáskezelőjében. Keresés a `StorageConnectionString` bejegyzés. A **érték**, cserélje le a teljes kapcsolati karakterlánc az Azure portálról mentett találhatóval. A `storageConnectionString` az alábbihoz kell hasonlítania:

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

## <a name="run-the-sample"></a>A minta futtatásához

Ez a minta létrehoz egy tesztelési a dokumentumok, feltölti a Blob Storage, a tárolóban lévő blobok sorolja fel, majd letölti a fájlt új néven, a régi és új fájlok összehasonlítás. 

A minta futtatásához nyomja le az F5 billentyűt. Kimeneti a konzolablakban a következőkhöz hasonló jeleníti meg: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

A folytatáshoz bármely billentyű lenyomásakor törli, a tároló és a fájlok. A folytatás előtt ellenőrizze a két fájl MyDocuments. Nyissa meg őket, és tekintse meg a megegyezik azzal. A konzolablakban kívül a BLOB URL-címét, majd illessze be a böngészőt, hogy a fájl tartalmát a Blob Storage tárolóban.

Például egy eszköz is használhatja a [Azure Tártallózó](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) megtekinthetik a fájlokat a Blob Storage tárolóban. Azure Tártallózó egy ingyenes platformfüggetlen eszköz, amely lehetővé teszi a tárfiók adatait. 

Miután ellenőrizte, hogy a fájlokat, kattintson a bemutató befejeződését, és törölje a tesztfájlok bármelyik billentyűt. Most, hogy ismeri a minta funkciója, nyissa meg a Program.cs fájlt nézze meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód ismertetése

Ezután azt végezze el a mintakódot, hogy megismerheti, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>A tárolási objektum mutató hivatkozások beolvasása

Az első lépés férhessen hozzá és felügyelhesse a Blob storage használt objektumok hivatkozásainak létrehozásához. Ezek az objektumok egymástól összeállítása – egyes használja a következő egy, a listában.

* Hozzon létre egy példányát a **CloudStorageAccount** objektum a tárfiókra mutat. 

* Hozzon létre egy példányát a **CloudBlobClient** objektum, amely a tárfiókban lévő a Blob szolgáltatás mutat. 

* Hozzon létre egy példányát a **CloudBlobContainer** objektum, a tároló elérésére. Tárolók használatával rendezheti a blobok, például a mappák használ a fájlok rendszerezéséhez a számítógépen.

Ha elvégezte a **CloudBlobContainer**, létrehozhat egy példányát a **CloudBlockBlob** objektumra mutat, az adott blob, amelyen szeretné, és hajtsa végre egy feltöltés, letöltés, másolása, stb. a műveletet.

> [!IMPORTANT]
> A tároló nevének kisbetűnek kell lennie. Lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) tároló és a blob nevének további információt.

Ebben a szakaszban az objektumok példányt létrehozni, hozzon létre egy új tárolót, és majd engedélyek beállítása a tárolón, a blobok nyilvánosak, és csak egy URL-cím elérhető. A tároló neve **quickstartblobs**. 

Ez a példa **CreateIfNotExists** mert szeretnénk hozzon létre egy új tároló minden alkalommal, amikor a minta futtatása. Éles környezetben, ahol az alkalmazás teljes tárolóhoz használ, hogy a rendszer jobb eljárás az, hogy csak akkor hívható **CreateIfNotExists** után. Másik lehetőségként időben tároló létrehozásához, így nem kell létrehozni a kódban.

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

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Blokkblobok a leggyakrabban használt, és amely a gyors üzembe helyezés használttól. 

Feltölteni a fájlt egy blobba, a blobra mutató hivatkozás beszerzése a cél-tárolóban. Miután a blobhivatkozást, az adatait feltöltheti azt a [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Ez a művelet a blob hoz létre, ha már nem létezik, vagy felülírja, ha már létezik.

A mintakód létrehoz egy helyi fájlt a feltöltés és letöltés, mint a feltölteni kívánt fájl tárolására használt **fileAndPath** és a blob neve **helyi_fájl**. Az alábbi példa feltölt a fájlt a tároló neve **quickstartblobs**.

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

Többféleképpen feltöltés, a Blob storage használata. Például ha egy memóriafolyam, használhatja a UploadFromFileAsync helyett a UploadFromStreamAsync metódust. 

Blokkblobok állhat bármilyen szövegből vagy bináris fájl. Lapblobokat elsősorban az infrastruktúra-szolgáltatási virtuális gépek biztonsági használt VHD-fájlokat. Hozzáfűző blobok használt naplózást, például a kívánt fájlra és majd hozzáadni további információkat. A Blob storage-ban tárolt legtöbb objektum blokkblobokat.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Kaphat a fájlok a tároló használja listáját [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). A következő kódot a bináris objektumok listájának beolvasása, majd végighalad őket, az URI-azonosítók található blobok megjelenítő. Másolja az URI a parancsablakot, és illessze be a böngészőt, hogy a fájl.

Ha 5000 vagy kevesebb BLOB a tárolóban, az összes a blob nevének egy hívása közben ListBlobsSegmentedAsync beolvasása. Ha több mint 5000 BLOB a tárolóban, a szolgáltatás lekérdezi az 5000-készletek listájában, amíg a blob nevének mindegyikét lettek beolvasva. Ezért először az API neve, az első 5000 blob nevének, valamint a folytatási kód adja vissza. A második alkalommal megadta a jogkivonatot, és a szolgáltatás lekérdezi a blob nevének a következő készletét, és stb a folytatási kód csak NULL értékű, ami azt jelzi, hogy a blob nevének mindegyikét lettek beolvasva. 

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

Blobok letöltése a helyi lemezek használata [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

A következő kódot a blob feltöltése egy korábbi szakaszában "_DOWNLOADED" utótag hozzáadása a blob nevének, így mindkét fájlt a helyi lemezen tölti le. 

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

Ha már nincs szüksége a blobok a gyors üzembe helyezés feltöltött, törölje a teljes tárolóhoz történő [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Ha már nincs szükség a fájlt is törli.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés megtanulta, hogyan viheti át a fájlok helyi lemezre és az Azure Blob storage .NET használatának közötti. További információt a Blob storage használata, továbbra is a Blob-tároló útmutató.

> [!div class="nextstepaction"]
> [A BLOB Storage műveletek útmutató](storage-dotnet-how-to-use-blobs.md)

A további Azure Storage mintakódok letöltése és futtatása, lásd a [mintát Azure Storage .NET](../common/storage-samples-dotnet.md).

A Tártallózó alkalmazással és a Blobok kapcsolatos további információkért lásd: [kezelése Azure Blob storage-erőforrások a Tártallózó alkalmazással](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
