---
title: Service Bus-üzenetsorok használata PHP |} Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. Kódminták PHP.
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
ms.openlocfilehash: 3514812f7f087582035dad5d9a4d620652aa4da9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868325"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Service Bus-üzenetsorok használata PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez az útmutató ismerteti a Service Bus-üzenetsorok használata. A mintákat a PHP és -felhasználási nyelven íródtak a [Azure SDK for PHP](../php-download-sdk.md). Az ismertetett forgatókönyvek **üzenetsorok létrehozása**, **üzenetek küldése és fogadása**, és **várólisták törlése**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
Egyetlen követelménye a PHP-alkalmazások létrehozása, amely hozzáfér az Azure Blob szolgáltatáshoz nem található osztályok hivatkozik a [Azure SDK for PHP](../php-download-sdk.md) a a kód. A fejlesztői eszközök segítségével hozzon létre az alkalmazás vagy a Jegyzettömb.

> [!NOTE]
> A PHP-telepítés is rendelkeznie kell a [OpenSSL bővítmény](http://php.net/openssl) telepítve és engedélyezve van.
> 
> 

Ez az útmutató hívható a PHP-alkalmazás helyileg, vagy egy Azure webes szerepkörről, a feldolgozói szerepkör vagy a webhely belül futó szolgáltatás funkciók fogja használni.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél szalagtárak beolvasása
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
A Service Bus-üzenetsorba API-k használatára, tegye a következőket:

1. A robotot fájl használatával hivatkozik a [require_once] [ require_once] utasítást.
2. Bármely osztályok segítségével lehet hivatkozni.

A következő példa bemutatja, hogyan a robotot fájl és a hivatkozás a `ServicesBuilder` osztály.

> [!NOTE]
> Ez a példa (és más, a cikkben szereplő példák) azt feltételezi, hogy telepítette az Azure-szerkesztő segítségével, a PHP Klienskódtárak segítségével. Ha telepítette a szalagtárak manuális vagy KÖRTEFÁK csomag, hivatkoznia kell a **WindowsAzure.php** robotjába fájlt.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Az alábbi példákban a `require_once` utasítás mindig megjelennek, de csak a szükséges végrehajtandó például osztályok hivatkozott.

## <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
Példányának létrehozása a Service Bus-ügyfélalkalmazást, először rendelkeznie kell érvényes kapcsolati karakterláncot a következő formátumban:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ha `Endpoint` formátuma általában a formátum `[yourNamespace].servicebus.windows.net`.

Bármely Azure-szolgáltatások ügyfél segítségével kell létrehozni a `ServicesBuilder` osztály. A következőket teheti:

* Átadni a kapcsolati karakterlánc közvetlenül.
* használja a **CloudConfigurationManager (CCM)** ellenőrizze a kapcsolati karakterlánc több külső forrás:
  * Alapértelmezés szerint azt egy külső adatforrás - környezeti változók támogatása tartalmaz
  * Új forrásból történő kiterjesztésével adhat hozzá a `ConnectionStringSource` osztály

Az itt leírt példák a kapcsolati karakterlánc közvetlenül át.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A Service Bus-üzenetsorok használatával kezelési műveleteket hajthat végre a `ServiceBusRestProxy` osztály. A `ServiceBusRestProxy` objektum összeállított keresztül a `ServicesBuilder::createServiceBusService` gyári módszer egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a token kezeléséhez szükséges jogokat kapjon azt.

A következő példa bemutatja, hogyan hozható létre egy `ServiceBusRestProxy` , és hívja meg `ServiceBusRestProxy->createQueue` nevű várólista létrehozása `myqueue` belül egy `MySBNamespace` szolgáltatásnévtér:

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
> Használhatja a `listQueues` metódusa `ServiceBusRestProxy` objektumok kereséséhez, ha a megadott névvel már létezik sor egy adott névtéren belül.
> 
> 

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Egy üzenet küldhető egy Service Bus-üzenetsorba, az alkalmazás hívások a `ServiceBusRestProxy->sendQueueMessage` metódust. A következő kód bemutatja, hogyan üzenet küldhető a `myqueue` várólista korábban jött létre a `MySBNamespace` szolgáltatás névtere.

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

Az elküldött (és a fogadott) példányai a Service Bus-üzenetek a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage] [ BrokeredMessage] objektumok rendelkeznek szabványos módszerek és az egyéni alkalmazásspecifikus tulajdonságokat, és egy tetszőleges alkalmazásadatokból álló törzzsel tárolására használt tulajdonságokat.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. A várólista hossza a felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból

Üzenetek fogadása egy üzenetsorból való legjobb módja a `ServiceBusRestProxy->receiveQueueMessage` metódust. Két különböző módban a lehet üzeneteket fogadni: [ *ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) és [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock). A **PeekLock** az alapértelmezett érték.

Használata esetén [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) mód, kap egy egylépéses művelet; Ez azt jelenti, hogy amikor a Service Bus egy olvasási kérést kap a sorhoz, azt az üzenetet, feldolgozottként jelöli meg, és visszaadja az alkalmazásnak. A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) mód a legegyszerűbb modell, és az olyan forgatókönyvekben működik a legjobban, ha az alkalmazás működését nem zavarja, hogy hiba esetén nem dolgoz fel üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Az alapértelmezett [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) mód, egy üzenet fogadását két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. A Service Bus kérelmet kap, ha azt talál a következő feldolgozandó üzenetet, zárolja azt, hogy más fogyasztók ne fogadjon azt, és majd visszaadja az alkalmazásnak. A fogadási folyamat második szintjére úgy, hogy a fogadott üzenet befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `ServiceBusRestProxy->deleteMessage`. Amikor a Service Bus látja a `deleteMessage` hívás, azt az üzenetet használnak, és távolítsa el a sorból.

A következő példa bemutatja, hogyan fogadni és feldolgozni egy üzenetet használatával [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) mode (az alapértelmezett mód).

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

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlockMessage` metódust a fogadott üzenethez (ahelyett, hogy a `deleteMessage` metódus). Ennek hatására a Service Bus feloldja az üzenet a várólistában zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Még nincs hozzárendelve az üzenetsorban lévő időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus lesz automatikusan feloldja az üzenet zárolását, és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `deleteMessage` kérés kiadása, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik *legalább egyszeri* ; Ez azt jelenti, hogy minden üzenet legalább egyszer fel de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek, majd adja hozzá a további logikát alkalmazások kezelésére a duplikált üzenetek kézbesítési ajánlott. Ez gyakran érhető el, használja a `getMessageId` metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, lásd: [üzenetsorok, témakörök és előfizetések] [ Queues, topics, and subscriptions] további információt.

További információ is látogasson el a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


