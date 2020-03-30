---
title: Azure Data Lake Storage Gen2 .NET SDK fájlokhoz & ACL-khez
description: Az Azure Storage-ügyfélkódtár segítségével kezelheti a könyvtárakat és a fájl- és címtárhozzáférés-vezérlési listákat (ACL) a hierarchikus névtérrel (HNS) engedélyezve lévő tárfiókokban.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061582"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A .NET használatával kezelheti a könyvtárakat, fájlokat és ACL-okat az Azure Data Lake Storage Gen2 szolgáltatásban

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet könyvtárakat, fájlokat és engedélyeket a hierarchikus névtérrel (HNS) rendelkező tárfiókokban. 

[Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API-hivatkozás](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 a Gen2 leképezés](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | visszajelzés[küldése](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésekhez telepítse az [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet csomagot.

A NuGet-csomagok telepítéséről a [Csomagok telepítése és kezelése a Visual Studio programban a NuGet Csomagkezelő használatával című témakörben](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)talál további információt.

Ezután adja hozzá ezeket a utasítások segítségével a kódfájl tetejéhez.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Csatlakozás a fiókhoz

A jelenések ebben a cikkben, létre kell hoznia egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) példányt, amely a tárfiókot jelöli. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozás fiókkulcs használatával

Ez a legegyszerűbb módja annak, hogy csatlakozzon egy fiókhoz. 

Ez a példa egy [DataLakeServiceClient-példányt](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) hoz létre egy fiókkulcs használatával.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Csatlakozás az Azure Active Directory (AD) használatával

Használhatja az [Azure identity ügyfélkódtár .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) az alkalmazás hitelesítéséhez az Azure AD.

Ez a példa egy [DataLakeServiceClient-példányt](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) hoz létre egy ügyfélazonosító, egy ügyféltitok és egy bérlői azonosító használatával.  Ezeknek az értékeknek a [beszerzése, olvassa el a jogkivonat beszerzése az Azure AD-től az ügyfélalkalmazásból érkező kérelmek engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört.

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
> További példák: az [Azure identity client library for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) dokumentáció..

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer a fájlok tárolójaként működik. Létrehozhat egyet a [DataLakeServiceClient.CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) metódus hívásával.

Ez a példa létrehoz `my-file-system`egy fájlrendszert. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtárhivatkozás létrehozása a [DataLakeFileSystemClient.CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) metódus meghívásával.

Ez a példa `my-directory` hozzáad egy elnevezett könyvtárat egy fájlrendszerhez, majd hozzáad egy alkönyvtárat. `my-subdirectory` 

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

## <a name="rename-or-move-a-directory"></a>Könyvtár átnevezése vagy áthelyezése

Könyvtár átnevezése vagy áthelyezése a [DataLakeDirectoryClient.RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) metódus meghívásával. Adja át a kívánt könyvtár elérési útját egy paraméternek. 

Ez a példa átnevezi az `my-subdirectory-renamed`alkönyvtárat a nevére.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Ez a példa `my-subdirectory-renamed` áthelyezi a névvel ellátott `my-directory-2`könyvtárat egy könyvtár alkönyvtárába. 

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

Könyvtár törlése a [DataLakeDirectoryClient.Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) metódus hívásával.

Ez a példa törli `my-directory`a .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Címtár ACL kezelése

A [DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) metódus hívásával és az ACL beállításával a [DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódus hívásával szerezd meg egy könyvtár hozzáférés-vezérlési listáját (ACL).

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

Ez a példa beveszi és beállítja a névvel ellátott könyvtár ACL-t. `my-directory` A `user::rwx,group::r-x,other::rw-` karakterlánc olvasási, írási és végrehajtási engedélyeket ad a tulajdonában lévő felhasználónak, csak olvasási és végrehajtási engedélyeket ad a birtokló csoportnak, és az összes többi olvasási és írási engedélyt ad.

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

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlhivatkozást a célkönyvtárban a [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) osztály egy példányának létrehozásával. Töltsön fel egy fájlt a [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódus hívásával. Győződjön meg arról, hogy a feltöltést a [DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) metódus hívásával fejezi be.

Ez a példa egy szövegfájlt `my-directory`tölt fel a nevesített könyvtárba.    

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
> Ha a fájlmérete nagy, a kódnak több hívást kell kezdeményeznie a [DataLakeFileClient.AppendAsync fájlba.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Fontolja meg a [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metódus használatát. Így a teljes fájlt egyetlen hívással feltöltheti. 
>
> Tekintse meg a következő szakaszban egy példa.

## <a name="upload-a-large-file-to-a-directory"></a>Nagyfájl feltöltése könyvtárba

A [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metódussal nagy fájlokat tölthet fel anélkül, hogy több hívást kellene kezdeményeznie a [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódusba.

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

## <a name="manage-a-file-acl"></a>Fájl acl kezelése

Egy fájl hozzáférés-vezérlési listájának (ACL) beszerezése a [DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) metódus hívásával és az ACL beállítása a [DataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) metódus hívásával.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

Ez a példa beveszi és beállítja egy nevű fájl ACL-ét. `my-file.txt` A `user::rwx,group::r-x,other::rw-` karakterlánc olvasási, írási és végrehajtási engedélyeket ad a tulajdonában lévő felhasználónak, csak olvasási és végrehajtási engedélyeket ad a birtokló csoportnak, és az összes többi olvasási és írási engedélyt ad.

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

## <a name="download-from-a-directory"></a>Letöltés könyvtárból 

Először hozzon létre egy [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) példányt, amely a letölteni kívánt fájlt jelöli. Használja a [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) metódust, és elemezheti a visszatérési értéket egy [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream) objektum beszerzéséhez. Bármely .NET fájlfeldolgozó API-val bájtokat menthet az adatfolyamból egy fájlba. 

Ez a példa [binaryreadert](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) és [FileStreamet](https://docs.microsoft.com/dotnet/api/system.io.filestream) használ a bájtok fájlba mentéséhez. 

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

A könyvtár tartalmának listázása a [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) metódus hívásával, majd az eredmények között történő számbavétel.

Ebben a példában kinyomtatja a könyvtárban található `my-directory`fájlok nevét.

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
* [Minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 –Gen2 leképezés](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

