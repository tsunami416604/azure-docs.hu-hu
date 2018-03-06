---
title: "Azure rövid útmutató – Blobok feltöltése, letöltése és listázása az Azure Storage-ban a Java használatával | Microsoft Docs"
description: "Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre. Majd a Storage Javához készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat."
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: rogarana
ms.openlocfilehash: cde366e75e4111a911be67795a2ad4dfa73778ea
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-java"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a Java használatával

A gyorsútmutató azt ismerteti, hogyan használható a Java blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ben található tárolóban.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* Maven-integrációval rendelkező IDE telepítése

* Másik lehetőségként telepítse és konfigurálja a Mavent a parancssorból való működéshez

Ez az útmutató az [Eclipse-et](http://www.eclipse.org/downloads/) használja az „Eclipse IDE for Java Developers” konfigurációval.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A gyorsútmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-java-quickstart) egy egyszerű konzolalkalmazás. 

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A projekt megnyitásához nyissa meg az Eclipse-et, és zárja be az üdvözlőképernyőt. Kattintson a **File** (Fájl), majd az **Open Projects from File System...** (Projekt megnyitása fájlrendszerből) lehetőségre. Ügyeljen arra, hogy a **Detect and configure project natures** (Projektek természetének észlelése és konfigurálása) lehetőség be legyen jelölve. Válassza a **Directory** (Könyvtár) lehetőséget, lépjen oda, ahová a klónozott adattárat mentette, és ezen belül válassza ki a **javaBlobsQuickstart** mappát. Győződjön meg arról, hogy a **javaBlobsQuickstarts** projekt megjelenik Eclipse-projektként, majd válassza a **Finish** (Befejezés) lehetőséget.

A projekt importálásának befejezése után nyissa meg az **AzureApp.java** fájlt (a **src/main/java** mappa **blobQuickstart.blobAzureApp** mappájában), és cserélje le az `accountname` és az `accountkey` értékeket a `storageConnectionString` karakterláncon belül. Ezután futtassa az alkalmazást.
     

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása
    
Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Nyissa meg az **AzureApp.Java** fájlt. Keresse meg a `storageConnectionString` változót. Cserélje le az `AccountName` és az `AccountKey` értékeket a kapcsolati karakterlánc azon értékeire, amelyeket az Azure Portalról mentett. A `storageConnectionString` ekkor a következőképpen néz ki:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta létrehoz egy tesztfájlt az alapértelmezett könyvtárban (Windows-felhasználók esetén ez a Dokumentumok mappa), feltölti a blobtárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt új néven, hogy össze lehessen hasonlítani a régebbi fájllal. 

Futtassa a mintát az Eclipse-ben a **Ctrl+F11** billentyűkombináció lenyomásával.

Ha a Maven használatával szeretné futtatni a mintát a parancssorban, nyisson meg egy rendszerhéjat, és navigáljon a **blobAzureApp** elemre a klónozott könyvtárában. Ezután írja be az `mvn compile exec:java` parancsot.

A következő példában látható kimenet a Windows rendszeren végzett futtatás eredményét mutatja.

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

 A folytatás előtt ellenőrizze a két fájlt az alapértelmezett mappában (Windows-felhasználók esetén ez a Dokumentumok mappa). Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a fájl tartalmának blobtárolóban való megtekintéséhez. Az Enter billentyű lenyomása után a program törli a Storage tárolót és a fájlokat.

Az [Azure Storage Explorert](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le az Enter billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tudja, hogy működik a minta, nyissa meg az **AzureApp.java** fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első lépésben létre kell hozni a referenciákat azokhoz az objektumokhoz, melyeket a blobtároló eléréséhez és kezeléséhez használ. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account) objektum egy példányát, amely a tárfiókra mutat.

    A **CloudStorageAccount** objektum az Ön tárfiókját képviseli. Ezzel az objektummal állíthatja be és érheti el a programozási környezetből a tárfiók tulajdonságait. A **CloudStorageAccount** objektum használatával létrehozhatja a **CloudBlobClient** példányát, amely a blobszolgáltatás eléréséhez szükséges.

* Hozza létre a **CloudBlobClient** objektum példányát, amely a tárfiókja [Blob szolgáltatására](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) mutat.

    A **CloudBlobClient** hozzáférési pontot biztosít a Blob szolgáltatáshoz, amellyel a programozási környezetből állíthatja be és érheti el a tárfiók tulajdonságait. A **CloudBlobClient** objektum használatával létrehozhatja a **CloudBlobContainer** példányát, amely a tárolók létrehozásához szükséges.

* Hozza létre a [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) objektum egy példányát. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.    

    A **CloudBlobContainer** létrehozása után létrehozhatja a [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) objektum egy példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

### <a name="create-a-container"></a>Tároló létrehozása 

Ebben a szakaszban létre fogja hozni az objektumok egy-egy példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak és mindössze egy URL-cím alapján elérhetőek legyenek. A tároló neve **quickstartblobs**. 

Ez a példa a [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists) metódust használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Éles környezetben, ahol mindig ugyanazt a tárolót használja egy alkalmazásban, célravezetőbb megoldás, ha csak egyszer hívja meg a **CreateIfNotExists** metódust. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

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

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a gyorsútmutató is ezeket használja. 

Egy fájl adott blobba való feltöltéséhez szerezze be a blob céltárolón belüli hivatkozását. Ha megszerezte a blobhivatkozást, adatokat a [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long) használatával tölthet fel rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja el, hogy az a **source** néven és a blob nevével elnevezve legyen feltöltve a **blob** helyre. A következő példa feltölti a fájlt a **quickstartblobs** nevű tárolóba.

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

A Blob-tárolóval többféle feltöltési módszer használható, ilyen például az [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), az [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), az [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), az [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) és az [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext). Például karakterlánchoz használhatja az UploadText metódust is az Upload helyett. 

A blokkblobok akármilyen típusú szöveges vagy bináris fájlok lehetnek. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használatosak. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban található fájlok listáját a [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs) paranccsal szerezheti be. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a talált blobok URI-ját. Az URI-t kimásolhatja a parancsablakból, és a fájl megtekintéséhez beillesztheti egy böngészőbe.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Blobokat a helyi lemezre a [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String) metódussal tölthet le.

A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot, és hozzáadja a „_DOWNLOADED” (Letöltve) utótagot a nevéhez, így mindkét fájlt láthatja majd a helyi lemezen. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a gyorsútmutatóban feltöltött blobokra, a teljes tárolót törölheti a [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists) paranccsal. Ez a tárolóban található fájlokat is törli.

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

Ennek a gyorsútmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Java használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-java-how-to-use-blob-storage.md)

További információk a Storage Explorerről és a blobokról: [Azure Blob Storage-erőforrások kezelése a Storage Explorer használatával](../../vs-azure-tools-storage-explorer-blobs.md).

További Java-mintákért tekintse meg a [Javát használó Azure Storage-mintákkal foglalkozó szakaszt](../common/storage-samples-java.md).