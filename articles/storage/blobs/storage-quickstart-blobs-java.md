---
title: Blob létrehozása az Azure Storage-ban a Java 7-es verzióját az ügyféloldali kódtár használatával |} A Microsoft Docs
description: Létre fog hozni egy tárfiókot és egy tárolót egy objektumtárban (Blobtárban). Ezután használja az Azure Storage ügyféloldali kódtára a Java 7-es verziója feltölt egy blobot az Azure Storage, letölt egy blobot, és a tárolóban lévő blobok listázása.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: rogarana
ms.openlocfilehash: 3bf82f37752009a488512d720093bc9c595dff8e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753214"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-java-v7"></a>Hogyan feltöltése, letöltése és a Java 7-es verzióját az ügyféloldali kódtár használatával blobok listázása

Ez az útmutató hogyan használható az ügyféloldali kódtárral való feltöltése, letöltése és az Azure Blob storage tárolóban lévő listában a blokkblobok használatát támogatják a Java 7-es elsajátíthatja.

> [!TIP]
> A Javához készült Azure Storage ügyféloldali kódtár legújabb verzióját a v10. A Microsoft azt javasolja, hogy ha lehetséges, az ügyféloldali kódtár legújabb verzióját használja-e. Használatának megkezdéséhez v10, lásd: [a rövid útmutató: Feltöltése, letöltése és blobok listázása a Java Storage SDK V10 használatával](storage-quickstart-blobs-java-v10.md).

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az Azure storage-fiókot is létrehozhat a [az Azure portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). A fiók létrehozásával kapcsolatos útmutatóért lásd a [tárfiók létrehozását](../common/storage-quickstart-create-account.md) ismertető szakaszt.

Győződjön meg arról, hogy a következő előfeltételek vonatkoznak:

* Telepítsen egy Maven-integrációval rendelkező IDE-t.

* Másik lehetőségként telepítse és konfigurálja a Mavent a parancssorból való működésre.

Ez az útmutató használ [Eclipse](http://www.eclipse.org/downloads/) az "Eclipse IDE for Java Developers" konfigurációval.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-java-quickstart) egy egyszerű Konzolalkalmazás.  

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A projekt megnyitásához indítsa el az Eclipse-t, és zárja be az üdvözlőképernyőt. Kattintson a **File** (Fájl), majd az **Open Projects from File System** (Projekt megnyitása fájlrendszerből) lehetőségre. Ügyeljen arra, hogy a **Detect and configure project natures** (Projektek természetének észlelése és konfigurálása) lehetőség be legyen jelölve. Válassza a **Directory** (Könyvtár) lehetőséget, majd lépjen oda, ahová a klónozott adattárat mentette. A klónozott adattárban válassza a **blobAzureApp** mappát. Győződjön meg arról, hogy a **blobAzureApp** projekt megjelenik Eclipse-projektként, majd válassza a **Finish** (Befejezés) lehetőséget.

A projekt importálásának befejezése után nyissa meg az **AzureApp.java** fájlt (a **src/main/java** mappa **blobQuickstart.blobAzureApp** mappájában), és cserélje le az `accountname` és az `accountkey` értékeket a `storageConnectionString` sztringen belül. Ezután futtassa az alkalmazást. Az ezen feladatok elvégzésére vonatkozó utasítások leírását a következő szakaszok tartalmazzák.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása
    
Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. Nyissa meg az **AzureApp.Java** fájlt. Keresse meg a `storageConnectionString` változót, és illessze be az előző szakaszban másolt kapcsolati sztring értéket. A `storageConnectionString` változó az alábbi példakódhoz hasonlóan néz ki:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a mintaalkalmazás létrehoz egy tesztfájlt az alapértelmezett könyvtárban (Windows-felhasználók esetén ez a *C:\Users\<felhasználó>\AppData\Local\Temp* könyvtár), feltölti a Blob Storage-ba, listázza a tárolóban található blobokat, majd letölti a fájlt új néven, hogy össze lehessen hasonlítani a régebbi fájllal. 

A minta futtatásához használja a Mavent a parancssorban. Nyisson meg egy rendszerhéjat, és navigáljon a **blobAzureApp** elemre a klónozott könyvtárában. Ezután írja be az `mvn compile exec:java` parancsot. 

A következő példában látható kimenet az alkalmazás Windows rendszeren történő futtatásának eredményét mutatja.

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

A folytatás előtt ellenőrizze a mintafájlt az alapértelmezett könyvtárban (Windows-felhasználók esetén ez a *C:\Users\<felhasználó>\AppData\Local\Temp* könyvtár). Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a fájl tartalmának blobtárolóban való megtekintéséhez. Ha összehasonlítja a könyvtárban lévő mintafájlt és a Blob Storage-ben tárolt tartalmakat, azt tapasztalja, hogy ezek megegyeznek. 

  >[!NOTE]
  >Az [Azure Storage Explorert](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat.

A fájlok ellenőrzése után nyomja le az **Enter** billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tudja, hogy működik a minta, nyissa meg az **AzureApp.java** fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első lépésben létre kell hozni a referenciákat azokhoz az objektumokhoz, melyeket a blobtároló eléréséhez és kezeléséhez használ. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) objektum egy példányát, amely a tárfiókra mutat.

    A **CloudStorageAccount** objektum az Ön tárfiókját képviseli. Ezzel az objektummal állíthatja be és érheti el a programozási környezetből a tárfiók tulajdonságait. A **CloudStorageAccount** objektum használatával létrehozhatja a **CloudBlobClient** példányát, amely a blobszolgáltatás eléréséhez szükséges.

* Hozza létre a **CloudBlobClient** objektum példányát, amely a tárfiókja [Blob szolgáltatására](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) mutat.

    A **CloudBlobClient** hozzáférési lehetőséget biztosít a Blob szolgáltatáshoz, amellyel a programozási környezetből állíthatja be és érheti el a Blob Storage tulajdonságait. A **CloudBlobClient** objektum használatával létrehozhatja a **CloudBlobContainer** példányát, amely a tárolók létrehozásához szükséges.

* Hozza létre a [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) objektum egy példányát. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A blobok a tárolókkal rendszerezhetők, hasonlóan ahhoz, ahogyan a számítógépen mappákba rendszerezi a fájlokat.    

    A **CloudBlobContainer** létrehozása után létrehozhatja a [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) objektum egy példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolókkal kapcsolatos további információkért tekintse meg a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával foglalkozó cikket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Tároló létrehozása

Ebben a szakaszban létre fogja hozni az objektumok egy-egy példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak és mindössze egy URL-cím alapján elérhetőek legyenek. A tároló neve **quickstartcontainer**. 

Ez a példa a [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists) metódust használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Éles környezetben, ahol ugyanazt a tárolót használja egy alkalmazásban, jobb eljárás a **CreateIfNotExists** egyszeri hívása. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

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

Feltölthet egy fájlt egy blokkblobba, kérje le egy hivatkozást a blob céltárolón. Ha megszerezte a blobhivatkozást, adatokat a [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload) használatával tölthet fel rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja, hogy az **source** néven és a blob nevével elnevezve legyen feltöltve a **blob** helyre. A következő példa feltölti a fájlt a **quickstartcontainer** nevű tárolóba.

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

A Blob Storage-hoz többféle `upload` metódus használható, ilyen például az [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), az [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), az [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), az [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) és az [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext). Ha például sztringje van, használhatja az `UploadText` metódust az `Upload` helyett. 

A blokkblobok akármilyen típusú szöveges vagy bináris fájlok lehetnek. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használhatók. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban található fájlok listáját a [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs) paranccsal szerezheti be. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a talált blobok URI-ját. Az URI-t kimásolhatja a parancsablakból, és a fájl megtekintéséhez beillesztheti egy böngészőbe.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Blobokat a helyi lemezre a [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile) metódussal tölthet le.

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

Ha már nincs szüksége a feltöltött blobok, akkor a teljes tárolót törölheti [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists). Ez a metódus a tárolóban található fájlokat is törli.

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan vihetők át fájlok egy helyi lemez és a Java használatával Azure Blob storage között. Ha bővebb információra van szüksége a Java használatával kapcsolatban, lépjen tovább a GitHub-forráskódadattárba.

> [!div class="nextstepaction"]
> [A Microsoft Azure Storage SDK for Java v10](https://github.com/azure/azure-storage-java) 
> [Java API-referencia](https://docs.microsoft.com/java/azure/)
> [Java-Kódminták](../common/storage-samples-java.md)
