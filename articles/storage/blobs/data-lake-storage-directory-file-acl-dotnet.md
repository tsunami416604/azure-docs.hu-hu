---
title: Azure Data Lake Storage Gen2 .NET SDK a fájlokhoz & ACL-ek
description: Az Azure Storage ügyféloldali kódtár segítségével kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1bf21b8714554dcdc52ab6e34041c738ec2653f6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105064"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A .NET használatával kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet a .NET használatával könyvtárakat, fájlokat és engedélyeket olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

[Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API-referenciák](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként telepítse az [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet csomagot.

A NuGet-csomagok telepítésével kapcsolatos további információkért lásd: [csomagok telepítése és kezelése a Visual Studióban a NuGet csomagkezelő használatával](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Ezután adja hozzá ezeket az utasításokat a programkód elejéhez.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -példányt hoz létre a fiók kulcsa alapján.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

A [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> További példákért tekintse meg a [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) dokumentációját.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. A [DataLakeServiceClient. CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) metódus meghívásával létrehozhat egyet.

Ez a példa egy nevű fájlrendszert hoz létre `my-file-system` . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a [DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) metódus meghívásával.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy fájlrendszerhez, majd hozzáadja a nevű alkönyvtárat `my-subdirectory` . 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat úgy, hogy meghívja a [DataLakeDirectoryClient. RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) metódust. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-subdirectory-renamed` .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Ez a példa egy nevű könyvtárat helyez át egy nevű `my-subdirectory-renamed` könyvtár alkönyvtárába `my-directory-2` . 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

A [DataLakeDirectoryClient. Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) metódus meghívásával törölhet egy könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Címtár-ACL kezelése

A címtár hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a [DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) metódust, és állítsa be az ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört. 

Ez a példa lekérdezi és beállítja a nevű könyvtár ACL-listáját `my-directory` . A karakterlánc `user::rwx,group::r-x,other::rw-` Megadja az olvasási, írási és végrehajtási engedélyeket a tulajdonos felhasználó számára, csak olvasási és végrehajtási engedélyeket ad a tulajdonos csoportnak, és minden más olvasási és írási engedélyt ad.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

Lekérheti és beállíthatja a fájlrendszer gyökérkönyvtárának ACL-listáját is. A gyökérkönyvtár beszerzéséhez adjon át egy üres karakterláncot ( `""` ) a [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) metódusba.

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlt a célhelyen a [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) osztály egy példányának létrehozásával. Töltsön fel egy fájlt a [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódus meghívásával. Ügyeljen arra, hogy a feltöltést a [DataLakeFileClient. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` .    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Ha a fájl mérete nagy, a kódnak több hívást is el kell végeznie a [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Használja helyette a [DataLakeFileClient. UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metódust. Így feltöltheti a teljes fájlt egyetlen hívással. 
>
> Tekintse meg a következő szakaszt egy példához.

## <a name="upload-a-large-file-to-a-directory"></a>Nagyméretű fájl feltöltése egy könyvtárba

A [DataLakeFileClient. UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metódus használatával nagyméretű fájlokat tölthet fel anélkül, hogy több hívást kellene tennie a [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódushoz.

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Fájl hozzáférés-vezérlési listájának kezelése

Egy fájl hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a [DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) metódust, és állítsa be az ACL-t úgy, hogy meghívja a [DataLakeFileClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) metódust.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört. 

Ez a példa lekérdezi és beállítja a nevű fájl hozzáférés-vezérlési listáját `my-file.txt` . A karakterlánc `user::rwx,group::r-x,other::rw-` Megadja az olvasási, írási és végrehajtási engedélyeket a tulajdonos felhasználó számára, csak olvasási és végrehajtási engedélyeket ad a tulajdonos csoportnak, és minden más olvasási és írási engedélyt ad.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból 

Először hozzon létre egy [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) -példányt, amely a letölteni kívánt fájlt jelöli. Használja a [DataLakeFileClient. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) metódust, és elemezze a visszatérési értéket egy [stream](https://docs.microsoft.com/dotnet/api/system.io.stream) -objektum beszerzéséhez. A .NET-fájlok feldolgozására szolgáló API-k használatával az adatfolyamból fájlba mentheti a bájtokat. 

Ez a példa egy [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) és egy [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) használ a bájtok fájlba mentéséhez. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázásához hívja meg a [FileSystemClient. GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) metódust, majd az eredmények alapján sorolja fel azokat.

Ez a példa kinyomtatja a nevű könyvtárban található egyes fájlok nevét `my-directory` .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Lásd még

* [API-referenciadokumentáció](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

