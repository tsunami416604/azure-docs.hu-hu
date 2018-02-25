---
title: "Azure Blob storage (object storage) a Java használatával |} Microsoft Docs"
description: "Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 4b243aa7a4e9aa3028259258292d1271867d28f4
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="how-to-use-blob-storage-from-java"></a>How to use Blob storage from Java (A Blob Storage használata Javával)
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Áttekintés
Az Azure Blob Storage egy olyan szolgáltatás, amely a strukturálatlan adatokat objektumként/blobként tárolja a felhőben. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez a cikk bemutatja, hogyan hajthat végre a szolgáltatást a Microsoft Azure Blob Storage tárolót használó általános forgatókönyvhöz. A mintákat a Java és -felhasználási nyelven íródtak a [Azure Storage SDK for Java][Azure Storage SDK for Java]. Az ismertetett forgatókönyvek **feltöltése**, **felsoroló**, **letöltése**, és **törlése** blobokat. A blobok további információkért lásd: a [lépések](#Next-Steps) szakasz.

> [!NOTE]
> Az SDK nem elérhető a fejlesztők számára, akik Azure Storage Android-eszközökön. További információkért lásd: a [Azure Storage SDK for Android][Azure Storage SDK for Android].
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
Ez a cikk tárolási szolgáltatásokkal, amelyek helyben, vagy a webes szerepkör vagy a feldolgozói szerepkör az Azure-ban futó belül Java-alkalmazások futtatása fogja használni.

Ehhez szüksége lesz a Java fejlesztői készlet (JDK) telepítése, és hozzon létre egy Azure Storage-fiókot az Azure-előfizetéshez. Ha ezzel végzett, akkor győződjön meg arról, hogy a fejlesztési rendszer megfelel-e a minimális követelményeit és függőségeit, amelyek szerepelnek a [Azure Storage SDK for Java] [ Azure Storage SDK for Java] GitHub tárházából. Ha a rendszer megfelel ezeknek a követelményeknek, akkor is kövesse letöltése és telepítése az Azure Storage szalagtárak Java ebből a rendszeren. Ezek a feladatok befejezése után lesz ebben a cikkben a példák használó Java-alkalmazás létrehozása.

## <a name="configure-your-application-to-access-blob-storage"></a>Állítsa be az alkalmazását, a Blob storage eléréséhez
Adja hozzá a következő importálási utasításokat a felső részén a Java-fájlt, amelyre az Azure Storage API-k használata a blobokhoz való hozzáférést.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure Storage kapcsolati karakterlánc beállítása
Egy Azure Storage-ügyfél egy tárolási kapcsolati karakterlánc végpontok és adatok szolgáltatások eléréséhez szükséges hitelesítő adatok tárolására használ. Ha egy ügyfél-alkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban a tárfiók nevével, és a tárfiók elsődleges elérési kulcs szerepel az [Azure-portálon](https://portal.azure.com) a a *AccountName* és *AccountKey* értékek. A következő példa bemutatja, hogyan deklarálhatnak ahhoz, hogy a kapcsolati karakterlánc statikus mezőben.

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Egy alkalmazás fut, a Microsoft Azure-ban a szerepkörön belüli, az ezt a karakterláncot tárolhatja a konfigurációs fájlban, *ServiceConfiguration.cscfg*, és hívja érhető el a **RoleEnvironment.getConfigurationSettings** metódust. Az alábbi példában a kapcsolati karakterláncot, lekérdezi a **beállítás** nevű elem *StorageConnectionString* a szolgáltatás konfigurációs fájljában.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

A következő minták azt feltételezik, hogy használt két módszer közül egyik beolvasni a tárolási kapcsolati karakterlánc.

## <a name="create-a-container"></a>Tároló létrehozása
A **CloudBlobClient** objektum lehetővé teszi, hogy a tárolók és blobok hivatkozási objektumok beolvasása. Az alábbi kód létrehoz egy **CloudBlobClient** objektum.

> [!NOTE]
> Nincsenek további módon hozhat létre **CloudStorageAccount** objektumok; további információkért lásd: **CloudStorageAccount** a a [Azure Storage ügyfél SDK-dokumentáció].
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Használja a **CloudBlobClient** hivatkozás a tárolóhoz használni kívánt objektumot. A tároló hozhat létre, ha az nem létezik a **createIfNotExists** metódus, amely más módon fog visszaadni a meglévő tárolóhoz. Alapértelmezés szerint az új tároló sajátja, ezért meg kell adnia a tárelérési kulcsát (úgy, ahogy korábban) a blobok letöltését az ebben a tárolóban.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>Választható lehetőség: A tárolók nyilvános hozzáférés konfigurálása
Egy tároló engedélyek privát hozzáférést alapértelmezés szerint vannak konfigurálva, de könnyen konfigurálható egy tároló engedélyt, hogy az összes felhasználó számára az interneten lévő nyilvános, csak olvasható hozzáférést:

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Fájl feltöltése a blob, beolvasni a tároló hivatkozását, és kérjen le egy blobhivatkozást segítségével. Miután egy blobhivatkozást, a feltöltési meghívásával beolvasott a blobhivatkozást bármilyen streamet feltölthet. Ez a művelet létrehozza a blobot, ha nem létezik, vagy felülírja, ellenkező esetben. A következő példakód ezt mutatja be, és feltételezi, hogy a tároló már létezik.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kapnak a tároló hivatkozását, mint amilyet egy blob feltöltése. Használhatja a tároló **listBlobs** metódust egy **a** hurok. A következő kódot a konzolba a tárolóban lévő minden egyes blob Uri kimenete.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Vegye figyelembe, hogy a blobok elérési út adatait a név lehet neve. Ez egy olyan virtuális könyvtárstruktúrát hoz létre, amelyet egy hagyományos fájlrendszerhez hasonlóan rendszerezhet és bejárhat. Ne feledje, hogy a könyvtárstruktúra csupán virtuális – a Blob Storage szolgáltatásban elérhető erőforrások kizárólag a tárolók és a blobok. Azonban az ügyféloldali kódtár kínál a **CloudBlobDirectory** objektum tekintse meg a virtuális könyvtárra, és így blobokkal munkavégzés folyamatainak egyszerűsítéséhez.

Lehet például a "photos", amelyben blobok "rootphoto1", "2010/photo1", "2010/photo2" nevű és "2011/photo1" lehet, hogy feltöltése nevű tárolót. Ez hozna létre a virtuális könyvtár "2010" és "2011" a "photos" tárolóban. A hívás esetén **listBlobs** a "photos" tárolóban, a gyűjtemény adott vissza fogja tartalmazni **CloudBlobDirectory** és **CloudBlob** objektumokból a könyvtárak és a legfelső szinten található blobokat. Ebben az esetben "2010" és "2011" könyvtárak, valamint fénykép "rootphoto1" vissza. Használhatja a **instanceof** operátor segítségével különböztetheti meg egymástól ezeket az objektumokat.

Ha szükséges, átviheti paramétereit, és a **listBlobs** metódust a **Listblobs** paraméter beállítása: igaz. Ennek eredményeképpen az összes blobjának ad vissza, függetlenül attól, könyvtár. További információkért lásd: **CloudBlobContainer.listBlobs** a a [Azure Storage ügyfél SDK-dokumentáció].

## <a name="download-a-blob"></a>Blob letöltése
Blobok letöltéséhez ugyanazokat a lépéseket egy blob feltöltése ahhoz, hogy kérjen le egy blobhivatkozást hasonló módon. A feltöltést példában feltöltése a blob-objektum neve. A következő példában át a blob tartalmát egy stream objektumra, mint a letöltési hívás egy **FileOutputStream** használható megőrizni a blobot egy helyi fájlba.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>Blob törlése
Törli a blob, a get egy blobhivatkozást, és a hívás **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>A blob-tároló törlése
Végül egy blob-tároló törléséhez lekérése blob tároló hivatkozását, és hívás **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Blob storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről.

* [Az Azure Storage Java SDK][Azure Storage SDK for Java]
* [Az Azure Storage ügyfél SDK-dokumentáció][Azure Storage ügyfél SDK-dokumentáció]
* [Az Azure Storage REST API-n][Azure Storage REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

További információ: [Azure Java-fejlesztőknek](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyfél SDK-dokumentáció]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
