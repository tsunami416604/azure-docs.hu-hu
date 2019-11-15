---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – Java'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage ügyféloldali kódtárat a javára tárolók és Blobok (Object) tárolók létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5196dbbfb52ce75031a53764b371d6d34b43fba7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091420"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-java"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtára a Javához

Ismerkedjen meg a Javához készült Azure Blob Storage ügyféloldali kódtáraval. Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

> [!NOTE]
> A korábbi SDK-verzió használatának megkezdéséhez lásd [: gyors útmutató: Azure Blob Storage ügyféloldali kódtára a Javához](storage-quickstart-blobs-java-legacy.md).

Használja a Javához készült Azure Blob Storage ügyféloldali kódtárat a következőhöz:

* Tároló létrehozása
* BLOB feltöltése az Azure Storage-ba
* Egy tároló összes blobjának listázása
* BLOB letöltése a helyi számítógépre
* Tároló törlése

[API-referenciák dokumentációja](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [Package (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verziója
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet az Azure Blob Storage-ügyfélhez készült, a Javával való együttműködéshez.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-Gyorsindítás-V12*nevű Java Core-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) a Maven használatával hozzon létre egy új, *blob-Gyorsindítás-V12*nevű Console-alkalmazást. Írja be az alábbi **MVN** -parancsot egyetlen sorba, és hozzon létre egy egyszerű "Helló világ!" Java-projekt. A parancs itt jelenik meg több sorban az olvashatóság érdekében.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart
                          -DartifactId=blob-quickstart-v12
                          -DarchetypeArtifactId=maven-archetype-quickstart
                          -DarchetypeVersion=1.4
                          -DinteractiveMode=false
   ```

1. A projekt létrehozásának eredményét a következőhöz hasonló módon kell kinéznie:

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

1. A *blob-Gyorsindítás-V12* könyvtárban hozzon létre egy másik könyvtárat, amely az *adat*nevű. A blob-adatfájlokat a rendszer létrehozza és tárolja.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *Pom. XML* fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Navigáljon a */src/Main/Java/com/Blobs/Quickstart* könyvtárához
1. Nyissa meg az *app. Java* fájlt a szerkesztőben
1. A `System.out.println("Hello world!");` utasítás törlése
1. `import` irányelvek hozzáadása

A kód a következő:

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

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

Ha a minta alkalmazás az Azure Storage-ba irányuló kérést tesz elérhetővé, akkor azt engedélyezni kell. A kérések engedélyezéséhez adja hozzá a Storage-fiók hitelesítő adatait az alkalmazáshoz kapcsolódási karakterláncként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le a `<yourconnectionstring>`t a tényleges kapcsolatok karakterláncára.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót a Windows rendszerben, el kell indítania a parancssorablak új példányát.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Programok újraindítása

A környezeti változó hozzáadását követően indítsa újra a futó programokat, amelyeknek olvasnia kell a környezeti változót. A folytatás előtt indítsa el például a fejlesztési környezetet vagy a szerkesztőt.

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blob-introduction/blob1.png)

A következő Java-osztályok használhatók az alábbi erőforrásokkal való kommunikációhoz:

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését. A Storage-fiók a legfelső szintű névteret biztosítja a Blob service számára.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): a `BlobServiceClientBuilder` osztály egy Fluent Builder API-t biztosít a `BlobServiceClient` objektumok konfigurációjának és példányának támogatásához.
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html): a `BlobContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.
* [Blobelemet](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): a `BlobItem` osztály a `listBlobsFlat`hívásával visszaadott blobokat jelöli.

## <a name="code-examples"></a>Példák a kódokra

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következőket az Azure Blob Storage ügyféloldali kódtára Javához:

* [A kapcsolatok karakterláncának beolvasása](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő Blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `Main` metódushoz:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("CONNECT_STR");
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ezután hozza létre a [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) osztály egy példányát, majd hívja meg a [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) metódust, hogy ténylegesen létrehozza a tárolót a Storage-fiókban.

Adja hozzá ezt a kódot a `Main` metódus végéhez:

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

1. Létrehoz egy szövegfájlt *a helyi* adatkönyvtárban.
1. Beolvas egy [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) objektumra mutató hivatkozást úgy, hogy meghívja a [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) metódust a tárolóban a [tároló létrehozása](#create-a-container) szakaszban.
1. A [uploadFromFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) metódus meghívásával feltölti a helyi szövegfájlt a blobba. Ez a metódus létrehozza a blobot, ha még nem létezik, de nem írja felül, ha igen.

Adja hozzá ezt a kódot a `Main` metódus végéhez:

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

A tárolóban lévő Blobok listázása a [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a `Main` metódus végéhez:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [downloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) metódus meghívásával. A példában szereplő kód a "Letöltés" utótagot adja hozzá a fájl nevéhez, hogy mindkét fájl látható legyen a helyi fájlrendszerben.

Adja hozzá ezt a kódot a `Main` metódus végéhez:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat, ha eltávolítja a teljes tárolót a [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) metódus használatával. Emellett törli az alkalmazás által létrehozott helyi fájlokat is.

Az alkalmazás a `System.console().readLine()` meghívásával szünetelteti a felhasználói bevitelt, mielőtt törli a blobot, a tárolót és a helyi fájlokat. Ez jó eséllyel ellenőrizhető, hogy az erőforrások valóban helyesen lettek-e létrehozva a Törlésük előtt.

Adja hozzá ezt a kódot a `Main` metódus végéhez:

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

Ez az alkalmazás létrehoz egy tesztoldalt a helyi mappában, és feltölti azt a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy össze lehessen hasonlítani a régi és az új fájlokat.

Navigáljon a *Pom. XML* fájlt tartalmazó könyvtárra, és fordítsa le a projektet a következő `mvn` parancs használatával.

```console
mvn compile
```

Ezután hozza létre a csomagot.

```console
mvn package
```

Futtassa az alábbi `mvn` parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Az alkalmazás kimenete az alábbi példához hasonló:

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

A tisztítási folyamat megkezdése előtt tekintse meg a két fájl *MyDocuments* mappáját. Ha megnyitja őket, láthatja, hogy megegyeznek.

A fájlok ellenőrzése után nyomja le az **ENTER** billentyűt a tesztoldal törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a Java használatával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Java-minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* További információért lásd a [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)-t.
* Oktatóanyagok, minták, gyors üzembe helyezés és egyéb dokumentáció: [Azure for Java Cloud Developers](/azure/java/).
