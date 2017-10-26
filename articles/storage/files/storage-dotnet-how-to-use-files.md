---
title: "Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel | Microsoft Docs"
description: "Itt megismerkedhet azzal, hogyan fejleszthet az Azure Files szolgáltatást használó .NET-alkalmazásokat és -szolgáltatásokat a fájladatok tárolásához."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 51180530790fc0077cea4d8aea7088f1f871681b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="develop-for-azure-files-with-net"></a>Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel 
> [!NOTE]
> Ez a cikk bemutatja, hogyan kezelheti az Azure Files szolgáltatást .NET-kóddal. Az Azure Files szolgáltatással kapcsolatos további információkért lásd: [Bevezetés az Azure Files használatába](storage-files-introduction.md).
>

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

Ez az oktatóanyag bemutatja annak alapjait, hogyan fejleszthet a .NET használatával az Azure Files szolgáltatást használó alkalmazásokat vagy szolgáltatásokat a fájladatok tárolásához. Ebben az oktatóanyagban egy egyszerű konzolalkalmazást hozunk létre, és bemutatjuk, hogyan hajthat végre alapszintű műveleteket a .NET-keretrendszer és az Azure Files használatával:

* Egy fájl tartalmának lekérése
* Egy fájlmegosztás kvótájának (maximális méretének) beállítása.
* Közös hozzáférésű jogosultságkód (SAS-kulcs) létrehozása egy, a megosztásban meghatározott megosztott elérési házirendet használó fájlhoz.
* Fájl másolása másik fájlba egy tárfiókon belül.
* Fájl másolása blobba egy tárfiókon belül.
* Hibaelhárítás az Azure Storage Metrics segítségével

> [!Note]  
> Mivel az Azure Files SMB-n keresztül is elérhető, lehetőség van olyan egyszerű alkalmazások írására, amelyek a fájl I/O System.IO osztályait használják az Azure-fájlmegosztás eléréséhez. Ez a cikk azt ismerteti, hogyan írhat az Azure Files szolgáltatással a [Fájl REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) révén kommunikáló Azure Storage .NET SDK-t használó alkalmazásokat. 


## <a name="create-the-console-application-and-obtain-the-assembly"></a>A konzolalkalmazás létrehozása és az összeállítás elérése
Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések azt mutatják be, hogyan hozhat létre konzolalkalmazást a Visual Studio 2017-ben, de a lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. Válassza az **Installed** (Telepítve) > **Templates** (Sablonok) > **Visual C#** > **Windows Classic Desktop** (Windows klasszikus asztal) lehetőséget
3. Válassza a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer) lehetőséget
4. Írja be az alkalmazás nevét a **Name:** (Név:) mezőbe
5. Kattintson az **OK** gombra.

Az oktatóanyagban szereplő példák hozzáadhatók a konzolalkalmazás `Program.cs` fájljában található `Main()` metódushoz.

Az Azure Storage ügyféloldali kódtárat bármilyen típusú .NET-alkalmazásban használhatja, ideértve az Azure-felhőszolgáltatásokat vagy -webappokat és az asztali és mobilalkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

## <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel
Két csomagra kell hivatkoznia a projektben az oktatóanyag teljesítéséhez:

* [A Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/): Ez a csomag programozott hozzáférést biztosít a tárfiókja adatforrásaihoz.
* [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban található kapcsolati karakterlánc elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGettel mindkét csomagot beszerezheti. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Keressen rá az interneten a „WindowsAzure.Storage” kifejezésre, és kattintson az **Install** (Telepítés) gombra a Storage ügyféloldali kódtár és függőségeinek telepítéséhez.
3. Keresse rá az interneten a „WindowsAzure.ConfigurationManager” kifejezésre, és kattintson az **Install** (Telepítés) gombra az Azure Configuration Manager telepítéséhez.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>A tárfiók hitelesítő adatainak mentése az app.config fájlba
Mentse el a hitelesítő adatokat a projekt app.config fájljába. Szerkessze az app.config fájlt úgy, hogy hasonló legyen az alábbi példában bemutatotthoz. Ehhez cserélje ki a `myaccount` elemet a tárfiók nevére, a `mykey` elemet pedig a tárfiók kulcsára.

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
> Az Azure Storage Emulator legújabb verziója nem támogatja az Azure Filest. A kapcsolati karakterláncnak egy Azure Storage-fiókot kell megcéloznia a felhőben, hogy használni lehessen az Azure Files szolgáltatással.

## <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Nyissa meg a `Program.cs` fájlt a Solution Explorer (Megoldáskezelő) felületén, és irányelvekkel adja hozzá az alábbiakat a fájl elejéhez.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>A fájlmegosztás szoftveres elérése
Adja hozzá az alábbi kódot a `Main()` módszerhez (a fent látható kód után) a kapcsolati karakterlánc lekéréséhez. Ez a kód lekér egy hivatkozást a korábban létrehozott fájlra, és eredményül megjeleníti annak tartalmát a konzolablakban.

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
Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve gigabájtokban kifejezve megadhat egy kvótát (vagy maximális méretet) egy fájlmegosztáshoz. Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

Ha beállít egy kvótát egy megosztáshoz, korlátozhatja a megosztáson tárolt fájlok összesített méretét. Ha a megosztásban található fájlok teljes mérete meghaladja a megosztáshoz beállított kvótát, az ügyfelek nem növelhetik tovább a meglévő fájlok méretét, és csak olyan új fájlokat hozhatnak létre, amelyek üresek.

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
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
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
Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve létrehozhat egy közös hozzáférésű jogosultságkódot (shared access signature, SAS) egy fájlmegosztáshoz vagy fájlhoz. Létrehozhat egy megosztott elérési házirendet is egy fájlmegosztáshoz, hogy kezelni tudja a közös hozzáférésű jogosultságkódokat. Azért érdemes létrehozni megosztott elérési házirendet, mert annak az eszközeivel vissza lehet hívni az SAS-t, amennyiben sérülne a biztonsága.

Az alábbi példa létrehoz egy megosztott elérési házirendet egy megosztáson, majd felhasználja a házirendet egy, a megosztásban található fájlhoz tartozó SAS korlátozására.

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

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
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
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

További információk a közös hozzáférésű jogosultságkód létrehozásáról és használatáról: [Közös hozzáférésű jogosultságkódok (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) és [SAS létrehozása az Azure-blobok segítségével](../blobs/storage-dotnet-shared-access-signature-part-2.md).

## <a name="copy-files"></a>Fájlok másolása
Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. A következő szakaszokban bemutatjuk, hogyan hajthatók végre szoftveresen ezek a másolási műveletek.

Az AzCopy segítségével is átmásolhat egy fájlt egy másikba, egy blobot egy fájlba vagy fordítva. Lásd: [Transfer data with the AzCopy Command-Line Utility](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Adatátvitel az AzCopy parancssori segédprogrammal).

> [!NOTE]
> Ha egy blobot másol egy fájlba vagy egy fájlt egy blobba, akkor is a közös hozzáférésű jogosultságkóddal (SAS) kell hitelesítenie a forrásobjektumot, ha egy tárfiókon belül történik a másolás.
> 
> 

**Fájl másolása másik fájlba** Az alábbi példa megosztáson belül másol át egy fájlt egy másikba. Mivel ez a másolási művelet tárfiókon belül másolja a fájlokat, a másoláshoz a megosztott kulcsos hitelesítést is használhatja.

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

**Fájl másolása blobba** Az alábbi példa létrehoz egy fájlt, és átmásolja egy ugyanabban a tárfiókban található blobba. A példa létrehoz egy SAS-t a forrásfájlhoz, amelynek a segítségével a szolgáltatás hitelesíti a forrásfájl hozzáférését a másolási művelet alatt.

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
// to authenticate access to the source object, even if you are copying within the same
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

Ugyanígy másolhat blobokat fájlokba. Ha a forrásobjektum egy blob, hozzon létre egy SAS-t, amely hitelesíti a blobhoz való hozzáférést a másolási művelet során.

## <a name="share-snapshots-preview"></a>Megosztási pillanatképek (előzetes verzió)
Az Azure Storage ügyféloldali kódtár 8.5-ös verziójától kezdve létrehozhat megosztási pillanatképeket (előzetes verzió). Ezekből felsorolást is készíthet, tallózhat köztük, és törölheti is a megosztási pillanatképeket. A megosztási pillanatképek csak olvashatók, így nem lehet rajtuk írási műveleteket végrehajtani.

**Megosztási pillanatképek létrehozása**

Az alábbi példával létrehozhat egy fájlmegosztási pillanatképet.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Megosztási pillanatképek felsorolása**

Az alábbi példa megjeleníti egy adott megosztás pillanatképeit.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Megosztási pillanatképekben található fájlok és könyvtárak tallózása**

Az alábbi példa bemutatja a megosztási pillanatképekben található fájlok és könyvtárak tallózását.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Megosztások és megosztási pillanatképek megjelenítése, valamint fájlmegosztások vagy fájlok visszaállítása megosztási pillanatképekből** 

A fájlmegosztásokról készített pillanatképek lehetővé teszik az egyes fájlok vagy a teljes fájlmegosztás visszaállítását a későbbiekben. 

A fájlmegosztási pillanatképekből a fájlok a pillanatkép lekérdezésével állíthatók vissza. Ezután lekérhet egy bizonyos megosztási pillanatképhez tartozó fájlt, és közvetlenül beolvashatja és összehasonlíthatja azt a verziót, vagy akár helyre is állíthatja.

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


**Megosztási pillanatképek törlése**

Az alábbi példával törölhet egy fájlmegosztási pillanatképet.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Azure Files-problémák hibaelhárítása mérőszámok segítségével
Az Azure Storage Analytics mostantól az Azure Files esetén is támogatja a mérőszámok használatát. A metrikai adatok segítségével nyomon követheti a kéréseket, és diagnosztizálhatja a problémákat.

Az Azure Files mérőszámait az [Azure Portalon](https://portal.azure.com) engedélyezheti. A mérőszámokat szoftveresen is lehet engedélyezni. Ehhez hívja meg a Set File Service Properties (fájl szolgáltatástulajdonságainak beállítása) műveletet a REST API vagy valamelyik, a Storage ügyféloldali kódtárában megtalálható megfelelője segítségével.

Az alábbi példakód bemutatja, hogyan használható a Storage .NET-hez készült ügyféloldali kódtára arra, hogy engedélyezze a mérőszámok használatát az Azure Fileshoz.

Először adja hozzá a következő `using` irányelveket a `Program.cs` fájlhoz a fentiekben hozzáadottak kiegészítéseként:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Ne feledje, hogy míg az Azure-blobok, az Azure Table és az Azure-üzenetsorok a megosztott `ServiceProperties` típust használják a `Microsoft.WindowsAzure.Storage.Shared.Protocol` névtérben, az Azure Files a saját típusát, a `FileServiceProperties` típust használja a `Microsoft.WindowsAzure.Storage.File.Protocol` névtérben. Mindkét névtérre hivatkozni kell a kódban, hogy az alábbi kód fordítható legyen.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
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

Emellett olvassa el az [Azure-fájlok hibaelhárításáról szóló cikk](storage-troubleshoot-windows-file-connection-problems.md) átfogó hibaelhárítási útmutatóját.

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók
* [Azure Files: zökkenőmentes felhőalapú SMB-fájlrendszer Windows és Linux rendszerekhez](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Az Azure Files használata Linuxszal](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage-eszköztámogatás
* [How to use AzCopy with Microsoft Azure Storage (Az AzCopy használata a Microsoft Azure Storage szolgáltatással)](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Using the Azure CLI with Azure Storage (Az Azure parancssori felülete és az Azure Storage együttes használata)](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Azure Files-problémák hibaelhárítása](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referencia
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia a fájlszolgáltatás REST API-jához](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Blogbejegyzések
* [Mostantól általánosan elérhető az Azure Files](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Az Azure Files ismertetése](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)