---
title: Azure rövid útmutató – Blob létrehozása objektumtárban a Java Storage SDK V10 használatával | Microsoft Docs
description: Ebben a rövid útmutatóban létrehoz egy tárolót egy objektumtárban (Blob Storage-fiókban), feltölt egy fájlt, objektumokat listáz ki, valamint letöltést végez a Java Storage SDK használatával.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704470"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>Rövid útmutató: Blobok feltöltése, letöltése és listázása a Java SDK V10 (előzetes verzió) használatával

A rövid útmutató azt ismerteti, hogyan használható az új Java Storage SDK blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ben található tárolóban. Az új Java SDK a reaktív programozási modell és az RxJava segítségével biztosít aszinkron műveleteket. Az RxJava bővebb ismertetését [itt](https://github.com/ReactiveX/RxJava) találja. 

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* A [Maven](http://maven.apache.org/download.cgi) telepítése és konfigurálása a parancssorból, vagy bármely szabadon választott Java IDE-ből való működésre
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) telepítése és konfigurálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A gyors útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) egy egyszerű konzolalkalmazás. 

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat.

A projekt importálásának befejezése után nyissa meg a **Qucikstart.java** fájlt (a **src/main/java/quickstart** mappában).

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása
Ehhez a megoldáshoz a tárfióknevét és -kulcsát biztonságosan legyen tárolnia helyileg, a mintakódot futtató gép környezeti változóiban. A környezeti változók létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

### <a name="for-linux"></a>Linux esetén

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>Windows esetén

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta létrehoz egy tesztfájlt az alapértelmezett könyvtárban (Windows esetén az AppData\Local\Temp mappában), majd felkéri a felhasználót, hogy töltse fel a tesztfájlt a Blob Storage-tárolóba, kilistázza a tárolóban található blobokat, és töltse le a feltöltött fájlt egy új néven, hogy össze lehessen hasonlítani a régebbi fájllal. 

Ha a Maven használatával szeretné futtatni a mintát a parancssorban, nyisson meg egy rendszerhéjat, és navigáljon a **storage-blobs-java-v10-quickstart** elemre a klónozott könyvtárában. Ezután írja be az `mvn compile exec:java` parancsot.

A következő példában látható kimenet a Windows rendszeren végzett futtatás eredményét mutatja.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

A mintát a felhasználó vezérli, így parancsokat kell megadnia a kód végrehajtásához. Vegye figyelembe, hogy a bemenet különbséget tesz a kis- és nagybetűk között.

Az [Azure Storage Explorert](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le az **E**, majd az Enter billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg a **Quickstart.java** fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük annak működését.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első lépésben létre kell hozni a referenciákat azokhoz az objektumokhoz, melyeket a blobtároló eléréséhez és kezeléséhez használ. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a StorageURL objektum egy példányát, amely a tárfiókra mutat.

    A [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) objektum az Ön tárfiókját jelöli, és lehetővé teszi egy új folyamat létrehozását. A folyamat ([HTTP-folyamat](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)) egy olyan szabályzatkészlet, amely kérések és válaszok manipulálásához használható, és engedélyezési, naplózási és újrapróbálkozási mechanizmusokat tartalmaz. A folyamattal létrehozható a [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview) objektum egy példánya, amely lehetővé teszi egy [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview)-példány létrehozását, amely pedig a blobtárolókon a műveletek futtatásához szükséges.

* Hozza létre a ContainerURL objektum egy példányát. Ez azt a tárolót jelöli, amelyhez Ön hozzá szeretne férni. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

    A **ContainerURL** hozzáférési pontot biztosít a tárolószolgáltatáshoz. A **ContainerURL** használatával létrehozhatja a [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview) objektum egy példányát, amely a blobok létrehozásához szükséges.

* Hozza létre a [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview) objektum egy példányát, amely arra a konkrét blobra mutat, amely Önt érdekli.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

### <a name="create-a-container"></a>Tároló létrehozása 

Ebben a szakaszban létrehozza a ContainerURL egy példányát, és azzal egy új tárolót. A mintabeli tároló neve **quickstart**. 

Ez a példa a [containerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview) metódust használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a gyorsútmutató is ezeket használja. 

Egy fájl adott blobba való feltöltéséhez szerezze be a blob céltárolón belüli hivatkozását. Ha megszerezte a blobhivatkozást, feltölthet rá egy fájlt egy alacsony szintű API-val, amilyen az [Upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview) (más néven PutBlob) vagy a [StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_) (más néven PutBLock) a BlockBlobURL-példányban, vagy a [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview) osztály által biztosított magas szintű API-kkal, mint például a [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview) metódus. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja, hogy az a **sourceFile** néven és a blob URL-címével elnevezve legyen feltöltve a **blob** helyre. A következő példa feltölti a fájlt a **quickstart** nevű tárolóba.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

A blokkblobok akármilyen típusú szöveges vagy bináris fájlok lehetnek. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használatosak. A hozzáfűző blobok az adatok utólagos hozzáfűzésére szolgálnak, és általában naplózási célra használatosak. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban található objektumok listája a [containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview) paranccsal kérhető le. Ez a metódus egyszerre legfeljebb 5000 objektumot ad vissza, valamint egy folytatási jelölőt (következő jelölőt), ha további listázható elemek is vannak még a tárolóban. Ehhez létrehozunk egy segítő függvényt, amely ismételten meghívja önmagát egészen addig, ameddig az előző listBlobsFlatSegment-válasz tartalmaz egy következő jelölőt.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Blobok letöltése

Blobokat a helyi lemezre a [blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview) metódussal tölthet le.

A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot, és hozzáadja a „_DOWNLOADED” (Letöltve) utótagot a nevéhez, így mindkét fájlt láthatja majd a helyi lemezen. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview) paranccsal. Ez a metódus a tárolóban található fájlokat is törli.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>További lépések

Ennek a gyorsútmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Java használatával. 

> [!div class="nextstepaction"]
> [A Java Storage SDK V10 forráskódja](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [API-referencia](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [Az RxJava ismertetése](https://github.com/ReactiveX/RxJava)