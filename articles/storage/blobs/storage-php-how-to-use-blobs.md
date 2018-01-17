---
title: "A blob storage (object storage) php-ből használata |} Microsoft Docs"
description: "Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
documentationcenter: php
services: storage
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 1af56b59-b3f0-4b46-8441-aab463ae088e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: tamram
ms.openlocfilehash: 6bc7fd799eddca14e728f965601acd244d88870c
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-use-blob-storage-from-php"></a>Php-ből a blob storage használata
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Az Azure Blob Storage egy olyan szolgáltatás, amely a strukturálatlan adatokat objektumként/blobként tárolja a felhőben. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást az Azure blob szolgáltatást használó általános forgatókönyvhöz. A mintákat a PHP és -felhasználási nyelven íródtak a [Azure Storage Blob ügyféloldali kódtára a PHP][download]. Az ismertetett forgatókönyvek **feltöltése**, **felsoroló**, **letöltése**, és **törlése** blobokat. A blobok további információkért lásd: a [további lépések](#next-steps) szakasz.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
Egyetlen követelménye a PHP-alkalmazások létrehozása, amely hozzáfér az Azure blob szolgáltatáshoz nem található osztályok hivatkozik a [Azure Storage ügyféloldali kódtára a PHP] [ download] a a kód. A fejlesztői eszközök hozhat létre az alkalmazás, például a Jegyzettömbbel.

Ebben az útmutatóban a Blob storage szolgáltatásainak, helyben vagy egy Azure webes szerepkörről, a feldolgozói szerepkör vagy a webhely belül futó PHP-alkalmazások belül néven használja.

## <a name="get-the-azure-client-libraries"></a>Az Azure Ügyfélkódtárai beolvasása
### <a name="install-via-composer"></a>Keresztül szerkesztő telepítése
1. Hozzon létre egy fájlt **composer.json** a projekt gyökérkönyvtárában, és az alábbi kód felvétele:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-blob": "*"
      }
    }
    ```
2. Töltse le  **[composer.phar] [ composer-phar]**  a projekt gyökérkönyvtárában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökérkönyvtárában
   
    ```
    php composer.phar install
    ```

Másik lehetőségként keresse fel a [Azure Storage PHP ügyféloldali kódtár] [ download] a Githubon a forráskód klónozását.

## <a name="configure-your-application-to-access-the-blob-service"></a>Állítsa be az alkalmazását, a blob szolgáltatás eléréséhez
Az Azure blob szolgáltatás API-kat használ, meg kell:

1. A robotot fájl használatával hivatkozik a [require_once] utasítást, és
2. Bármely osztályok segítségével lehet hivatkozni.

A következő példa bemutatja, hogyan a robotot fájl és a hivatkozás a **BlobRestProxy** osztály.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Blob\BlobRestProxy;
```

Az alábbi példákban a `require_once` utasítás mindig megjelenik, de csak a szükséges végrehajtandó például osztályok hivatkozott.

## <a name="set-up-an-azure-storage-connection"></a>Az Azure storage-kapcsolat beállítása
Egy Azure blob szolgáltatásügyfél példányt létrehozni, először egy érvényes kapcsolati karakterláncot kell rendelkeznie. A blob szolgáltatás kapcsolati karakterláncának formátuma:

Élő szolgáltatások elérésére:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

A storage emulator eléréséhez:

```php
UseDevelopmentStorage=true
```

Egy Azure Blob szolgáltatásügyfél létrehozásához kell használnia a **BlobRestProxy** osztály. A következőket teheti:

* a kapcsolati karakterláncot adja át a közvetlenül vagy
* A webalkalmazás környezeti változók segítségével tárolja a kapcsolati karakterláncot. Lásd: [Azure webalkalmazás-konfigurációs beállítások](../../app-service/web-sites-configure.md) dokumentumában kapcsolati karakterláncok konfigurálása.

Az itt leírt példák a kapcsolati karakterlánc közvetlenül át.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

$blobClient = BlobRestProxy::createBlobService($connectionString);
```

## <a name="create-a-container"></a>Tároló létrehozása
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

A **BlobRestProxy** objektum lehetővé teszi, hogy a blob tárolókat hozhat létre a **createContainer** metódust. A tároló létrehozásakor a tárolóra vonatkozó beállítások adhatók meg, de ez így nincs szükség. (A következő példa bemutatja, hogyan állíthatja be a tároló hozzáférés-vezérlési lista (ACL) és a tároló metaadatait.)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob client.
$blobClient = BlobRestProxy::createBlobService($connectionString);


// OPTIONAL: Set public access policy and metadata.
// Create container options object.
$createContainerOptions = new CreateContainerOptions();

// Set public access policy. Possible values are
// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
// CONTAINER_AND_BLOBS:
// Specifies full public read access for container and blob data.
// proxys can enumerate blobs within the container via anonymous
// request, but cannot enumerate containers within the storage account.
//
// BLOBS_ONLY:
// Specifies public read access for blobs. Blob data within this
// container can be read via anonymous request, but container data is not
// available. proxys cannot enumerate blobs within the container via
// anonymous request.
// If this value is not specified in the request, container data is
// private to the account owner.
$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

// Set container metadata.
$createContainerOptions->addMetaData("key1", "value1");
$createContainerOptions->addMetaData("key2", "value2");

try    {
    // Create container.
    $blobClient->createContainer("mycontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Hívása **setPublicAccess (PublicAccessType::CONTAINER\_és\_BLOBOK)** elérhetővé válnak a tároló és a blob adatainak elérése névtelen kérésekkel. Hívása **setPublicAccess(PublicAccessType::BLOBS_ONLY)** teszi csak blob-adatok elérése névtelen kérésekkel érhető el. Tároló hozzáférés-vezérlési listák kapcsolatos további információkért lásd: [Set tároló hozzáférés-vezérlési lista (REST API-t)][container-acl].

Blob szolgáltatás hibakódokkal kapcsolatban további információkért lásd: [Blob hibakódjai][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Feltölteni a fájlt egy blobba, használja a **BlobRestProxy -> createBlockBlob** metódust. Ez a művelet a blob hoz létre, ha nem létezik, vagy felülírja, ha támogatja. Az alábbi Kódpélda feltételezi, hogy a tároló már létre lett hozva, és használja [fopen] [ fopen] adatfolyamként fájl megnyitásához.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

$content = fopen("c:\myfile.txt", "r");
$blob_name = "myblob";

try    {
    //Upload blob
    $blobClient->createBlockBlob("mycontainer", $blob_name, $content);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Vegye figyelembe, hogy a fenti példában feltölt egy blobot adatfolyamként. Azonban egy blob is feltölthetők a karakterlánc használja, mint például a [fájl\_beolvasása\_tartalma] [ file_get_contents] függvény. Ehhez használja a fenti példában, módosítsa `$content = fopen("c:\myfile.txt", "r");` való `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához, használja a **BlobRestProxy -> listBlobs** metódust egy **foreach** hurok a hurok a eredményén keresztül. A következő kódot neve minden egyes blob a tárolóban lévő kimenetként és jeleníti meg az URI a böngészőre.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // List blobs.
    $blob_list = $blobClient->listBlobs("mycontainer");
    $blobs = $blob_list->getBlobs();

    foreach($blobs as $blob)
    {
        echo $blob->getName().": ".$blob->getUrl()."<br />";
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="download-a-blob"></a>Blob letöltése
Töltse le a blob, hívja meg a **BlobRestProxy getBlob ->** módszer, majd hívja a **getContentStream** metódust a kapott **GetBlobResult** objektum.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);


try    {
    // Get blob.
    $blob = $blobClient->getBlob("mycontainer", "myblob");
    fpassthru($blob->getContentStream());
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Vegye figyelembe, hogy a fenti példa blob lekérdezi egy adatfolyam-erőforrásként (alapértelmezés). Azonban használhatja a [adatfolyam\_beolvasása\_tartalma] [ stream-get-contents] működnek, mint a visszaadott stream alakítható át karakterlánccá.

## <a name="delete-a-blob"></a>Blob törlése
Blobok törléséhez, adja át a tároló és a blob neve **BlobRestProxy -> deleteBlob**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // Delete blob.
    $blobClient->deleteBlob("mycontainer", "myblob");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-a-blob-container"></a>A blob-tároló törlése
Végül egy blob-tároló törlése, a tároló nevét át **BlobRestProxy -> deleteContainer**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // Delete container.
    $blobClient->deleteContainer("mycontainer");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure blob szolgáltatás alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről.

* Látogasson el a [Azure Storage PHP ügyféloldali kódtár API-referencia](http://azure.github.io/azure-storage-php/)
* Tekintse meg a [speciális Blob példa](https://github.com/Azure/azure-storage-php/blob/master/samples/BlobSamples.php).

[download]: https://github.com/Azure/azure-storage-php
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
