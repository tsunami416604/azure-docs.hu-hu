---
title: Az Azure Service Bus-várólisták használata PHP-vel
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre PHP-alkalmazásokat üzenetek küldéséhez és fogadásához egy Service Bus-várólistából.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760691"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Rövid útmutató: A Service Bus-várólisták használata a PHP-vel
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre PHP-alkalmazásokat üzenetek küldéséhez és fogadásához egy Service Bus-várólistából. 

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Ha nincs várólistája, amivel dolgozhatna, kövesse az Azure Portal használata című lépéseit [a Service Bus-várólista-cikk létrehozásához](service-bus-quickstart-portal.md) egy várólista létrehozásához.
    1. Olvassa el a Service Bus **várólistáinak**gyors **áttekintését.** 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolati karakterlánc bekapcsolása**. 

        > [!NOTE]
        > Ebben az oktatóanyagban a PHP használatával hozzon létre egy **várólistát** a Service Bus névtérben. 
3. [PHP-hoz készült Azure SDK](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
Az egyetlen követelmény egy PHP-alkalmazás, amely hozzáfér az Azure Blob szolgáltatás az osztályok hivatkozva az [Azure SDK PHP-hez](https://github.com/Azure/azure-sdk-for-php) a kódon belül. Bármilyen fejlesztői eszközzel létrehozhatja az alkalmazást vagy a Jegyzettömböt.

> [!NOTE]
> A PHP telepítéséhez az [OpenSSL kiterjesztésnek](https://php.net/openssl) is telepítve és engedélyezve kell lennie.

Ebben az útmutatóban szolgáltatásfunkciókat fog használni, amelyek helyileg egy PHP-alkalmazásból, vagy egy Azure webes szerepkörben, feldolgozói szerepkörben vagy webhelyen futó kódban hívhatók meg.

## <a name="get-the-azure-client-libraries"></a>Az Azure ügyfélkódtárak beszereznie
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
A Service Bus várólista API-inak használatához tegye a következőket:

1. Hivatkozzon az automatikus betöltőfájlra a [require_once][require_once] utasítás használatával.
2. Hivatkozzon a használható osztályokra.

A következő példa bemutatja, hogyan kell felvenni `ServicesBuilder` az automatikus betöltő fájlt, és hivatkozni az osztályra.

> [!NOTE]
> Ez a példa (és a cikk ben található egyéb példák) feltételezi, hogy a PHP ügyfélkódtárakat a Composer-en keresztül telepítette az Azure-hoz. Ha a könyvtárakat manuálisan vagy PEAR csomagként telepítette, hivatkoznia kell a **WindowsAzure.php** automatikus betöltőfájljára.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Az alábbi példákban a `require_once` kimutatás mindig megjelenik, de csak a példához szükséges osztályokra hivatkozik a program.

## <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolat beállítása
A Service Bus-ügyfél példányosításához először érvényes kapcsolati karakterláncmal kell rendelkeznie ebben a formátumban:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ahol `Endpoint` jellemzően a `[yourNamespace].servicebus.windows.net`formátum .

Bármely Azure-szolgáltatás ügyfél létrehozásához `ServicesBuilder` az osztályt kell használnia. A következőket teheti:

* Adja át a kapcsolati karakterláncot közvetlenül hozzá.
* A **CloudConfigurationManager (CCM)** segítségével több külső forrást is ellenőrizhet a kapcsolati karakterlánchoz:
  * Alapértelmezés szerint egy külső forrás - környezeti változók támogatása
  * Új forrásokat adhat hozzá `ConnectionStringSource` az osztály kiterjesztésével

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
Felügyeleti műveleteket hajthat végre a Service `ServiceBusRestProxy` Bus-várólistákhoz az osztályon keresztül. Az `ServiceBusRestProxy` objektum a `ServicesBuilder::createServiceBusService` gyári metóduson keresztül jön létre egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a token engedélyeket a kezeléséhez.

A következő példa bemutatja, hogyan `ServiceBusRestProxy` hozhat `ServiceBusRestProxy->createQueue` létre egy `myqueue` és `MySBNamespace` hívhat hívást egy szolgáltatásnévtérben elnevezett várólista létrehozásához:

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
> Az objektumokon `listQueues` `ServiceBusRestProxy` lévő metódus segítségével ellenőrizheti, hogy létezik-e már megadott nevű várólista egy névtérben.
> 
> 

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzenetet szeretne küldeni egy Service Bus-várólistába, az alkalmazás meghívja a `ServiceBusRestProxy->sendQueueMessage` metódust. A következő kód bemutatja, hogyan `myqueue` küldhet üzenetet `MySBNamespace` a szolgáltatás névterében korábban létrehozott várólistára.

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

A Service Bus várólistáiba küldött (és onnan érkező) üzenetek a [BrokeredMessage][BrokeredMessage] osztály példányai. [A BrokeredMessage-objektumok][BrokeredMessage] szabványos metódusok és tulajdonságok készletével rendelkeznek, amelyek egyéni alkalmazásspecifikus tulajdonságok tárolására szolgálnak, és tetszőleges alkalmazásadatok at tartalmaznak.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. A várólista méretének felső határa 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából

A várólistából érkező üzenetek fogadásának legjobb `ServiceBusRestProxy->receiveQueueMessage` módja egy módszer használata. Az üzenetek két különböző módban fogadhatók: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) és [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). A **PeekLock** az alapértelmezett érték.

A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használataesetén a fogadás egylövetű művelet; azaz amikor a Service Bus olvasási kérelmet kap egy üzenetre egy várólistában, az üzenetet felhasználásként jelöli meg, és visszaküldi azt az alkalmazásnak. A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód a legegyszerűbb modell, és az olyan forgatókönyvekben működik a legjobban, ha az alkalmazás működését nem zavarja, hogy hiba esetén nem dolgoz fel üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet felhasználásként jelölte meg, majd amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenet hiányozni fog.

Az alapértelmezett [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) módban az üzenet fogadása kétlépcsős műveletté válik, amely lehetővé teszi olyan alkalmazások támogatását, amelyek nem tolerálják a hiányzó üzeneteket. Amikor a Service Bus kérést kap, megkeresi a következő használni hozandó üzenetet, zárolja, hogy megakadályozza, hogy más fogyasztók megkapják, majd visszaküldi azt az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeni feldolgozáshoz), a `ServiceBusRestProxy->deleteMessage`fogadási folyamat második szakaszát a fogadási üzenet nek való átadásával fejezi be. Amikor a Service `deleteMessage` Bus látja a hívást, megfogja jelölni az üzenetet, mint a felhasznált, és távolítsa el a várólistából.

A következő példa bemutatja, hogyan fogadhat és dolgozhat fel egy üzenetet [peeklock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) módban (ez az alapértelmezett mód).

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

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni `unlockMessage` az üzenetet, akkor meghívhatja a metódust a fogadott üzenetben (a `deleteMessage` metódus helyett). Ez azt eredményezi, hogy a Service Bus feloldja az üzenet zárolását a várólistán belül, és elérhetővé teszi, hogy újra megkapja, akár ugyanazt a fogyasztó alkalmazást, akár egy másik fogyasztó alkalmazást.

Időtúltöltés is van társítva a várólistán belül zárolt üzenethez, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és végrehajtja azt. újra fogadható.

Abban az esetben, ha az alkalmazás összeomlik `deleteMessage` az üzenet feldolgozása után, de a kérelem kiadása előtt, akkor az üzenet újra kézbesítésre kerül az alkalmazáshoz, amikor újraindul. Ezt gyakran *nevezik legalább egyszer* feldolgozás; ez azt illeti, minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyv nem tűri a duplikált feldolgozást, akkor további logika hozzáadása az alkalmazásokhoz az ismétlődő üzenetek kézbesítésének kezeléséhez ajánlott. Ez gyakran az üzenet `getMessageId` módszerével érhető el, amely a kézbesítési kísérletek során állandó marad.

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta a Service Bus-várólisták alapjait, további információért olvassa [el a Várólisták, témakörök és előfizetések című témakört.][Queues, topics, and subscriptions]

További információkért látogasson el a [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


