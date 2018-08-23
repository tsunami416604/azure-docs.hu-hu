---
title: Service Bus-üzenetsorok használata PHP |} A Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. A PHP nyelven írt kódmintákat.
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: b2bf67ac6943c80e5bf6ae94eca346fe964f95e6
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42058714"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Service Bus-üzenetsorok használata PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez az útmutató bemutatja, hogyan használható a Service Bus-üzenetsorok. A PHP és a használati minták írt a [php-hez készült Azure SDK-t](../php-download-sdk.md). Az ismertetett forgatókönyvek között megtalálható **üzenetsorok létrehozása**, **üzenetek küldése és fogadása**, és **üzenetsorok törlése**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
A csak követelmény a PHP-alkalmazás létrehozása, amely hozzáfér az Azure Blob service, az osztályok a hivatkozó a [php-hez készült Azure SDK-t](../php-download-sdk.md) származó a kódon belül. Bármely fejlesztői eszközök segítségével hozzon létre az alkalmazás vagy a Jegyzettömböt.

> [!NOTE]
> A PHP-telepítés is rendelkeznie kell a [OpenSSL bővítmény](http://php.net/openssl) telepítve és engedélyezve van.
> 
> 

Ebben az útmutatóban szolgáltatása, amely a PHP-alkalmazás helyi belül, vagy egy Azure webes szerepkör, feldolgozói szerepkör vagy webhelyén belül futó fogja használni.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél-kódtárak beszerzése
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
A Service Bus-üzenetsor API-k használatához tegye a következőket:

1. Az automatikus betöltő fájl használatával hivatkozhat a [require_once] [ require_once] utasítást.
2. Bármely osztályok segítségével lehet hivatkozni.

Az alábbi példa bemutatja, hogyan foglalhat bele a szalaghibák fájlt, és hivatkozás a `ServicesBuilder` osztály.

> [!NOTE]
> Ebben a példában (és más, a cikkben szereplő példákat) feltételezi, hogy telepítette a PHP-Klienskódtárak Azure Composer keresztül. Ha manuálisan vagy egy KÖRTE csomagot telepítette a kódtárakat, meg kell hivatkoznia a **WindowsAzure.php** automatikus betöltő fájlt.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Az alábbi példákban a `require_once` utasítás mindig megjelenik, de csak az a példában végrehajtásához szükséges osztályokat hivatkozott.

## <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
Hozza létre a Service Bus-ügyfélalkalmazást, először szüksége van egy érvényes kapcsolati karakterláncot a következő formátumban:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ahol `Endpoint` formátuma általában a formátum `[yourNamespace].servicebus.windows.net`.

Bármely kell használni az Azure-szolgáltatásügyfél létrehozásának a `ServicesBuilder` osztály. A következőket teheti:

* Adja meg a kapcsolati karakterlánc közvetlenül azt.
* Használja a **CloudConfigurationManager (CCM)** ellenőrizze a kapcsolati karakterlánc több külső forrás:
  * Alapértelmezés szerint azt egy külső forrás - környezeti változók támogatást tartalmaz
  * Új források által bővítése is hozzáadhat a `ConnectionStringSource` osztályban

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A Service Bus-üzenetsorok használatával kezelési műveleteket hajthat végre a `ServiceBusRestProxy` osztály. A `ServiceBusRestProxy` zkonstruování objektu keresztül a `ServicesBuilder::createServiceBusService` gyári módszer egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a token engedélyeket lehet kezelni.

Az alábbi példa bemutatja, hogyan hozható létre egy `ServiceBusRestProxy` hívja `ServiceBusRestProxy->createQueue` nevű várólista létrehozása `myqueue` belül egy `MySBNamespace` szolgáltatásnévtér:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Használhatja a `listQueues` metódust `ServiceBusRestProxy` objektumok ellenőrizze, hogy egy adott nevű üzenetsor már egy adott névtéren belül.
> 
> 

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Üzenet küldése a Service Bus-üzenetsorba, az alkalmazás meghívja a `ServiceBusRestProxy->sendQueueMessage` metódust. A következő kód bemutatja, hogyan üzenet küldése a `myqueue` belül korábban létrehozott üzenetsorba a `MySBNamespace` szolgáltatásnévteret.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Üzeneteket küldenek (és a fogadott) példányai a Service Bus a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage] [ BrokeredMessage] objektumok rendelkeznek normál módszerek és az egyéni alkalmazásspecifikus tulajdonságokat, és a egy tetszőleges alkalmazásadatokból álló törzzsel törzs tárolására használt tulajdonságokat.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Ez a várólista mérete felső korlátja 5 GB-os.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból

Üzenetek fogadása egy üzenetsorból legjobb módja az, hogy használjon egy `ServiceBusRestProxy->receiveQueueMessage` metódust. Üzenetek fogadhatók két különböző módban: [ *ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode) és [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). A **PeekLock** az alapértelmezett érték.

Használata esetén [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód, kap egy egylépéses művelet; azt jelenti, amikor a Service Bus egy üzenetsorban lévő üzenet egy olvasási kérést kap, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód a legegyszerűbb modell, és az olyan forgatókönyvekben működik a legjobban, ha az alkalmazás működését nem zavarja, hogy hiba esetén nem dolgoz fel üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Az alapértelmezett [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) üzenet fogadása a módban két szakaszból álló művelet lesz, amely lehetővé teszi olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. A Service Bus-kérést kap, amikor azt talál a következő feldolgozandó üzenetet, zárolja azt, hogy más fogyasztók számára fogadni, és majd visszaadja az alkalmazásnak. A fogadási folyamat második szakaszában a fogadott üzenethez történő átadásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `ServiceBusRestProxy->deleteMessage`. Amikor a Service Bus látja a `deleteMessage` hívást, fog jelölje meg az üzenetet, és távolítsa el az üzenetsorból.

A következő példa bemutatja, hogyan fogadni és feldolgozni egy üzenetet az [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) módban (az alapértelmezett mód).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlockMessage` metódust a fogadott üzenethez (helyett a `deleteMessage` metódus). Ennek hatására a Service Bus feloldja az az üzenetsorban lévő üzenet zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását lesz, és adja meg elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `deleteMessage` kérés kiadása, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik *legalább egyszeri* feldolgozása; azt jelenti, minden üzenetet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek, majd adja hozzá a további logikát az alkalmazások kezelésére a duplikált üzenetek kézbesítését ajánlott. Ez gyakran érhető el használatával a `getMessageId` metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, [üzenetsorok, témakörök és előfizetések] [ Queues, topics, and subscriptions] további információt.

További információ is látogasson el a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


