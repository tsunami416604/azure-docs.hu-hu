---
title: "Azure gyors üzembe helyezés – az Azure Blob Storage tárolót használó Java átviteli objektumok |} Microsoft Docs"
description: "Gyorsan ismerje meg, és a Java használatával Azure Blob-tároló objektumok átvitele"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 5676cef446de7a68d3d8fd1a3b6833a5de184ea1
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Átviteli objektumok az Azure Blob Storage tárolóban javás környezetekben

A gyors üzembe helyezés elsajátíthatja Java használatával töltse fel, töltse le, és az Azure Blob storage tárolója blokk blobok listázása.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* Egy IDE, amely rendelkezik a Maven-integráció telepítése

* Azt is megteheti telepítése és konfigurálása a parancssorból működéséhez Maven

Ez az oktatóanyag használja [Eclipse](http://www.eclipse.org/downloads/) a "Eclipse IDE a Java fejlesztői" konfigurációjával kapcsolatban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-java-quickstart) alapvető Konzolalkalmazás a gyors üzembe helyezés használatban van. 

Használjon [git](https://git-scm.com/) letölteni az alkalmazást a fejlesztési környezet egy példányát. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Ez a parancs klónokat a tárházat a helyi git-mappába. Nyissa meg a projektet, indítsa el az eclipse-ben, és zárja be az üdvözlőképernyőn. Válassza ki **fájl** majd **nyissa meg a projektet a fájlrendszerből...** . Győződjön meg arról, hogy **hibakeresés, és konfigurálja a projekt natures** be van jelölve. Válassza ki **Directory** keresse meg a klónozott tárház, azon belül tárolásához válassza ki a **javaBlobsQuickstart** mappa. Győződjön meg arról, hogy a **javaBlobsQuickstarts** projekt jelenik meg az Eclipse-projekt, majd jelölje be **Befejezés**.

Importálja a projekt befejezése után nyissa meg a **AzureApp.java** (található **blobQuickstart.blobAzureApp** található **src/main/java**), és cserélje le a `accountname`és `accountkey` található a `storageConnectionString` karakterlánc. Ezután futtassa az alkalmazást.
     

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása
    
Az alkalmazás a kapcsolati karakterláncot kell megadnia a tárfiók. Nyissa meg a **AzureApp.Java** fájlt. Keresés a `storageConnectionString` változó. Cserélje le a `AccountName` és `AccountKey` Azure-portálról mentett értékekkel a kapcsolódási karakterláncban értékeket. A `storageConnectionString` az alábbihoz kell hasonlítania:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>A minta futtatásához

Ez a minta létrehoz egy tesztelési fájlt az alapértelmezett címtárban (a dokumentumok, a windows-felhasználók számára), feltölti a Blob Storage, a tárolóban lévő blobok sorolja fel, majd letölti a fájlt új néven, a régi és új fájlok összehasonlítás. 

A minta futtatásához billentyűkombináció lenyomásával **Ctrl + F11** az eclipse-ben.

Ha a minta Maven használatával, a parancssor futtatásához, nyisson meg egy kezelőfelületet, és keresse meg **blobAzureApp** a klónozott directory belül. Írja be `mvn compile exec:java`.

A következő: egy példa: Ha az alkalmazás futtatásához a Windows rendszeren

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

 A folytatás előtt ellenőrizze a két fájlt az alapértelmezett címtárban (a dokumentumok, a windows-felhasználók számára). Nyissa meg őket, és tekintse meg a megegyezik azzal. A konzolablakban kívül a BLOB URL-címét, majd illessze be a böngészőt, hogy a fájl tartalmát a Blob Storage tárolóban. Megnyomja az enter billentyűt, amikor törli a tárolót, és a fájlokat.

Például egy eszköz is használhatja a [Azure Tártallózó](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) megtekinthetik a fájlokat a Blob Storage tárolóban. Azure Tártallózó egy ingyenes platformfüggetlen eszköz, amely lehetővé teszi a tárfiók adatait. 

Miután ellenőrizte, hogy a fájlokat, nyomja le az enter billentyűt a bemutató befejeződését, és törölje a tesztelési fájlokat. Most, hogy ismeri a minta funkciója, nyissa meg a **AzureApp.java** fájlt nézze meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód ismertetése

Ezután azt végezze el a mintakódot, hogy megismerheti, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>A tárolási objektum mutató hivatkozások beolvasása

Az első lépés férhessen hozzá és felügyelhesse a Blob storage használt objektumok hivatkozásainak létrehozásához. Ezek az objektumok egymástól összeállítása – egyes használja a következő egy, a listában.

* Hozzon létre egy példányát a **CloudStorageAccount** mutató objektum a [tárfiók](/java/api/com.microsoft.azure.management.storage._storage_account).

    A **CloudStorageAccount** objektum a tárfiók a megjelenítése, és lehetővé teszi, és a tárfiók tulajdonságai programozott hozzáférés. Használja a **CloudStorageAccount** objektum egy példányát is létrehozhat a **CloudBlobClient**, a blob szolgáltatás eléréséhez szükséges.

* Hozzon létre egy példányát a **CloudBlobClient** objektum, amely mutat a [Blob szolgáltatás](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) tárfiókba.

    A **CloudBlobClient** a blob szolgáltatás számára, hogy lehetővé teszi a blob storage tulajdonságai programokon keresztül érhetők el, és hozzáférési pontot biztosít. Használja a **CloudBlobClient** létrehozhat egy példányát a **CloudBlobContainer** objektum, amely tárolók létrehozásához szükséges.

* Hozzon létre egy példányát a **CloudBlobContainer** objektum, amely képviseli az [tároló](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) fér hozzá. Tárolók használatával rendezheti a blobok, például a mappák használ a fájlok rendszerezéséhez a számítógépen.    

    Ha elvégezte a **CloudBlobContainer**, létrehozhat egy példányát a **CloudBlockBlob** mutat, az adott objektum [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) amely érdekli, a és egy feltöltés, letöltés, másolása, stb. a művelet végrehajtásához.

> [!IMPORTANT]
> A tároló nevének kisbetűnek kell lennie. Lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) tároló és a blob nevének további információt.

### <a name="create-a-container"></a>Tároló létrehozása 

Ebben a szakaszban az objektumok példányt létrehozni, hozzon létre egy új tárolót, és majd engedélyek beállítása a tárolón, a blobok nyilvánosak, és csak egy URL-cím elérhető. A tároló neve **quickstartblobs**. 

Ez a példa **CreateIfNotExists** mert szeretnénk hozzon létre egy új tároló minden alkalommal, amikor a minta futtatása. Éles környezetben, ahol az alkalmazás teljes tárolóhoz használ, hogy a rendszer jobb eljárás az, hogy csak akkor hívható **CreateIfNotExists** után. Azt is megteheti a tároló időben hozhat létre, így nem kell a kódban létrehozásához.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Blokkblobok a leggyakrabban használt, és amely a gyors üzembe helyezés használttól. 

Feltölteni a fájlt egy blobba, a blobra mutató hivatkozás beszerzése a cél-tárolóban. Miután a blobhivatkozást, az adatait feltöltheti azt a [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Ez a művelet a blob hoz létre, ha már nem létezik, vagy felülírja, ha már létezik.

A mintakód létrehoz egy helyi feltöltésének használható, és töltse le. a fájlt fel kell tölteni tárolni **forrás** és a blob neve **blob**. Az alábbi példa feltölt a fájlt a tároló neve **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Több [módszerek feltöltése](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) , hogy a Blob storage használata. Például ha egy karakterlánc, használhatja a fájlfeltöltési módszer, hanem a UploadText metódust. 

Blokkblobok állhat bármilyen szövegből vagy bináris fájl. Lapblobokat elsősorban az infrastruktúra-szolgáltatási virtuális gépek biztonsági használt VHD-fájlokat. Hozzáfűző blobok használt naplózást, például a kívánt fájlra és majd hozzáadni további információkat. A Blob storage-ban tárolt legtöbb objektum blokkblobokat.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Kaphat a fájlok a tároló használja listáját [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). A következő kódot a bináris objektumok listájának beolvasása, majd végighalad őket, az URI-azonosítók található blobok megjelenítő. Másolja az URI a parancsablakot, és illessze be a böngészőt, hogy a fájl.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Blobok letöltése a helyi lemezek használata [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

A következő kódot a blob feltöltése egy korábbi szakaszában "_DOWNLOADED" utótag hozzáadása a blob nevének, így mindkét fájlt a helyi lemezen tölti le. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a blobok a gyors üzembe helyezés feltöltött, törölje a teljes tárolóhoz történő [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Ez is törli a fájlokat a tárolóban.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés megtanulta, hogyan viheti át a fájlokat egy helyi lemezre és Azure Blob Storage tárolót használó Java között. További információt a Blob storage használata, továbbra is a Blob-tároló útmutató.

> [!div class="nextstepaction"]
> [A BLOB Storage műveletek útmutató](storage-java-how-to-use-blob-storage.md)

A Tártallózó alkalmazással és a Blobok kapcsolatos további információkért lásd: [kezelése Azure Blob storage-erőforrások a Tártallózó alkalmazással](../../vs-azure-tools-storage-explorer-blobs.md).
