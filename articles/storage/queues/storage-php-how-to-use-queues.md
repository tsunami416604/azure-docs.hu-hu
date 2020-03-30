---
title: A VÁRÓlista-tároló használata a PHP-ből – Azure Storage
description: Ismerje meg, hogyan használhatja az Azure Queue storage szolgáltatás létrehozása és törlése várólisták, és üzenetek beszúrása, beküldése és törlése. A minták PHP-ben íródnak.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302971"
---
# <a name="how-to-use-queue-storage-from-php"></a>How to use Queue storage from PHP (A Queue Storage használata PHP-val)

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Ez az útmutató bemutatja, hogyan hajthatja végre a gyakori forgatókönyvek az Azure Queue storage szolgáltatás használatával. A mintákat az Azure [Storage Client Library for PHP osztályokon keresztül írják.][download] A lefedett forgatókönyvek közé tartozik a várólista-üzenetek beszúrása, bepillantása, betekintése és törlése, valamint várólisták létrehozása és törlése.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása

Az egyetlen követelmény egy PHP-alkalmazás, amely hozzáfér az Azure Queue storage az osztályok hivatkozása az [Azure Storage Client Library for PHP][download] a kódon belül. Az alkalmazás létrehozásához bármilyen fejlesztői eszközt (pl. Jegyzettömböt) használhat.

Ebben az útmutatóban a várólista-tárolási szolgáltatás olyan szolgáltatásait használja, amelyek helyileg vagy az Azure-beli webalkalmazásokon belül futó kódban hívhatók meg egy PHP-alkalmazáson belül.

## <a name="get-the-azure-client-libraries"></a>Az Azure ügyfélkönyvtárak beszereznie

### <a name="install-via-composer"></a>Telepítés zeneszerzőn keresztül

1. Hozzon létre egy **composer.json** nevű fájlt a projekt gyökerében, és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Töltse le **[a composer.phar fájlt][composer-phar]** a projekt gyökérfájljában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökér
   
    ```
    php composer.phar install
    ```

Másik lehetőségként nyissa meg az [Azure Storage PHP ügyfélkönyvtárát][download] a GitHubon a forráskód klónozásához.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása a várólista-tároló eléréséhez

Az Azure Queue storage API-k használatához a következőket kell használnia:

1. Hivatkozzon az automatikus betöltőfájlra a [require_once] utasítás használatával.
2. Hivatkozzon a használni kívánt osztályokra.

A következő példa bemutatja, hogyan lehet felvenni az automatikus betöltő fájlt, és hivatkozni a **QueueRestProxy** osztályra.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

A következő példákban az `require_once` utasítás mindig megjelenik, de csak a példa végrehajtásához szükséges osztályokra hivatkozik.

## <a name="set-up-an-azure-storage-connection"></a>Azure-tárolási kapcsolat beállítása

Az Azure Queue storage-ügyfél példányosítani, először rendelkeznie kell egy érvényes kapcsolati karakterlánc. A várólista-szolgáltatás kapcsolati karakterláncának formátuma a következő.

Élő szolgáltatás eléréséhez:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Az emulátor tárolójának eléréséhez:

```php
UseDevelopmentStorage=true
```

Az Azure Queue szolgáltatás ügyfél létrehozásához a **QueueRestProxy** osztályt kell használnia. Az alábbi módszerek bármelyikét használhatja:

* Adja át a kapcsolati karakterláncot közvetlenül hozzá.
* A kapcsolati karakterlánc tárolásához használja a webalkalmazásban a környezeti változókat. Tekintse meg [az Azure webalkalmazás konfigurációs beállítások](../../app-service/configure-common.md) dokumentum a kapcsolati karakterláncok konfigurálása.
Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A **QueueRestProxy** objektum lehetővé teszi várólista létrehozását a **CreateQueue** metódus használatával. Várólista létrehozásakor megadhat beállításokat a várólistán, de erre nincs szükség. (Az alábbi példa bemutatja, hogyan állíthatók be metaadatok egy várólistán.)

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> A metaadatkulcsok esetében ne hagyatkozzon a kis- és nagybetűk megkülönböztetésén. A szolgáltatás minden kulcsát kisbetűvel olvassa be a szolgáltatásból.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása várólistához

Ha üzenetet szeretne hozzáadni egy várólistához, használja **a QueueRestProxy->createMessage**. A metódus a várólista nevét, az üzenet szövegét és az üzenetbeállításokat veszi át (amelyek nem kötelezőek).

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

A várólista elején lévő üzenetek (vagy üzenetek) betekintése anélkül is megtekinthet, hogy eltávolítaná a várólistából a **QueueRestProxy->peekMessages**hívásával. Alapértelmezés szerint a **peekMessage** metódus egyetlen üzenetet ad vissza, de ezt az értéket a **PeekMessagesOptions->setNumberOfMessages** metódussal módosíthatja.

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
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

A kód két lépésben távolítja el az üzenetet a várólistából. Először hívja **QueueRestProxy->listMessages**, ami az üzenetet láthatatlanná teszi bármely más kód, amely a várólistából olvas. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. (Ha az üzenet ebben az időszakban nem törlődik, ismét láthatóvá válik a várólistán.) Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia a **QueueRestProxy->deleteMessage parancsot.** Az üzenet eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód hardver- vagy szoftverhiba miatt nem tudja feldolgozni az üzenetet, a kód egy másik példánya megkapja ugyanazt az üzenetet, és újra próbálkozhat. A kód kéri **deleteMessage** közvetlenül az üzenet feldolgozása után.

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

A várólistán lévő üzenetek tartalmát a **QueueRestProxy->updateMessage**. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. A következő kód új tartalommal frissíti a várólista-üzenetet, és a láthatósági időtúltöltést további 60 másodpercre állítja be. Ezzel menti az üzenethez társított munkaállapotát, és újabb percet ad az ügyfélnek az üzeneten való munka folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>További lehetőségek az üzenetek que-que-jének törléséhez

Kétféleképpen szabhatja testre az üzenetek várólistából történő beolvasását. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. Másodszor, beállíthatja a hosszabb vagy rövidebb láthatósági időout, amely lehetővé teszi a kódot többé-kevésbé időt, hogy teljes mértékben feldolgozza az egyes üzeneteket. A következő kód példa a **getMessages** metódus t használja 16 üzenetet egy hívásban. Ezután feldolgozza az egyes üzeneteket egy **for** ciklus használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Várólista hosszának beszereznie

Megbecsülheti egy üzenetsorban található üzenetek számát. A **QueueRestProxy->getQueueMetadata** metódus kéri a várólista-szolgáltatást, hogy adja vissza a várólista metaadatait. A **visszaadott objektum getApproximateMessageCount** metódusának hívása számba veszi, hogy hány üzenet van egy várólistában. A számláló csak hozzávetőleges, mert az üzenetek hozzáadhatók vagy eltávolíthatók, miután a várólista-szolgáltatás válaszol a kérésre.

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Egy várólista és az összes benne lévő üzenet törléséhez hívja meg a **QueueRestProxy->deleteQueue** metódust.

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az Azure Queue storage alapjait, kövesse az alábbi hivatkozásokat az összetettebb tárolási feladatokmegismeréséhez:

* Látogasson el az [Azure Storage PHP ügyfélkönyvtárAPI-hivatkozási hivatkozási körébe](https://azure.github.io/azure-storage-php/)
* Lásd a [speciális várólista példa](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

További információ: A [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
