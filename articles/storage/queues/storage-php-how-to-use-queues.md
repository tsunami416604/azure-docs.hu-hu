---
title: Php-ből a Queue storage használata |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Queue storage szolgáltatás létrehozása és törlése, üzenetsorok, és helyezze, beolvasása, és törli az üzenetet. Minta PHP nyelven íródtak.
documentationcenter: php
services: storage
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.openlocfilehash: 737054aefa684b3fc486c860253c69afbb6eaf0c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295806"
---
# <a name="how-to-use-queue-storage-from-php"></a>How to use Queue storage from PHP (A Queue Storage használata PHP-val)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyvek az Azure Queue storage szolgáltatás használatával. A minták írt származó keresztül a [Azure Storage ügyféloldali kódtára a PHP][download]. A kezelt forgatókönyvek között megtalálható a beszúrásához, Bepillantás, lekérését, és üzenetsorbeli üzenetek, törlése, valamint a létrehozása és üzenetsorok törlése.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
A csak követelmény a PHP-alkalmazás létrehozása, amely hozzáfér az Azure Queue storage, az osztályok a hivatkozó a [Azure Storage ügyféloldali kódtára a PHP] [ download] származó a kódon belül. Az alkalmazás létrehozásához bármilyen fejlesztői eszközt (pl. Jegyzettömböt) használhat.

Ez az útmutató a Queue storage szolgáltatásainak, amely nem hívható meg egy PHP-alkalmazás helyi, illetve egy Azure webes szerepkör, feldolgozói szerepkör vagy webhelyén belül futó kód használhatja.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél-kódtárak beszerzése
### <a name="install-via-composer"></a>Composer-n keresztül telepítése
1. Hozzon létre egy fájlt **composer.json** a projekt gyökérkönyvtárában található és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Töltse le **[composer.phar] [ composer-phar]** a projekt gyökérkönyvtárában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökérkönyvtárában
   
    ```
    php composer.phar install
    ```

Másik lehetőségként lépjen a [Azure Storage PHP ügyféloldali kódtár] [ download] klónozásához a forráskód a githubon.

## <a name="configure-your-application-to-access-queue-storage"></a>A Queue storage eléréséhez az alkalmazás konfigurálása
Az Azure Queue storage, az API-k használatához kell tennie:

1. Hivatkozzon a szalaghibák használatával a [require_once] utasítást.
2. Bármely osztályokat, amelyekre szüksége lehet hivatkozni.

Az alábbi példa bemutatja, hogyan foglalhat bele a szalaghibák fájlt, és hivatkozás a **QueueRestProxy** osztály.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

A következő példákban a `require_once` utasítás mindig látható, de csak az a példában végrehajtásához szükséges osztályok hivatkozott.

## <a name="set-up-an-azure-storage-connection"></a>Az Azure storage-kapcsolat beállítása
Hozza létre az Azure Queue storage-kliens, először szüksége van egy érvényes kapcsolati karakterláncot. A queue szolgáltatás kapcsolati karakterláncának formátuma a következő.

Egy élő szolgáltatás eléréséhez:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Az emulator tárolóinak eléréséhez:

```php
UseDevelopmentStorage=true
```

Az Azure Queue szolgáltatásügyfél létrehozásának, kell használnia a **QueueRestProxy** osztály. Az alábbi módszerek bármelyikét használhatja:

* Adja meg a kapcsolati karakterlánc közvetlenül azt.
* A webalkalmazásban a környezeti változók használatával tárolja a kapcsolati karakterláncot. Lásd: [Azure webalkalmazás-konfigurációs beállítások](../../app-service/web-sites-configure.md) dokumentum kapcsolati karakterláncok konfigurálása.
Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **QueueRestProxy** objektum lehetővé teszi a várólista létrehozása a **createQueue** metódust. Egy üzenetsor létrehozásakor az üzenetsor beállításokat is megadhat, de ez így nem kötelező. (Az alábbi példa bemutatja, hogyan metaadatok beállítása az üzenetsorba.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Kis-és nagybetűk metaadatok kulcsok nem támaszkodhat. Az összes kulcs olvasható kisbetűs a szolgáltatásból.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Adjon meg egy üzenetet egy üzenetsorba
Vegyen fel egy üzenetet egy üzenetsorba, használja a **QueueRestProxy -> createMessage**. A metódus szükséges, az üzenetsor neve, az üzenet szövege és üzenet-beállításokat (nem kötelező).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe
Is bepillanthat, egy üzenet (vagy üzenetek) egy üzenetsor elején anélkül, hogy eltávolítaná az üzenetsorból meghívásával **QueueRestProxy -> peekMessages**. Alapértelmezés szerint a **peekMessage** metódus adja vissza egy üzenet, de az érték használatával módosíthatja a **PeekMessagesOptions -> setNumberOfMessages** metódust.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>A következő üzenet kivétele az üzenetsorból
A kód két lépésben egy üzenetsorból eltávolítja az üzenetet. Először meghívja **QueueRestProxy -> listMessages**, ami lehetővé teszi az üzenetet az üzenetsorból olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. (Ha ez az időtartam az üzenet nem törlődik, azt láthatóvá válik az üzenetsor újra.) A befejezéshez, az üzenet eltávolítása az üzenetsorból, meg kell hívni **QueueRestProxy -> deleteMessage**. Az üzenet eltávolítása a két lépésből álló folyamat biztosítja, hogy a kód nem tudja feldolgozni egy üzenetet, mert hardveres vagy szoftveres hiba, amikor a kód egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálkozzon újra. A kód meghívja **deleteMessage** jobb gombbal az üzenet feldolgozása után.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása
Módosíthatja a tartalmát egy üzenet helyben, az üzenetsor meghívásával **QueueRestProxy -> updateMessage**. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenet az új tartalommal, és beállítja a láthatósági időkorlátot további 60 másodperccel bővíti. Ez az üzenet társított munkahelyi állapotát menti, és az ügyfél az üzenet használatának folytatására további egy percet biztosít. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>További lehetőségek az üzenetek üzenetsorból
Két módon, hogy testre szabhatja az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második beállíthat egy hosszabb vagy rövidebb láthatósági időkorlát hosszabb vagy rövidebb idő teljesen feldolgozni az egyes üzenet, amely lehetővé teszi, hogy a kód. Az alábbi példakód a **getMessages** metódus egyetlen hívásával 16 darab lekéréséhez. Ezután minden üzenetet használatával feldolgozza a **a** ciklus. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Első sor hossza
Megbecsülheti egy üzenetsorban található üzenetek számát. A **QueueRestProxy -> getQueueMetadata** módszert kéri a queue szolgáltatásból az üzenetsor metaadatait adja vissza. Hívása a **getApproximateMessageCount** hány üzenetek vannak egy üzenetsorban lévő számát módszerrel a visszaadott objektumon. A számláló értéke csak hozzávetőleges, mert üzeneteket is lehet hozzáadni vagy eltávolítani után a queue szolgáltatás válaszol a kérésre.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és a benne lévő összes üzenet törléséhez hívja a **QueueRestProxy -> deleteQueue** metódust.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok:

* Látogasson el a [az Azure Storage PHP ügyféloldali kódtár API-referencia](http://azure.github.io/azure-storage-php/)
* Tekintse meg a [várólista speciális példa](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

További információ: a [PHP fejlesztői központ](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: http://getcomposer.org/composer.phar

