---
title: 'Rövid útmutató: Az Azure Blob storage-tár v12 – Java'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Blob storage-beli ügyfélkód12-es Java-verziót egy tároló és egy blob blob (objektum) tárolóban. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 15e0eaaeb407ad22220faa7d085eea805fe62679
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731341"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Gyorsútmutató: Blobok kezelése Java v12 SDK-val

Ebben a rövid útmutatóban megtudhatja, hogy a Java használatával kezelheti a blobokat. A blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatot képesek tárolni, beleértve a képeket, dokumentumokat, adatfolyam-továbbítási és archiválási adatokat. Blobokat fog feltölteni, letölteni és listázni, és tárolókat hozhat létre és törölhet.

[API-referenciadokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [mintái](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy tárfiókot.](../common/storage-account-create.md)
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verzió.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Az előző SDK-verzió ismerkedéséhez olvassa el a [Gyorsútmutató: Blobok kezelése Java v8 SDK-val](storage-quickstart-blobs-java-legacy.md)című témakört.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Blob storage-ügyfélkódtár v12 Java-hoz való használata.

### <a name="create-the-project"></a>A projekt létrehozása

*Blob-quickstart-v12 nevű Java-alkalmazás*létrehozása.

1. Egy konzolablakban (például a cmd, a PowerShell vagy a Bash) a Maven használatával hozzon létre egy új konzolalkalmazást *blob-quickstart-v12*névvel. "Hello world!" parancs létrehozásához írja be a következő **mvn** parancsot. Java projekt.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. A projekt generálásának kimenete valahogy így fog kinézni:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. A blob-quickstart-v12 könyvtár oldalán hozzon létre egy másik *könyvtárat,* az úgynevezett data .In side the *blob-quickstart-v12* directory, create another directory called data . Ez az, ahol a blob adatfájlok jönnek létre és tárolják.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemet a függőségek csoportjához.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. Keresse meg a */src/main/java/com/blobs/quickstart* könyvtárat
1. Az *App.java* fájl megnyitása a szerkesztőben
1. Az `System.out.println("Hello world!");` utasítás törlése
1. Direktívák hozzáadása `import`

Itt a kód:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob storage háromféle erőforrást kínál:

* A tárfiók
* Tároló a tárfiókban
* Blob a tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob-tárolási architektúra diagramja](./media/storage-blobs-introduction/blob1.png)

Az alábbi Java-osztályok segítségével kezelheti ezeket az erőforrásokat:

* [BlobServiceClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html) `BlobServiceClient` Az osztály lehetővé teszi az Azure Storage-erőforrások és blobtárolók kezelésére. A tárfiók biztosítja a Blob szolgáltatás legfelső szintű névterét.
* [BlobServiceClientBuilder:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html) `BlobServiceClientBuilder` Az osztály egy folyékony építő API-t biztosít `BlobServiceClient` az objektumok konfigurálásának és példányosításának elősegítésére.
* [BlobContainerClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) `BlobContainerClient` Az osztály lehetővé teszi az Azure Storage-tárolók és azok blobjainak kezelésére.
* [BlobClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) `BlobClient` Az osztály lehetővé teszi az Azure Storage-blobok kezelésére.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): `BlobItem` Az osztály a hívásból visszaadott egyes blobokat `listBlobsFlat`jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan hajthatja végre a következőket az Azure Blob storage-ügyfélrendszertár Java-hoz:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát a [tárolási kapcsolat karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból.

Adja hozzá ezt `Main` a kódot a metódushoz:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód hozzáfűz egy UUID értéket a tároló nevéhez annak érdekében, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ezután hozzon létre egy példányt a [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) osztály, majd hívja meg a [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) metódust, hogy ténylegesen hozza létre a tárolót a tárfiókban.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Szövegfájlt hoz létre *data* a helyi adatkönyvtárban.
1. Egy [BlobClient-objektumra](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) mutató hivatkozást kér le a [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) metódus hívásával a tárolón a [Tároló létrehozása](#create-a-container) szakaszból.
1. Feltölti a helyi szöveges fájlt a blobba az [uploadFromFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) metódus hívásával. Ez a módszer létrehozza a blobot, ha még nem létezik, de nem írja felül, ha igen.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Sorolja fel a blobok a tárolóban hívja meg a [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) metódus. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listaelem-művelet csak azt az egy blobot adja vissza.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [downloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) metódus hívásával. A példakód hozzáadja a "DOWNLOAD" utótagot a fájlnévhez, így mindkét fájlt láthatja a helyi fájlrendszerben.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az erőforrásokat az alkalmazás által létrehozott eltávolításával a teljes tároló [ti a törlési](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) módszerrel. Törli az alkalmazás által létrehozott helyi fájlokat is.

Az alkalmazás szünetela felhasználói `System.console().readLine()` bevitel a hívás, mielőtt törli a blob, tároló és a helyi fájlokat. Ez jó alkalom annak ellenőrzésére, hogy az erőforrások megfelelően jöttek-e létre, mielőtt törölné őket.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztfájlt a helyi mappában, és feltölti azt a Blob storage-ba. A példa ezután felsorolja a blobok a tárolóban, és letölti a fájlt egy új nevet, így össze lehet hasonlítani a régi és az új fájlokat.

Nyissa meg a *pom.xml* fájlt tartalmazó könyvtárat, és `mvn` fordítsa le a projektet a következő paranccsal.

```console
mvn compile
```

Ezután készítse el a csomagot.

```console
mvn package
```

Az alkalmazás `mvn` végrehajtásához futtassa a következő parancsot.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

A tisztítási folyamat megkezdése előtt ellenőrizze, hogy az *adatmappában* van-e a két fájl. Ha megnyitja őket, láthatja, hogy megegyeznek.

Miután ellenőrizte a fájlokat, nyomja le az **Enter** billentyűt a tesztfájlok törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhatja a blobokat Java használatával.

A Blob storage mintaalkalmazásának megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Az Azure Blob storage SDK v12 Java-mintái](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* További információ: [Az Azure SDK Java-hoz](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)című témakörben olvashat.
* Oktatóanyagok, minták, rövid útmutatók és egyéb dokumentációk, látogasson el [az Azure for Java felhőfejlesztők.](/azure/developer/java/)
