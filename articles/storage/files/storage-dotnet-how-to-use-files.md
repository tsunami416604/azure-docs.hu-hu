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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301437"
---
# <a name="develop-for-azure-files-with-net"></a>Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ez az oktatóanyag bemutatja annak alapjait, hogyan fejleszthet a .NET-keretrendszer használatával az [Azure Filest](storage-files-introduction.md) használó alkalmazásokat fájladatok tárolásához. Ez az oktatóanyag egy egyszerű konzolos alkalmazást hoz létre, amellyel alapszintű műveleteket hajthat végre a .NET és a Azure Files használatával:

* Egy fájl tartalmának beolvasása.
* Állítsa be a fájlmegosztás maximális méretét vagy *kvótáját* .
* Hozzon létre egy megosztott hozzáférési aláírást (SAS-kulcsot) egy olyan fájlhoz, amely a megosztáson definiált tárolt hozzáférési szabályzatot használja.
* Fájl másolása másik fájlba egy tárfiókon belül.
* Fájl másolása blobba egy tárfiókon belül.
* Az Azure Storage metrikáinak használata a hibaelhárításhoz.

További információ a Azure Filesről: [Mi az Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>A .NET API-k ismertetése

Az Azure Files két széleskörű megközelítést nyújt az ügyfélalkalmazásokhoz: az SMB protokollt és a REST-et. A .NET-en belül a `System.IO` és `WindowsAzure.Storage` API-k elvonták ezeket a megközelítést.

API | A következő esetekben használja | Megjegyzések
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Az alkalmazás/alkalmazásnak: <ul><li>Az SMB használatával kell olvasni/írni a fájlokat</li><li>Olyan eszközön fut, amely a 445-ös porton keresztül éri el az Azure Files-fiókot.</li><li>Nem kell kezelnie a fájlmegosztás rendszergazdai beállításait.</li></ul> | A Azure Files SMB protokollon keresztül megvalósított fájl I/O általában ugyanaz, mint a hálózati fájlmegosztás vagy a helyi tárolóeszköz I/O-értéke. A .NET-szolgáltatások számos funkciójának bevezetéséhez, beleértve a fájl I/O-t, tekintse meg a [konzol alkalmazás](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) oktatóanyagát.
[Microsoft. Azure. Storage. file](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Az alkalmazás/alkalmazásnak: <ul><li>Az 445-as porton keresztüli SMB használatával nem lehet hozzáférni Azure Files tűzfal-vagy INTERNETSZOLGÁLTATÓi megkötések miatt</li><li>Rendszergazdai funkciókat igényel, például a fájlmegosztás kvótájának beállítását vagy közös hozzáférésű jogosultságkód létrehozásának lehetőségét.</li></ul> | Ez a cikk azt mutatja be, hogyan használhatók a fájlok I/O-használata az SMB és a fájlmegosztás kezelése helyett a REST-`Microsoft.Azure.Storage.File`.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>A konzolalkalmazás létrehozása és az összeállítás elérése

Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések bemutatják, hogyan hozhat létre egy Console-alkalmazást a Visual Studio 2019-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Indítsa el a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza a C# **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább**gombra.
1. Az **új projekt konfigurálása**lapon adja meg az alkalmazás nevét, majd válassza a **Létrehozás**lehetőséget.

Az oktatóanyagban szereplő összes példát felveheti a konzol alkalmazás `Program.cs` fájljának `Main()` metódusára.

Az Azure Storage ügyféloldali kódtárat bármilyen típusú .NET-alkalmazásban használhatja. Ezek a típusok egy Azure Cloud Service-t vagy webalkalmazást, valamint asztali és mobil alkalmazásokat tartalmaznak. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

## <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel

Az oktatóanyag elvégzéséhez tekintse meg a projektben szereplő csomagokat:

* [A .NET-hez készült közös kódtár Microsoft Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Ez a csomag programozott hozzáférést biztosít a Storage-fiókjában található általános erőforrásokhoz.
* [A .NET-hez készült blob-függvénytár Microsoft Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Ez a csomag programozott hozzáférést biztosít a blob-erőforrásokhoz a Storage-fiókban.
* [Microsoft Azure Storage a .NET-hez készült file Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Ez a csomag programozott hozzáférést biztosít a Storage-fiókban található fájlok erőforrásaihoz.
* [A .NET-hez készült Configuration Manager-függvénytár Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Ez a csomag egy olyan osztályt biztosít, amely a konfigurációs fájlban lévő kapcsolatok karakterláncának elemzésére szolgál, bárhol is fut az alkalmazás.

A NuGettel mindkét csomagot beszerezheti. Kövesse az alábbi lépéseket:

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget.
1. A **NuGet csomagkezelő**területén válassza a **Tallózás**lehetőséget. Ezután keresse meg és válassza a **Microsoft. Azure. Storage. blob**elemet, majd válassza a **telepítés**lehetőséget.

   Ez a lépés telepíti a csomagot és annak függőségeit.
1. A csomagok keresése és telepítése:

   * **Microsoft. Azure. Storage. Common**
   * **Microsoft. Azure. Storage. file**
   * **Microsoft. Azure. ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Mentse a Storage-fiók hitelesítő adatait az app. config fájlba

Ezután mentse a hitelesítő adatait a projekt `App.config` fájljában. A **megoldáskezelő**kattintson duplán a `App.config` elemre, és szerkessze a fájlt úgy, hogy az a következő példához hasonló legyen. Cserélje le a `myaccount`t a Storage-fiók nevére, és `mykey` a Storage-fiók kulcsával.

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

A **Megoldáskezelőban**nyissa meg a `Program.cs` fájlt, és adja hozzá az alábbi utasításokat a fájl elejéhez.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>A fájlmegosztás szoftveres elérése

Ezután adja hozzá a következő tartalmat a `Main()` metódushoz a fent látható kód után a kapcsolódási karakterlánc lekéréséhez. Ez a kód lekéri a korábban létrehozott fájlra mutató hivatkozást, és megjeleníti a tartalmát.

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

Az Azure Storage ügyféloldali kódtár 5. x-es verziójától kezdve megadhatja egy fájlmegosztás kvótáját (a maximális méretet). Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

Egy megosztás kvótájának beállítása korlátozza a megosztáson tárolt fájlok teljes méretét. Ha a megosztásban lévő fájlok teljes mérete meghaladja a megosztáson beállított kvótát, az ügyfelek nem növelhetik a meglévő fájlok méretét. Az ügyfelek nem tudnak új fájlokat létrehozni, kivéve, ha ezek a fájlok üresek.

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

Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve létrehozhat egy közös hozzáférésű jogosultságkódot (shared access signature, SAS) egy fájlmegosztáshoz vagy fájlhoz. A megosztott hozzáférési aláírások kezeléséhez egy fájlmegosztás tárolt hozzáférési szabályzata is létrehozható. Azt javasoljuk, hogy hozzon létre egy tárolt hozzáférési szabályzatot, mert az lehetővé teszi az SAS visszavonását, ha az biztonsága sérül.

A következő példa egy tárolt hozzáférési szabályzatot hoz létre egy megosztáson. A példa ezt a házirendet használja a megosztásban található fájlokra vonatkozó SAS-korlátozások megadásához.

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

A megosztott hozzáférési aláírások létrehozásával és használatával kapcsolatos további információkért lásd: [how a Shared Access Signature Works](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Fájlok másolása

Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. A következő szakaszban bemutatjuk, hogyan hajthat végre programozott módon ezeket a másolási műveleteket.

A AzCopy használatával az egyik fájlt átmásolhatja egy másikba, vagy átmásolhatja a blobot egy fájlba vagy fordítva. Lásd: Ismerkedés [a AzCopy szolgáltatással](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Ha egy blobot másol egy fájlba vagy egy fájlt egy blobba, akkor is közös hozzáférésű jogosultságkóddal (SAS) kell engedélyeznie a forrásobjektumhoz való hozzáférést, ha a másolás tárfiókon belül történik.
>

### <a name="copy-a-file-to-another-file"></a>Fájl másolása másik fájlba

Az alábbi példa megosztáson belül másol át egy fájlt egy másikba. Mivel a másolási művelet ugyanazon a Storage-fiókban található fájlok között másolható, a másoláshoz megosztott kulcsos hitelesítést használhat.

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

Az Azure Storage ügyféloldali kódtár 8,5-es verziójától kezdve létrehozhat egy megosztási pillanatképet. Ezekből felsorolást is készíthet, tallózhat köztük, és törölheti is a megosztási pillanatképeket. A megosztási pillanatképek csak olvashatók, így nem lehet rajtuk írási műveleteket végrehajtani.

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

### <a name="browse-files-and-directories-within-share-snapshots"></a>A megosztási pillanatképekben található fájlok és könyvtárak tallózása

Az alábbi példa bemutatja a megosztási pillanatképekben található fájlok és könyvtárak tallózását.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Megosztások listázása és Pillanatképek megosztása és fájlmegosztás vagy-fájlok visszaállítása a megosztási pillanatképekről

A fájlmegosztásokról készített pillanatképek lehetővé teszik az egyes fájlok vagy a teljes fájlmegosztás visszaállítását a későbbiekben.

A fájlmegosztási pillanatképekből a fájlok a pillanatkép lekérdezésével állíthatók vissza. Ezután lekérhet egy adott megosztási pillanatképhez tartozó fájlt. Használja az adott verziót közvetlenül olvasásra, illetve összehasonlításra vagy visszaállításra.

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

### <a name="delete-share-snapshots"></a>Megosztási Pillanatképek törlése

Az alábbi példával törölhet egy fájlmegosztási pillanatképet.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## Azure Files hibáinak megoldása mérőszámok használatával<a name="troubleshooting-azure-files-using-metrics"></a>

Az Azure Storage Analytics mostantól az Azure Files esetén is támogatja a mérőszámok használatát. A metrikai adatok segítségével nyomon követheti a kéréseket, és diagnosztizálhatja a problémákat.

A [Azure Portal](https://portal.azure.com)Azure Files metrikáit is engedélyezheti. A metrikákat programozott módon is engedélyezheti, ha meghívja a Fájlszolgáltatások tulajdonságainak beállítása műveletet a REST API vagy a Storage ügyféloldali kódtár egyik analógiáját.

Az alábbi példakód bemutatja, hogyan használható a Storage .NET-hez készült ügyféloldali kódtára arra, hogy engedélyezze a mérőszámok használatát az Azure Fileshoz.

Először adja hozzá a következő `using` irányelveket a `Program.cs`-fájlhoz, a fent hozzáadott fájlok mellett:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Bár az Azure-Blobok, az Azure-táblák és az Azure-várólisták a megosztott `ServiceProperties` típust használják a `Microsoft.Azure.Storage.Shared.Protocol` névtérben, Azure Files a saját típusát használja, a `FileServiceProperties` típusát a `Microsoft.Azure.Storage.File.Protocol` névtérben. Az alábbi kód fordításához azonban mindkét névteret hivatkoznia kell a kódból.

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

Ha bármilyen problémába ütközik, tekintse meg a [Windows Azure Files kapcsolatos problémák elhárítása](storage-troubleshoot-windows-file-connection-problems.md)című témakört.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Filesről:

### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók

* [Azure Files: zökkenőmentes felhőalapú SMB-fájlrendszer Windows és Linux rendszerekhez](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Azure Files használata Linux rendszeren](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage-eszköztámogatás

* [Ismerkedés a AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Files-problémák hibaelhárítása Windowson](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referencia

* [Azure Storage API-k a .NET-hez](/dotnet/api/overview/azure/storage)
* [Fájlszolgáltatások REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Blogbejegyzések

* [Már általánosan elérhető az Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Az Azure File Storage ismertetése)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure Files szolgáltatás bemutatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
