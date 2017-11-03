---
title: "Service Bus-üzenettémakörök használata PHP |} Microsoft Docs"
description: "Útmutató a Service Bus-üzenettémakörök használata a PHP az Azure-ban."
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: 
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 4763b172375668213372e6f4f8fc87431c430e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Service Bus-üzenettémák és előfizetések használata PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk bemutatja, hogyan használhatja a Service Bus-üzenettémák és előfizetések. A mintákat a PHP és -felhasználási nyelven íródtak a [Azure SDK for PHP](../php-download-sdk.md). Az ismertetett forgatókönyvek **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzeneteket küld egy témakör**, **üzenetek fogadása egy előfizetésből**, és **üzenettémák és előfizetések törlése**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
A PHP-alkalmazások létrehozása, amely hozzáfér az Azure Blob szolgáltatáshoz tartozó egyetlen követelménye: hivatkozhasson rá az osztályokat a [Azure SDK for PHP](../php-download-sdk.md) a a kód. A fejlesztői eszközök segítségével hozzon létre az alkalmazás vagy a Jegyzettömb.

> [!NOTE]
> A PHP-telepítés is rendelkeznie kell a [OpenSSL bővítmény](http://php.net/openssl) telepítve és engedélyezve van.
> 
> 

Ez a cikk ismerteti a szolgáltatási funkcióinak a használatát, amely PHP-alkalmazás helyileg, vagy egy Azure webes szerepkörről, a feldolgozói szerepkör vagy a webhely belül futó kód hívása.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél szalagtárak beolvasása
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
A Service Bus API-k használata:

1. A robotot fájl használatával hivatkozik a [require_once] [ require-once] utasítást.
2. Bármely osztályok segítségével lehet hivatkozni.

A következő példa bemutatja, hogyan a robotot fájl és a hivatkozás a **ServiceBusService** osztály.

> [!NOTE]
> Ez a példa (és más, a cikkben szereplő példák) azt feltételezi, hogy telepítette az Azure-szerkesztő segítségével, a PHP Klienskódtárak segítségével. Ha telepítette a szalagtárak manuális vagy KÖRTEFÁK csomag, hivatkoznia kell a **WindowsAzure.php** robotjába fájlt.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

A következő példákban a `require_once` utasítás mindig megjelenik, de csak a szükséges végrehajtandó például osztályok hivatkozott.

## <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
Egy Service Bus-ügyfélalkalmazást példányosítani először rendelkeznie kell érvényes kapcsolati karakterláncot a következő formátumban:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ha `Endpoint` formátuma általában a formátum `https://[yourNamespace].servicebus.windows.net`.

Bármely Azure-szolgáltatások ügyfél segítségével kell létrehozni a `ServicesBuilder` osztály. A következőket teheti:

* Átadni a kapcsolati karakterlánc közvetlenül.
* használja a **CloudConfigurationManager (CCM)** ellenőrizze a kapcsolati karakterlánc több külső forrás:
  * Alapértelmezés szerint az tartalmaz egy külső adatforrás - környezeti változók támogatása.
  * Új forrásból történő kiterjesztésével adhat hozzá a `ConnectionStringSource` osztály.

Az itt leírt példák a kapcsolati karakterlánc közvetlenül át.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A Service Bus-témakörök használatával kezelési műveleteket hajthat végre a `ServiceBusRestProxy` osztály. A `ServiceBusRestProxy` objektum összeállított keresztül a `ServicesBuilder::createServiceBusService` gyári módszer egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a token kezeléséhez szükséges jogokat kapjon azt.

A következő példa bemutatja, hogyan hozható létre egy `ServiceBusRestProxy` , és hívja meg `ServiceBusRestProxy->createTopic` egy nevű üzenettémakör létrehozásához `mytopic` belül egy `MySBNamespace` névtér:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Használhatja a `listTopics` metódusa `ServiceBusRestProxy` objektumok kereséséhez, ha a témakör a megadott névvel már létezik egy szolgáltatásnévtérben.
> 
> 

## <a name="create-a-subscription"></a>Előfizetés létrehozása
Üzenettémakör-előfizetéseket is jönnek létre a `ServiceBusRestProxy->createSubscription` metódust. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az alapértelmezett szűrő, amely akkor használatos, ha nincs meghatározva szűrő egy új előfizetés létrehozásakor. Ha a **MatchAll** szűrővel, a témakörbe közzétett összes üzenetet kerülnek, az előfizetés virtuális üzenetsorában. A következő példa egy "mysubscription" nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrő.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Beállíthat szűrőket, amelyek lehetővé teszik annak meghatározását, hogy mely üzenetek jelenjenek meg egy adott üzenettémakör-előfizetésben. A szűrő előfizetések által támogatott legrugalmasabb típusú a [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter), amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. SqlFilters kapcsolatos további információkért lásd: [SqlFilter.SqlExpression tulajdonság][sqlfilter].

> [!NOTE]
> Minden egyes szabály egy előfizetésben dolgozza fel a bejövő üzenetek egymástól függetlenül, az eredmény üzenetek hozzáadása az előfizetéséhez. Emellett minden egyes új előfizetés rendelkezik egy alapértelmezett **szabály** objektum egy szűrőt, amely az összes üzenet témakörben az előfizetéshez. Csak a szűrőnek megfelelő üzeneteket fogadni, el kell távolítani az alapértelmezett szabályt. Az alapértelmezett szabály eltávolításához használja a `ServiceBusRestProxy->deleteRule` metódust.
> 
> 

A következő példa egy nevű előfizetést hoz létre `HighMessages` rendelkező egy **SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `MessageNumber` 3-nál nagyobb tulajdonság. Lásd: [üzenetek küldése egy témakör](#send-messages-to-a-topic) egyéni tulajdonságok hozzáadása üzenetek kapcsolatos információkat.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Vegye figyelembe, hogy ez a kód egy további névtér használatát igényli: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Hasonlóképpen, a következő példa egy nevű előfizetést hoz létre `LowMessages` rendelkező egy `SqlFilter` , amely csak rendelkező üzeneteket választja ki egy `MessageNumber` tulajdonságának értéke kisebb vagy egyenlő, mint 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Mostantól, ha egy üzenetet küld a `mytopic` témakör, mindig biztosítását előfizetett fogadó számára a `mysubscription` előfizetés, és szelektív módon kézbesíti a a `HighMessages` és `LowMessages` (attól függően, előfizetések esetén az üzenet tartalma).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Egy üzenet küldhető egy Service Bus-témakörbe, az alkalmazás hívások a `ServiceBusRestProxy->sendTopicMessage` metódust. A következő kód bemutatja, hogyan üzenet küldhető a `mytopic` korábban jött létre a témakör a `MySBNamespace` szolgáltatás névtere.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

Service Bus-üzenettémakörök küldött üzenetek példánya a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage] [ BrokeredMessage] objektumok rendelkeznek egy szabványos tulajdonságkészlettel és módszerek, valamint az egyéni alkalmazásspecifikus tulajdonságokat használható tulajdonságok. A következő példa bemutatja, hogyan 5 teszt üzeneteket küld a `mytopic` a témakörben korábban hozott létre. A `setProperty` módszert egyéni tulajdonság hozzáadása (`MessageNumber`) minden üzenethez. Vegye figyelembe, hogy a `MessageNumber` tulajdonság értéke a minden üzenet változik (Ez az érték segítségével meghatározhatja, melyik előfizetések fogják megkapni, ahogy az a [előfizetés létrehozása](#create-a-subscription) szakaszban):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. Ez a témakör mérete felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetés
Üzenetek fogadása egy előfizetés legjobb módja a `ServiceBusRestProxy->receiveSubscriptionMessage` metódust. Két különböző módban a lehet üzeneteket fogadni: [ *ReceiveAndDelete* és *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). A **PeekLock** az alapértelmezett érték.

A [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus egy olvasási kérést kap egy előfizetésben lévő üzenetre vonatkozóan, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * mód a legegyszerűbb modell, és működik a legjobban forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Az alapértelmezett [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mód, egy üzenet fogadását két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második szintjére úgy, hogy a fogadott üzenet befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `ServiceBusRestProxy->deleteMessage`. Amikor a Service Bus látja a `deleteMessage` hívást, az üzenetet, feldolgozottként jelöli meg és eltávolítása a sorból.

A következő példa bemutatja, hogyan fogadni és feldolgozni egy üzenetet használatával [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mode (az alapértelmezett mód). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hogyan: alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlockMessage` metódust a fogadott üzenethez (ahelyett, hogy a `deleteMessage` metódus). Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Még nincs hozzárendelve az üzenetsorban lévő időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és teszi elérhető ismételt fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `deleteMessage` kérés kiadása, akkor az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ezt gyakran nevezik *legalább egyszeri* ; Ez azt jelenti, hogy minden üzenet legalább egyszer fel de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek, majd alkalmazásfejlesztőnek további logikát alkalmazások kezelésére a duplikált üzenetek kézbesítése. Ez gyakran érhető el, használja a `getMessageId` metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A témakör és előfizetés törléséhez használja a `ServiceBusRestProxy->deleteTopic` vagy a `ServiceBusRestProxy->deleteSubscripton` módszerek, illetve. Vegye figyelembe, hogy egy témakör törlése összes előfizetést is törli az adott témakörre regisztrált.

A következő példa bemutatja, hogyan nevű egy témakör törlése `mytopic` és a regisztrált előfizetések.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Használatával a `deleteSubscription` metódus, egymástól függetlenül előfizetés törlése:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, lásd: [üzenetsorok, témakörök és előfizetések] [ Queues, topics, and subscriptions] további információt.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
