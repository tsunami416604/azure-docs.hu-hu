---
title: Fejlesztés az Azure Files Javával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre olyan Java-alkalmazásokat és-szolgáltatásokat, amelyek a fájlok tárolásához Azure Files használnak.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 54f545c2d4f897f85ab3e5fd10d07666e93e4f41
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509676"
---
# <a name="develop-for-azure-files-with-java"></a>Fejlesztés az Azure Files szolgáltatáshoz Javával
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan alkalmazásokat vagy szolgáltatásokat a Java használatával, amelyek a fájlok tárolására Azure Files használnak. Ebben az oktatóanyagban létrehozunk egy konzolszoftver-alkalmazást, és bemutatjuk, hogyan lehet alapszintű műveleteket végrehajtani a Java és a Azure Files használatával:

* Azure-fájlmegosztás létrehozása és törlése
* Könyvtárak létrehozása és törlése
* Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén
* Fájl feltöltése, letöltése és törlése

> [!Note]  
> Mivel Azure Files az SMB protokollon keresztül érhető el, az Azure-fájlmegosztás szabványos Java I/O-osztályok használatával való elérésére szolgáló alkalmazások írhatók. Ez a cikk leírja, hogyan írhatók az Azure Storage Java SDK-t használó alkalmazások, amelyek a [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) használatával kommunikálnak a Azure Filesekkel.

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A minták létrehozásához szüksége lesz a Java Development Kit (JDK) és a [Javához készült Azure Storage SDK](https://github.com/Azure/azure-storage-java)-ra. Létre kell hoznia egy Azure Storage-fiókot is.

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása Azure Files használatára
Az Azure Storage API-k használatához adja hozzá a következő utasítást a Java-fájl elejéhez, ahová a Storage szolgáltatást szeretné elérni.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage-beli kapcsolatok karakterláncának beállítása
Azure Files használatához csatlakoznia kell az Azure Storage-fiókjához. Az első lépés egy kapcsolati karakterlánc konfigurálása, amelyet a rendszer a Storage-fiókhoz való kapcsolódáshoz használ. Hozzunk létre egy statikus változót ehhez.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Cserélje le a your_storage_account_name és a your_storage_account_keyt a Storage-fiók tényleges értékeire.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Csatlakozás Azure Storage-fiókhoz
A Storage-fiókhoz való kapcsolódáshoz a **CloudStorageAccount** objektumot kell használnia, amely a kapcsolati karakterláncot átadja az **elemzési** metódusának.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

A **CloudStorageAccount. Parse** egy InvalidKeyException dob, így azt egy try/catch blokkban kell elhelyeznie.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A Azure Files összes fájlja és könyvtára egy **megosztás**nevű tárolóban található. A Storage-fiókja annyi megosztással rendelkezhet, amennyit a fiók kapacitása lehetővé tesz. Egy megosztáshoz és annak tartalmához való hozzáféréshez Azure Files-ügyfelet kell használnia.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

A Azure Files ügyfél használatával megszerezheti a megosztásra mutató hivatkozást.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

A megosztás tényleges létrehozásához használja a CloudFileShare objektum **createifnotexists metódust** metódusát.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Ezen a ponton a **megosztás** egy **sampleshare**nevű megosztásra mutató hivatkozást tárol.

## <a name="delete-an-azure-file-share"></a>Azure-fájlmegosztás törlése
Egy megosztás törléséhez hívja meg a **deleteifexists paranccsal** metódust egy CloudFileShare objektumon. Az alábbi mintakód erre a kódra vonatkozik.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
A tárolót úgy is rendszerezheti, hogy az alkönyvtárakban helyez el fájlokat, ahelyett, hogy mindegyiket a gyökérkönyvtárba helyezné. Azure Files lehetővé teszi, hogy annyi könyvtárat hozzon létre, amennyit a fiókja engedélyezni fog. Az alábbi kód létrehoz egy **sampledir** nevű alkönyvtárat a gyökérkönyvtár alatt.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Könyvtár törlése
A címtár törlése egyszerű feladat, bár érdemes megjegyezni, hogy nem törölhet olyan könyvtárat, amely továbbra is tartalmaz fájlokat vagy más címtárakat.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén
Egy megosztáson belüli fájlok és könyvtárak listájának beszerzése egyszerűen elvégezhető a **ListFilesAndDirectories** CloudFileDirectory-hivatkozással való meghívásával. A metódus visszaadja a ListFileItem objektumok listáját, amelyeket megadhat. Az alábbi kód például a gyökérkönyvtárban található fájlokat és könyvtárakat sorolja fel.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Fájl feltöltése
Ebből a szakaszból megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból egy megosztás gyökérkönyvtárára.

A fájlok feltöltésének első lépéseként be kell szereznie egy hivatkozást arra a könyvtárba, ahol a fájl található. Ezt úgy teheti meg, hogy meghívja a megosztás objektum **getRootDirectoryReference** metódusát.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Most, hogy már van egy hivatkozása a megosztás gyökérkönyvtárára, feltöltheti a fájlt a következő kód használatával.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Fájl letöltése
Az Azure Files a fájlok letöltésének egyik leggyakoribb művelete. A következő példában a kód letölti SampleFile.txt és megjeleníti annak tartalmát.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Fájl törlése
Egy másik gyakori Azure Files művelet a fájl törlése. A következő kód törli a **sampledir**nevű könyvtárban tárolt SampleFile.txt nevű fájlt.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure Storage API-król, kövesse az alábbi hivatkozásokat.

* [Azure Java-fejlesztőknek](/java/azure)/)
* [Javához készült Azure Storage SDK](https://github.com/azure/azure-storage-java)
* [Androidhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-android)
* [Azure Storage ügyféloldali SDK-referencia](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)
* [Adatok átvitele a AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md)
* [Azure Files-problémák hibaelhárítása – Windows](storage-troubleshoot-windows-file-connection-problems.md)
