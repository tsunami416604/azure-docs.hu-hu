---
title: Fejlesztés az Azure Files javával |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Java-alkalmazások és szolgáltatások, amelyek az Azure Files használatának a fájladatok tárolásához.
services: storage
author: wmgries
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 62a771b831c88f73df91c50f34ba6de0d95b832e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453093"
---
# <a name="develop-for-azure-files-with-java"></a>Fejlesztés az Azure Files javával
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag bemutatja annak alapjait, a Java használatával történő alkalmazásokat és szolgáltatásokat, amelyek az Azure Files használatának a fájladatok tárolásához. Ebben az oktatóanyagban azt fogja hozzon létre egy konzolalkalmazást, és bemutatják, hogyan hajthat végre alapszintű műveleteket a Java és az Azure Files:

* Hozzon létre vagy töröljön az Azure-fájlmegosztások
* Hozzon létre, és törölje a címtárakat
* Fájlok és mappák az Azure-fájlmegosztások számbavétele
* Feltöltésére, letöltésére és fájl törlése

> [!Note]  
> Mivel az Azure Files SMB-n keresztül lehet elérni, akkor lehet alkalmazások írását, amelyek a szabványos Java-i/o-osztály használatával az Azure-fájlmegosztás eléréséhez. Ez a cikk azt ismerteti, hogyan írhat alkalmazásokat, amelyek használják az Azure Storage Java SDK-t használ a [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) felvenni a kapcsolatot az Azure Files.

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A minták összeállításához, szüksége lesz a Java fejlesztői készlet (JDK) és a [Javához készült Azure Storage SDK](https://github.com/Azure/azure-storage-java). Meg is létrehozott egy Azure storage-fiókot.

## <a name="set-up-your-application-to-use-azure-files"></a>Állítsa be alkalmazását az Azure Files használata
Az Azure storage API-k használatához adja hozzá a következő utasítást, ha szeretne hozzáférni a társzolgáltatás a Java-fájl elejéhez.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Az Azure Files használatának kell csatlakoznia az Azure storage-fiókba. Az első lépés egy kapcsolati karakterláncot, amely segítségével a tárfiókhoz való kapcsolódás konfigurálása lenne. Határozzon meg egy statikus változó valósítható meg.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Cserélje le tényleges értéke alapján a tárfiók your_storage_account_name és your_storage_account_key.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Az Azure storage-fiók csatlakoztatása
Csatlakozhat a tárfiókhoz, kell használnia a **CloudStorageAccount** objektum átadása egy kapcsolati karakterláncot a saját **elemezni** metódus.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** egy InvalidKeyException jelez, így try/catch blokkon belül el kell.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A fájlok és könyvtárak az Azure Files egy nevű tárolóban található egy **megosztás**. A storage-fiókot, a fiók kapacitás lehetővé teszi, hogy annyi megosztások rendelkezhet. Egy megosztást és tartalmát való hozzáférés megszerzéséhez szeretné használni az Azure Files-ügyfél.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Az Azure Files-ügyfelet használ, majd szerezhet be egy hivatkozást a megosztáshoz.

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

Ezen a ponton **megosztása** egy hivatkozást a megosztásban található **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Azure-fájlmegosztás törlése
Egy megosztás törlésekor történik meghívásával a **deleteIfExists** metódus CloudFileShare objektumon. Itt látható, amely, amely mintakódot.

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
Tároló található alkönyvtárai nem ezek mindegyike a gyökérmappában lévő fájlokat azáltal is rendszerezheti. Az Azure Files lehetővé teszi, hogy hozzon létre annyi könyvtárakon, a fiók lehetővé teszi. Az alábbi kód létrehoz nevű alkönyvtárban **sampledir** a gyökérkönyvtárban.

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
Egy egyszerű feladat, egy könyvtár törlése folyamatban, bár Megjegyzendő, hogy nem törölhető a fájlok továbbra is tartalmazó könyvtárba, vagy más címtárakban.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és mappák az Azure-fájlmegosztások számbavétele
Fájlok és könyvtárak a megosztáson belüli beszerzése könnyedén elvégezhető meghívásával **listFilesAndDirectories** CloudFileDirectory hivatkozik. A módszer is iterálása ListFileItem objektumok listáját adja vissza. Tegyük fel a következő kódot a fájlok és könyvtárak a legfelső szintű könyvtárán belül található listázza.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Fájl feltöltése
Ebben a szakaszban megismerheti, hogyan feltölthet egy fájlt a helyi tárolóból gyökérkönyvtárában található egy megosztást az alakzatot kell.

Az első lépés egy fájl feltöltése az szerezzen be egy hivatkozást a könyvtárban, ahol kell tárolni. Meghívásával ehhez a **getRootDirectoryReference** metódus a megosztott objektum.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Most, hogy egy hivatkozást a megosztás gyökérkönyvtárában van, feltölthet egy fájlt a következő kód használatával.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Fájl letöltése
A gyakoribb műveleteket fogja elvégezni ellen az Azure Files egyik fájlok letöltéséhez. A következő példában a kód letölti SampleFile.txt, és megjeleníti a tartalmát.

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
Egy másik gyakori Azure Files-művelet a fájl törlése. Az alábbi kód töröl egy fájlt, nevű könyvtárat tárolt SampleFile.txt nevű **sampledir**.

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
Ha szeretné, ha többet szeretne megtudni a más az Azure storage API-k, az alábbi hivatkozásokat követve.

* [Azure Java-fejlesztőknek készült](/java/azure)/)
* [Az Azure Storage-hoz készült Java SDK](https://github.com/azure/azure-storage-java)
* [Az Azure Storage SDK for Android](https://github.com/azure/azure-storage-android)
* [Azure Storage ügyféloldali SDK-referencia](http://dl.windowsazure.com/storage/javadoc/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)
* [Adatátvitel az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md)
* [Azure Files-problémák hibaelhárítása – Windows](storage-troubleshoot-windows-file-connection-problems.md)
