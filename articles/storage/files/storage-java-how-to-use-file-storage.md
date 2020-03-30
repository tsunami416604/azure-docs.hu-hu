---
title: Fejlesztés Az Azure Files java-val | Microsoft dokumentumok
description: Ismerje meg, hogyan fejleszthet Java-alkalmazásokat és -szolgáltatásokat, amelyek az Azure Files segítségével tárolják a fájladatokat.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837463"
---
# <a name="develop-for-azure-files-with-java"></a>Fejlesztés az Azure Files szolgáltatáshoz Javával
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag bemutatja a Java használatának alapjait olyan alkalmazások vagy szolgáltatások fejlesztéséhez, amelyek az Azure Files-t használják a fájladatok tárolására. Ebben az oktatóanyagban létrehozunk egy konzolalkalmazást, és bemutatjuk, hogyan hajthatja végre az alapvető műveleteket a Java és az Azure Files használatával:

* Azure-fájlmegosztások létrehozása és törlése
* Könyvtárak létrehozása és törlése
* Fájlok és könyvtárak számbavétele Azure-fájlmegosztásban
* Fájl feltöltése, letöltése és törlése

> [!Note]  
> Mivel az Azure Files sMB-n keresztül érhető el, az Azure-fájlmegosztáshoz a szabványos Java I/O-osztályok használatával elérhető alkalmazások is írhatók. Ez a cikk ismerteti, hogyan írhat alkalmazásokat, amelyek az Azure Storage Java SDK, amely az [Azure Files REST API-t](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) használja az Azure Files használatával az Azure Files használatával.

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A minták létrehozásához szüksége lesz a Java Development Kit (JDK) és az [Azure Storage SDK Java.For](https://github.com/Azure/azure-storage-java)build the samples, you will need the Java Development Kit (JDK) and the Azure Storage SDK for Java . Azure storage-fiókot is létre kellett volna hoznia.

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása az Azure Files használatára
Az Azure storage API-k használatához adja hozzá a következő utasítást a Java-fájl tetejéhez, ahonnan hozzá kíván férni a tárolási szolgáltatáshoz.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure storage-kapcsolati karakterlánc beállítása
Az Azure Files használatához csatlakoznia kell az Azure storage-fiókjához. Az első lépés egy kapcsolati karakterlánc konfigurálása lenne, amelyet a tárfiókhoz való csatlakozáshoz használunk. Definiáljunk egy statikus változót ehhez.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Cserélje le your_storage_account_name és your_storage_account_key a tárfiók tényleges értékeire.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Csatlakozás Azure-tárfiókhoz
A tárfiókhoz való csatlakozáshoz a **CloudStorageAccount** objektumot kell használnia, és egy kapcsolati karakterláncot kell átadnia annak **elemzési metódusának.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** dob egy InvalidKeyException így meg kell tenni, hogy egy próba/fogás blokk.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Az Azure Files összes fájlja és könyvtára **egy Megosztás**nevű tárolóban található. A tárfiók annyi megosztással rendelkezhet, amennyit a fiók kapacitása lehetővé tesz. Ahhoz, hogy hozzáférést szerezzen egy megosztáshoz és annak tartalmához, egy Azure Files ügyfelet kell használnia.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Az Azure Files ügyfél használatával ezután beszerezhet egy megosztásra mutató hivatkozást.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

A megosztás tényleges létrehozásához használja a CloudFileShare objektum **createIfNotExists** metódusát.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Ezen a ponton a **megosztás** egy **sampleshare**nevű megosztásra mutató hivatkozást tartalmaz.

## <a name="delete-an-azure-file-share"></a>Azure-fájlmegosztás törlése
A megosztás törlése a **deleteIfExists** metódus meghívásával történik egy CloudFileShare objektumon. Itt van a minta kód, hogy nem ezt.

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
A tárolást úgy is rendszerezheti, hogy fájlokat helyez el az alkönyvtárakban, és nem mindegyik van a gyökérkönyvtárban. Az Azure Files lehetővé teszi, hogy annyi könyvtárat hozzon létre, amennyit a fiókja lehetővé tesz. Az alábbi kód létrehoz egy **mintakönyvtár** nevű alkönyvtárat a gyökérkönyvtár alatt.

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
A könyvtár törlése egyszerű feladat, bár meg kell jegyezni, hogy nem lehet törölni egy olyan könyvtárat, amely továbbra is tartalmaz fájlokat vagy más könyvtárakat.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak számbavétele Azure-fájlmegosztásban
A megosztáson belüli fájlok és könyvtárak listájának beszerzése könnyen elvégezhető a **listFilesAndDirectories** hívásával egy CloudFileDirectory hivatkozáson. A metódus visszaadja a ListFileItem objektumok listáját, amelyeken temetethet. A következő kód például a gyökérkönyvtárban lévő fájlokat és könyvtárakat sorolja fel.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Fájl feltöltése
Ebből a szakaszból megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból egy megosztás gyökérkönyvtárába.

A fájl feltöltésének első lépése az, hogy hivatkozást szerezzen arra a könyvtárra, ahol tartózkodnia kell. Ehhez hívja meg a megosztásobjektum **getRootDirectoryReference** metódusát.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Most, hogy már rendelkezik hivatkozással a megosztás gyökérkönyvtárára, a következő kód használatával tölthet fel egy fájlt.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Fájl letöltése
Az Azure Files-on végrehajtott leggyakoribb műveletek egyike a fájlok letöltése. A következő példában a kód letölti a SampleFile.txt fájlt, és megjeleníti annak tartalmát.

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
Egy másik gyakori Azure Files művelet a fájl törlése. A következő kód törli a SampleFile.txt nevű fájlt, amely egy **sampledir**nevű könyvtárban található.

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
Ha szeretne többet megtudni más Azure storage API-k, kövesse ezeket a hivatkozásokat.

* [Az Azure for Java fejlesztői](/java/azure)/)
* [Azure Storage SDK Java-hoz](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK Android-alapú](https://github.com/azure/azure-storage-android)
* [Azure Storage ügyféloldali SDK-referencia](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage-csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)
* [Adatok átvitele az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md)
* [Azure Files-problémák hibaelhárítása – Windows](storage-troubleshoot-windows-file-connection-problems.md)
