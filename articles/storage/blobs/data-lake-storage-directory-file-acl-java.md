---
title: Azure Data Lake Storage Gen2 Java SDK fájlokhoz & ACL-khez
description: Használja az Azure Storage-kódtárak Java kezelésére könyvtárak és a fájl-és címtárhozzáférés-vezérlési listák (ACL) a tárfiókok, amelyek hierarchikus névtér (HNS) engedélyezve van.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061562"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Könyvtárak, fájlok és AC-k kezelése a Java használatával az Azure Data Lake Storage Gen2 szolgáltatásban

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet a Java használatával könyvtárakat, fájlokat és engedélyeket olyan tárfiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

[Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API-hivatkozás](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 a Gen2 leképezés](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | visszajelzést[ad](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésekhez nyissa meg [ezt a lapot,](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) és keresse meg a Java-könyvtár legújabb verzióját. Ezután nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adjon hozzá egy függőségi elemet, amely az adott verzióra hivatkozik.

Ha azt tervezi, hogy hitelesíti az ügyfélalkalmazást az Azure Active Directory (AD) használatával, majd adja hozzá a függőséget az Azure Secret Client Library. Lásd: [A titkos ügyféltárcsomag hozzáadása a projekthez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ezután adja hozzá ezeket az importálási kimutatásokat a kódfájlhoz.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Csatlakozás a fiókhoz 

A jelenések ebben a cikkben, létre kell hoznia egy **DataLakeServiceClient** példányt, amely a tárfiókot jelöli. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozás fiókkulcs használatával

Ez a legegyszerűbb módja annak, hogy csatlakozzon egy fiókhoz. 

Ez a példa egy **DataLakeServiceClient-példányt** hoz létre egy fiókkulcs használatával.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Csatlakozás az Azure Active Directory (Azure AD) használatával

Használhatja az [Azure identity ügyfélkódtár Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) az alkalmazás hitelesítéséhez az Azure AD.You can use the Azure identity client library for Java to authenticate your application with Azure AD.

Ez a példa egy **DataLakeServiceClient-példányt** hoz létre egy ügyfélazonosító, egy ügyféltitok és egy bérlői azonosító használatával.  Ezeknek az értékeknek a [beszerzése, olvassa el a jogkivonat beszerzése az Azure AD-től az ügyfélalkalmazásból érkező kérelmek engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört.

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> További példák: az [Azure identity client library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) dokumentáció.


## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer a fájlok tárolójaként működik. Létrehozhat egyet a **DataLakeServiceClient.createFileSystem** metódus meghívásával.

Ez a példa létrehoz `my-file-system`egy fájlrendszert. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtárhivatkozás létrehozása a **DataLakeFileSystemClient.createDirectory** metódus meghívásával.

Ez a példa `my-directory` hozzáad egy elnevezett könyvtárat egy fájlrendszerhez, majd hozzáad egy alkönyvtárat. `my-subdirectory` 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Könyvtár átnevezése vagy áthelyezése

Könyvtár átnevezése vagy áthelyezése a **DataLakeDirectoryClient.rename** metódus meghívásával. Adja át a kívánt könyvtár elérési útját egy paraméternek. 

Ez a példa átnevezi az `my-subdirectory-renamed`alkönyvtárat a nevére.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Ez a példa `my-subdirectory-renamed` áthelyezi a névvel ellátott `my-directory-2`könyvtárat egy könyvtár alkönyvtárába. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Könyvtár törlése a **DataLakeDirectoryClient.deleteWithResponse** metódus hívásával.

Ez a példa törli `my-directory`a .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Címtár ACL kezelése

Ez a példa bekerül, majd beállítja a névvel ellátott könyvtár ACL-ét. `my-directory` Ebben a példában a tulajdonában lévő felhasználó olvasási, írási és végrehajtási engedélyeket ad, a tulajdonában lévő csoport csak olvasási és végrehajtási engedélyeket ad, és az összes többi olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlhivatkozást a célkönyvtárban a **DataLakeFileClient** osztály egy példányának létrehozásával. Töltsön fel egy fájlt a **DataLakeFileClient.append** metódus hívásával. Győződjön meg arról, hogy a feltöltést a **DataLakeFileClient.FlushAsync** metódus hívásával fejezi be.

Ez a példa egy .' `my-directory`nevű könyvtárba tölt fel egy szövegfájlt.

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Ha a fájlmérete nagy, a kódnak több hívást kell kezdeményeznie a **DataLakeFileClient.append** metódushoz. Fontolja meg a **DataLakeFileClient.uploadFromFile** metódus használatát. Így a teljes fájlt egyetlen hívással feltöltheti. 
>
> Tekintse meg a következő szakaszban egy példa.

## <a name="upload-a-large-file-to-a-directory"></a>Nagyfájl feltöltése könyvtárba

A **DataLakeFileClient.uploadFromFile** metódussal nagy fájlokat tölthet fel anélkül, hogy több hívást kellene kezdeményeznie a **DataLakeFileClient.append** metódusba.

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Fájl acl kezelése

Ez a példa bekerül, majd beállítja a fájl acl nevű `upload-file.txt`. Ebben a példában a tulajdonában lévő felhasználó olvasási, írási és végrehajtási engedélyeket ad, a tulajdonában lévő csoport csak olvasási és végrehajtási engedélyeket ad, és az összes többi olvasási hozzáférést biztosít.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Letöltés könyvtárból

Először hozzon létre egy **DataLakeFileClient** példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **DataLakeFileClient.read** metódust. Bármely .NET fájlfeldolgozó API-val bájtokat menthet az adatfolyamból egy fájlba. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Ebben a példában kinyomtatja a könyvtárban található `my-directory`fájlok nevét.

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Lásd még

* [API-referenciadokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 –Gen2 leképezés](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)