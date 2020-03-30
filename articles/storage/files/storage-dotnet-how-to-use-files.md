---
title: Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel | Microsoft Docs
description: Itt megismerkedhet azzal, hogyan fejleszthet az Azure Files szolgáltatást használó .NET-alkalmazásokat és -szolgáltatásokat a fájladatok tárolásához.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301437"
---
# <a name="develop-for-azure-files-with-net"></a>Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ez az oktatóanyag bemutatja annak alapjait, hogyan fejleszthet a .NET-keretrendszer használatával az [Azure Filest](storage-files-introduction.md) használó alkalmazásokat fájladatok tárolásához. Ez az oktatóanyag egy egyszerű konzolalkalmazást hoz létre a .NET és az Azure Files alapvető műveletekhez:

* Egy fájl tartalmának beszerezése.
* Állítsa be a fájlmegosztás maximális méretét vagy *kvótáját.*
* Hozzon létre egy megosztott hozzáférésű aláírást (SAS-kulcsot) egy olyan fájlhoz, amely a megosztáson definiált tárolt hozzáférési házirendet használ.
* Fájl másolása másik fájlba egy tárfiókon belül.
* Fájl másolása blobba egy tárfiókon belül.
* Az Azure Storage-metrikák segítségével hibaelhárítás.

Ha többet szeretne megtudni az Azure Files-ról, olvassa el [a Mi az Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>A .NET API-k ismertetése

Az Azure Files két széleskörű megközelítést nyújt az ügyfélalkalmazásokhoz: az SMB protokollt és a REST-et. A .NET-en `WindowsAzure.Storage` belül a és az `System.IO` API-k absztrakt ezeket a megközelítéseket.

API | A következő esetekben használja | Megjegyzések
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Az alkalmazás/alkalmazásnak: <ul><li>Fájlok olvasására/írására van szüksége az SMB használatával</li><li>Olyan eszközön fut, amely a 445-ös porton keresztül éri el az Azure Files-fiókot.</li><li>Nem kell kezelnie a fájlmegosztás rendszergazdai beállításait.</li></ul> | Az Azure Files sMB-n keresztüli fájli/o-fájl-i/o-ja általában megegyezik a hálózati fájlmegosztással vagy helyi tárolóeszközzel rendelkező I/O-val. A .NET számos szolgáltatásának, köztük a fájli/o-nak a bemutatása a [Konzolalkalmazás oktatóanyagában](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) található.
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Az alkalmazás/alkalmazásnak: <ul><li>Az Azure Files nem érhető el az SMB használatával a 445-ös porton tűzfal- vagy internetszolgáltatói korlátozások miatt</li><li>Rendszergazdai funkciókat igényel, például a fájlmegosztás kvótájának beállítását vagy közös hozzáférésű jogosultságkód létrehozásának lehetőségét.</li></ul> | Ez a cikk bemutatja `Microsoft.Azure.Storage.File` a fájl I/O használata a REST használata helyett SMB és a fájlmegosztás kezelése.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>A konzolalkalmazás létrehozása és az összeállítás elérése

Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. Az alábbi lépések bemutatják, hogyan hozhat létre konzolalkalmazást a Visual Studio 2019-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Indítsa el a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Framework)** lehetőséget a C#-hoz, majd kattintson a **Tovább**gombra.
1. Az **Új projekt konfigurálása**csoportban adja meg az alkalmazás nevét, és válassza a **Létrehozás gombot.**

Az oktatóanyag összes kódpéldáját hozzáadhatja a `Main()` konzolalkalmazás `Program.cs` fájljának metódusához.

Az Azure Storage-ügyfélkódtár bármilyen típusú .NET alkalmazás. Ezek a típusok közé tartozik az Azure felhőszolgáltatás vagy webalkalmazás, valamint az asztali és mobilalkalmazások. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

## <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel

Az oktatóanyag befejezéséhez tekintse meg a projekt ben található csomagokat:

* [A Microsoft Azure Storage közös könyvtára .](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Ez a csomag programozott hozzáférést biztosít a tárfiók ban található közös erőforrásokhoz.
* [Microsoft Azure Storage Blob-könyvtár a .](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Ez a csomag programozott hozzáférést biztosít a tárfiókblob-erőforrásokhoz.
* [Microsoft Azure Storage File library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Ez a csomag programozott hozzáférést biztosít a tárfiókban lévő fájlerőforrásokhoz.
* [Microsoft Azure Configuration Manager-könyvtár a .](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Ez a csomag egy osztályt biztosít egy kapcsolati karakterlánc elemzéséhez egy konfigurációs fájlban, bárhol is fut az alkalmazás.

A NuGettel mindkét csomagot beszerezheti. Kövesse az alábbi lépéseket:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza **a NuGet-csomagok kezelése parancsot.**
1. A **NuGet csomagkezelőben**válassza a **Tallózás**lehetőséget. Ezután keresse meg a **Microsoft.Azure.Storage.Blob**elemet, és válassza a **Telepítés**lehetőséget.

   Ez a lépés telepíti a csomagot és annak függőségeit.
1. Keresse meg és telepítse a csomagokat:

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Tárfiók hitelesítő adatainak mentése az App.config fájlba

Ezután mentse a hitelesítő adatokat `App.config` a projekt fájljába. A **Megoldáskezelőben**kattintson duplán a fájlra, `App.config` és úgy szerkeszti a fájlt, hogy az hasonló legyen a következő példához. Cserélje `myaccount` le a tárfiók nevét és `mykey` a tárfiók kulcsát.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Az Azure Storage Emulator legújabb verziója nem támogatja az Azure Filest. A kapcsolati sztringnek egy Azure Storage-fiókot kell megcéloznia a felhőben, hogy használni lehessen az Azure Files szolgáltatással.

## <a name="add-using-directives"></a>Hozzáadás irányelvekkel

A **Megoldáskezelőben** `Program.cs` nyissa meg a fájlt, és adja hozzá a következő utasításokat a fájl tetejéhez.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>A fájlmegosztás szoftveres elérése

Ezután adja hozzá a `Main()` következő tartalmat a metódushoz a fenti kód után a kapcsolati karakterlánc lekéréséhez. Ez a kód hivatkozást kap a korábban létrehozott fájlra, és kiadja annak tartalmát.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

A kimenet megtekintéséhez futtassa a konzolalkalmazást.

## <a name="set-the-maximum-size-for-a-file-share"></a>Egy fájlmegosztás maximális méretének beállítása

Az Azure Storage-ügyfélkönyvtár 5.x-es verziójától kezdve beállíthatja a fájlmegosztás kvótáját (maximális mérete). Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

A megosztás kvótájának beállítása korlátozza a megosztáson tárolt fájlok teljes méretét. Ha a megosztáson lévő fájlok teljes mérete meghaladja a megosztáson beállított kvótát, az ügyfelek nem növelhetik a meglévő fájlok méretét. Az ügyfelek csak akkor hozhatnak létre új fájlokat, ha azok üresek.

Az alábbi példa bemutatja, hogyan ellenőrizheti egy megosztás aktuális kihasználását, és hogyan adhat meg hozzá kvótát.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Közös hozzáférésű jogosultságkód létrehozása egy fájlhoz vagy fájlmegosztáshoz

Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve létrehozhat egy közös hozzáférésű jogosultságkódot (shared access signature, SAS) egy fájlmegosztáshoz vagy fájlhoz. A megosztott hozzáférésű aláírások kezeléséhez fájlmegosztáson tárolt hozzáférési szabályzatot is létrehozhat. Azt javasoljuk, hogy hozzon létre egy tárolt hozzáférési szabályzatot, mert lehetővé teszi a SAS visszavonását, ha veszélybe kerül.

A következő példa egy megosztott hozzáférési szabályzatot hoz létre egy megosztáson. A példa ezt a házirendet használja a megosztásban lévő fájl SAS-ének megkötéseinek megadására.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

A megosztott hozzáférésű aláírások létrehozásáról és használatáról a [Közös hozzáférésű aláírás működése című](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)témakörben talál további információt.

## <a name="copy-files"></a>Fájlok másolása

Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. A következő szakaszokban bemutatjuk, hogyan kell ezeket a másolási műveleteket programozott módon elvégezni.

Az AzCopy segítségével is másolhat egy fájlt egy másikba, vagy blobot másolhat egy fájlba, vagy fordítva. Lásd: [Az AzCopy – Első lépések](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Ha egy blobot másol egy fájlba vagy egy fájlt egy blobba, akkor is közös hozzáférésű jogosultságkóddal (SAS) kell engedélyeznie a forrásobjektumhoz való hozzáférést, ha a másolás tárfiókon belül történik.
>

### <a name="copy-a-file-to-another-file"></a>Fájl másolása másik fájlba

Az alábbi példa megosztáson belül másol át egy fájlt egy másikba. Mivel ez a másolási művelet az ugyanazon tárfiókban lévő fájlok között másol, a megosztott kulcsú hitelesítés sel elvégezheti a másolást.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Fájl másolása blobba

Az alábbi példa létrehoz egy fájlt, és átmásolja egy ugyanabban a tárfiókban található blobba. A példa létrehoz a forrásfájlhoz egy SAS-t, amellyel a szolgáltatás engedélyezi a forrásfájlhoz való hozzáférést a másolási művelet alatt.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Ugyanígy másolhat blobokat fájlokba. Ha a forrásobjektum egy blob, hozzon létre egy SAS-t, amely engedélyezi a blobhoz való hozzáférést a másolási művelet során.

## <a name="share-snapshots"></a>Pillanatképek megosztása

Az Azure Storage ügyfélkönyvtár 8.5-ös verziójától kezdve létrehozhat egy megosztási pillanatképet. Ezekből felsorolást is készíthet, tallózhat köztük, és törölheti is a megosztási pillanatképeket. A megosztási pillanatképek csak olvashatók, így nem lehet rajtuk írási műveleteket végrehajtani.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása

Az alábbi példával létrehozhat egy fájlmegosztási pillanatképet.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása

Az alábbi példa megjeleníti egy adott megosztás pillanatképeit.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Megosztási pillanatképekben található fájlok és könyvtárak tallózása

Az alábbi példa bemutatja a megosztási pillanatképekben található fájlok és könyvtárak tallózását.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Megosztások és megosztási pillanatképek megjelenítése, valamint fájlmegosztások vagy fájlok visszaállítása megosztási pillanatképekből

A fájlmegosztásokról készített pillanatképek lehetővé teszik az egyes fájlok vagy a teljes fájlmegosztás visszaállítását a későbbiekben.

A fájlmegosztási pillanatképekből a fájlok a pillanatkép lekérdezésével állíthatók vissza. Ezután beolvashat egy fájlt, amely egy adott megosztási pillanatképhez tartozik. Ezzel a verzióval közvetlenül olvashatja és összehasonlíthatja, illetve visszaállíthatja azt.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Megosztási pillanatképek törlése

Az alábbi példával törölhet egy fájlmegosztási pillanatképet.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Azure-fájlok – metrikák – problémamegoldás<a name="troubleshooting-azure-files-using-metrics"></a>

Az Azure Storage Analytics mostantól az Azure Files esetén is támogatja a mérőszámok használatát. A metrikai adatok segítségével nyomon követheti a kéréseket, és diagnosztizálhatja a problémákat.

Az Azure-fájlok metrikákat az [Azure Portalon](https://portal.azure.com)engedélyezheti. A metrikákat programozott módon is engedélyezheti, ha meghívja a Fájlszolgáltatás tulajdonságainak beállítása műveletet a REST API-val vagy annak egyik analógjával a Storage Client Library-ben.

Az alábbi példakód bemutatja, hogyan használható a Storage .NET-hez készült ügyféloldali kódtára arra, hogy engedélyezze a mérőszámok használatát az Azure Fileshoz.

Először adja `using` hozzá a `Program.cs` fájlhoz a következő irányelveket, valamint a fent hozzáadott irányelveket:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Bár az Azure Blobs, az Azure Tables `ServiceProperties` és `Microsoft.Azure.Storage.Shared.Protocol` az Azure-várólisták a megosztott `FileServiceProperties` típust `Microsoft.Azure.Storage.File.Protocol` használják a névtérben, az Azure Files a saját típusát, a névtér típusát használja. A következő kód fordításához azonban mindkét névtérre hivatkoznikell a kódból.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Ha bármilyen problémát tapasztal, olvassa el az [Azure Files hibaelhárítási problémáit a Windows rendszerben.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>További lépések

Az Azure Files szolgáltatásról az alábbi forrásokban talál további információt:

### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók

* [Azure Files: zökkenőmentes felhőalapú SMB-fájlrendszer Windows és Linux rendszerekhez](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Az Azure Files használata Linux rendszerrel](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage-eszköztámogatás

* [Bevezetés az AzCopy használatába](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Files-problémák hibaelhárítása Windowson](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referencia

* [Azure Storage API-k a .NET-hez](/dotnet/api/overview/azure/storage)
* [A File szolgáltatás REST API-ja](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Blogbejegyzések

* [Azure File Storage, most már általánosan elérhető](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Az Azure File Storage ismertetése)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [A Microsoft Azure Fájlszolgáltatás bemutatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
