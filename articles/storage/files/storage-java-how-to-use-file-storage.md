---
title: Az Azure Files Java kialakított |} Microsoft Docs
description: Ismerje meg, hogyan fejleszthet Java-alkalmazások és adatok tárolásához Azure-fájlokat használó szolgáltatásokat.
services: storage
documentationcenter: java
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: a9585bc77a73cbd84fb2efa201a5745c62f3360a
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738200"
---
# <a name="develop-for-azure-files-with-java"></a>Az Azure Files Java kialakított
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag mutatni, alkalmazásokat és szolgáltatásokat fájl adatok tárolásához Azure-fájlokat használó Java használatával alapjait. Ebben az oktatóanyagban a rendszer hozzon létre egy konzolalkalmazást és bemutatják, hogyan Azure fájlokat és Java alapszintű műveletek végrehajtása:

* Hozzon létre vagy töröljön az Azure fájlmegosztások
* Hozzon létre vagy töröljön a könyvtárak
* Fájlok és könyvtárak az Azure fájlmegosztások számbavétele
* Töltse fel, töltse le és törölje a fájlt

> [!Note]  
> Azure fájlok érhető SMB-n keresztül, mert is lehet írni a szabványos Java i/o-osztályokat Azure fájlmegosztás elérhető alkalmazásokat. Ez a cikk ismerteti, hogyan írhat alkalmazásokat, amelyek használják az Azure Storage Java SDK, amely használja a [Azure fájlok REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) ügyfélcsatornája az Azure Fileshoz.

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A minták létrehozásához szüksége lesz a Java fejlesztői készlet (JDK) és a [Azure Storage SDK for Java](https://github.com/Azure/azure-storage-java). Meg is létrehozott egy Azure storage-fiókot.

## <a name="set-up-your-application-to-use-azure-files"></a>Állítsa be az alkalmazás Azure-fájlok használata
Az Azure storage API-k, vegye fel a következő utasítás célhelyeként a a társzolgáltatás hozzáférni a Java-fájl elejéhez.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Azure-fájlok használata esetén meg kell az Azure storage-fiókjához. Az első lépés is konfigurálhat egy kapcsolati karakterláncot, amely csatlakozni a tárfiókhoz fogjuk használni. Nézzük meg egy statikus változót ehhez.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Cserélje le a tárfiók a tényleges értékek your_storage_account_name és your_storage_account_key.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Az Azure storage-fiókhoz való csatlakozást
A tárfiók csatlakozni kell használnia a **CloudStorageAccount** objektum átadása egy kapcsolati karakterláncot a **elemezni** metódus.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** egy InvalidKeyException jelez, ezért kell tenni egy try vagy catch blokkon belül.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A fájlok és könyvtárak Azure fájlban találhatók nevű tároló egy **megosztás**. A tárfiók lehet a kapacitásával lehetővé teszi tetszőleges számú megosztások. A megosztást és tartalmát hozzáférést szerezni kell használnia egy Azure fájlok ügyfél.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Az Azure Fileshoz-ügyfélprogrammal, majd szerezhet be egy hivatkozást egy megosztásra.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

A megosztás ténylegesen létrehozásához használja a **createIfNotExists** CloudFileShare metódusa.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Ezen a ponton **megosztása** nevű megosztáshoz egy hivatkozást tartalmaz **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Az Azure fájlmegosztások törlése
Egy megosztás törlése meghívásával történik a **deleteIfExists** CloudFileShare objektum metódus. Itt látható mintakód, amelyet, amely.

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
Tárolási tegyen alkönyvtárak így ahelyett hogy ezek a gyökérkönyvtárban található fájlok is rendezhetők. Az Azure Files hozhat létre a fiókját engedélyezi a könyvtárat. Az alábbi kódot hoz létre a következő nevű **sampledir** a gyökérkönyvtárban.

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
A könyvtár törlése mely egyszerű feladat, érdemes megjegyezni, hogy nem törölhető a fájlok továbbra is tartalmazó könyvtár vagy más címtárakban.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak az Azure fájlmegosztások számbavétele
Fájlok és könyvtárak egy megosztáson belüli listájának beszerzésével könnyen történik meghívásával **listFilesAndDirectories** CloudFileDirectory hivatkozással. A metódus ListFileItem objektumokat, amely akkor is többször listáját adja vissza. Tegyük fel az alábbi kód felsorolja a fájlok és könyvtárak a legfelső szintű könyvtárán belül.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Fájl feltöltése
Ebben a szakaszban megtudhatja, hogyan feltölteni a fájlt a helyi tároló megosztás gyökérkönyvtárában alakzatot.

Az első lépés egy feltöltés az beszerzése hivatkozni kell a könyvtárban, ahol kell tárolni. Ehhez hívja a **getRootDirectoryReference** a megosztás metódusa.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Most, hogy a megosztás gyökérkönyvtárában mutató hivatkozás, feltöltheti a fájlt, az alábbi kód használatával.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Fájl letöltése
Az Azure fájlokra vonatkozóan fog végrehajtani a gyakoribb műveletek egyik fájlok letöltéséhez. A következő példában a kód SampleFile.txt tölti le, és megjeleníti a tartalmát.

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
Egy másik közös Azure fájlok művelet a fájl törlése. Az alábbi kód töröl egy fájlt egy nevű könyvtár tárolt SampleFile.txt nevű **sampledir**.

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
Ha azt szeretné, ha többet szeretne megtudni az egyéb Azure storage API-k, az alábbi hivatkozásokat követve.

* [Azure Java-fejlesztőknek](/java/azure)/)
* [Az Azure Storage Java SDK](https://github.com/azure/azure-storage-java)
* [Az Azure Storage SDK for Android](https://github.com/azure/azure-storage-android)
* [Az Azure Storage ügyfél SDK-dokumentáció](http://dl.windowsazure.com/storage/javadoc/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)
* [Adatátvitel az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md)
* [Azure Files-problémák hibaelhárítása – Windows](storage-troubleshoot-windows-file-connection-problems.md)