---
title: 'Gyors útmutató: Azure Blob Storage ügyféloldali kódtár Java SDK V8-hoz'
description: Létre fog hozni egy tárfiókot és egy tárolót egy objektumtárban (Blobtárban). Ezután használja az Azure Storage ügyféloldali kódtárat a Java SDK V8-hoz a Blobok Azure Storage-ba való feltöltéséhez, egy blob letöltéséhez és a tárolóban lévő Blobok listázásához.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 404599ade960d43ef2cb86777fb41701ce96e11b
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609124"
---
# <a name="quickstart-azure-blob-storage-client-library-for-java-sdk-v8"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtár Java SDK V8-hoz

Ismerkedjen meg a Javához készült Azure Blob Storage ügyféloldali kódtáraval. Az Azure Blob Storage a Microsoft objektum-tárolási megoldás a felhőhöz. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

A Javához készült Azure Blob Storage ügyféloldali kódtár a következőhöz használható:

* Tároló létrehozása
* Engedélyek beállítása tárolón
* BLOB létrehozása az Azure Storage-ban
* A blob letöltése a helyi számítógépre
* Egy tároló összes blobjának listázása
* Tároló törlése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Maven-integrációt tartalmazó IDE.
* Másik lehetőségként telepítse és konfigurálja a Mavent a parancssorból való működésre.

Ez az útmutató az [Eclipse](https://www.eclipse.org/downloads/) -t használja az "Eclipse ide for Java Developers" konfigurációval.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A [minta alkalmazás](https://github.com/Azure-Samples/storage-blobs-java-quickstart) egy alapszintű konzolos alkalmazás.

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

A folytatás előtt ellenőrizze a mintafájlt az alapértelmezett könyvtárban (Windows-felhasználók esetén ez a *C:\Users\<felhasználó>\AppData\Local\Temp* könyvtár). Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a fájl tartalmának Blob-tárolóban való megtekintéséhez. Ha összehasonlítja a könyvtárban lévő mintafájlt és a Blob Storage-ben tárolt tartalmakat, azt tapasztalja, hogy ezek megegyeznek.

  >[!NOTE]
  >Az [Azure Storage Explorert](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat.

A fájlok ellenőrzése után nyomja le az **Enter** billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tudja, hogy működik a minta, nyissa meg az **AzureApp.java** fájlt, és tekintse meg a kódot.

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első teendő a referenciák létrehozása a Blob-tárolóhoz való hozzáféréshez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) objektum egy példányát, amely a tárfiókra mutat.

    A **CloudStorageAccount** objektum az Ön tárfiókját képviseli. Ezzel az objektummal állíthatja be és érheti el a programozási környezetből a tárfiók tulajdonságait. A **CloudStorageAccount** objektum használatával létrehozhatja a **CloudBlobClient** példányát, amely a Blob szolgáltatás eléréséhez szükséges.

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

Egy fájl egy blokk blobba való feltöltéséhez szerezzen be egy hivatkozást a blobra a tárolóban. Ha megszerezte a blobhivatkozást, adatokat a [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload) használatával tölthet fel rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

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

Ha már nincs szüksége a feltöltött blobokra, a teljes tárolót törölheti a [CloudBlobContainer. deleteifexists paranccsal](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists)használatával. Ez a metódus a tárolóban található fájlokat is törli.

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

Ebben a cikkben megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Java használatával. Ha bővebb információra van szüksége a Java használatával kapcsolatban, lépjen tovább a GitHub-forráskódadattárba.

> [!div class="nextstepaction"]
> [Java API-referenciák](https://docs.microsoft.com/java/api/overview/azure/storage?view=azure-java-legacy) a Java-alapú
> - [kódokhoz](../common/storage-samples-java.md)
