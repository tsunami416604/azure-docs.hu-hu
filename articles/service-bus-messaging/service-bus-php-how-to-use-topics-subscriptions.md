---
title: Service Bus-üzenettémakörök használata PHP |} A Microsoft Docs
description: Útmutató a Service Bus-üzenettémakörök használata a PHP az Azure-ban.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 09/06/2018
ms.author: aschhab
ms.openlocfilehash: 44601d069f8c93e6101b5856e49ecf8c4b519e78
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569076"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Service Bus-üzenettémák és előfizetések használata PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk bemutatja, hogyan használható a Service Bus-üzenettémák és előfizetések. A PHP és a használati minták írt a [php-hez készült Azure SDK-t](../php-download-sdk.md). Az ismertetett forgatókönyvek között megtalálható **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzenetek küldése egy üzenettémakörbe**, **fogadása előfizetés üzeneteit**, és **üzenettémák és előfizetések törlése**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
A PHP-alkalmazás létrehozása, amely hozzáfér az Azure Blob service egyetlen követelménye, hogy az osztályok hivatkozhat a [php-hez készült Azure SDK-t](../php-download-sdk.md) származó a kódon belül. Bármely fejlesztői eszközök segítségével hozzon létre az alkalmazás vagy a Jegyzettömböt.

> [!NOTE]
> A PHP-telepítés is rendelkeznie kell a [OpenSSL bővítmény](http://php.net/openssl) telepítve és engedélyezve van.
> 
> 

Ez a cikk ismerteti, amely nem hívható meg helyileg egy PHP-alkalmazáson belül, vagy egy Azure webes szerepkör, feldolgozói szerepkör vagy webhelyén belül futó service szolgáltatásait használja.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél-kódtárak beszerzése
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
A Service Bus API-k használata:

1. Az automatikus betöltő fájl használatával hivatkozhat a [require_once] [ require-once] utasítást.
2. Bármely osztályok segítségével lehet hivatkozni.

Az alábbi példa bemutatja, hogyan foglalhat bele a szalaghibák fájlt, és hivatkozás a **ServiceBusService** osztály.

> [!NOTE]
> Ebben a példában (és más, a cikkben szereplő példákat) feltételezi, hogy telepítette a PHP-Klienskódtárak Azure Composer keresztül. Ha manuálisan vagy egy KÖRTE csomagot telepítette a kódtárakat, meg kell hivatkoznia a **WindowsAzure.php** automatikus betöltő fájlt.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

A következő példákban a `require_once` utasítás mindig látható, de csak az a példában végrehajtásához szükséges osztályokat hivatkozott.

## <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
Hozza létre a Service Bus-ügyfélalkalmazást, először szüksége van egy érvényes kapcsolati karakterláncot a következő formátumban:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ahol `Endpoint` formátuma általában a formátum `https://[yourNamespace].servicebus.windows.net`.

Bármely Azure-szolgáltatás ügyfél létrehozásához kell használnia a `ServicesBuilder` osztály. A következőket teheti:

* Adja meg a kapcsolati karakterlánc közvetlenül azt.
* Használja a **CloudConfigurationManager (CCM)** ellenőrizze a kapcsolati karakterlánc több külső forrás:
  * Alapértelmezés szerint ez tartalmaz egy külső forrás - környezeti változók támogatása.
  * Új forrásokat a `ConnectionStringSource` osztály kiterjesztésével adhat hozzá.

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A Service Bus-üzenettémák használatával kezelési műveleteket hajthat végre a `ServiceBusRestProxy` osztály. A `ServiceBusRestProxy` zkonstruování objektu keresztül a `ServicesBuilder::createServiceBusService` gyári módszer egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a token engedélyeket lehet kezelni.

Az alábbi példa bemutatja, hogyan hozható létre egy `ServiceBusRestProxy` hívja `ServiceBusRestProxy->createTopic` nevű témakör létrehozásához `mytopic` belül egy `MySBNamespace` névtér:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
> Használhatja a `listTopics` metódust `ServiceBusRestProxy` objektumok ellenőrizheti, ha a témakör a megadott néven már létezik egy szolgáltatásnévtérben.
> 
> 

## <a name="create-a-subscription"></a>Előfizetés létrehozása
Üzenettémakör-előfizetéseket is jönnek létre az a `ServiceBusRestProxy->createSubscription` metódust. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs meghatározva szűrő egy új előfizetés létrehozásakor, a **MatchAll** szűrő (alapértelmezett) használatos. Ha a **MatchAll** szűrőt használ, a témakörbe közzétett összes üzenetet az előfizetés virtuális üzenetsorának vannak elhelyezve. Az alábbi példa egy "mysubscription" nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrőt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
Beállíthat szűrőket, amelyek lehetővé teszik annak meghatározását, hogy mely üzenetek jelenjenek meg egy adott üzenettémakör-előfizetésben. A legrugalmasabb típusú szűrő előfizetések által támogatott a [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), amely megvalósítja az SQL92 egy részhalmazát. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. SqlFilters kapcsolatos további információkért lásd: [SqlFilter.SqlExpression tulajdonság][sqlfilter].

> [!NOTE]
> Minden egyes szabály egy adott előfizetés dolgozza fel a bejövő üzenetek egymástól függetlenül, az eredmény üzenetek hozzáadása az előfizetéséhez. Emellett minden új előfizetési csomaghoz tartozik egy alapértelmezett **szabály** objektum egy szűrőt, amely üzeneteket ad hozzá minden a témakörből az előfizetést. Csak a szűrőnek megfelelő üzenetek fogadása, távolítsa el az alapértelmezett szabályt. Eltávolíthatja az alapértelmezett szabály használatával a `ServiceBusRestProxy->deleteRule` metódust.
> 
> 

Az alábbi példa egy nevű előfizetést hoz létre `HighMessages` együtt egy **SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `MessageNumber` 3-nál nagyobb tulajdonság. Lásd: [üzenetek küldése egy üzenettémakörbe](#send-messages-to-a-topic) üzenetek egyéni tulajdonságok adásával kapcsolatos információkért.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Ez a kód egy további névtér használatát igényli: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Hasonlóképpen, az alábbi példa egy nevű előfizetést hoz létre `LowMessages` az egy `SqlFilter` , amely csak rendelkező üzeneteket választja ki egy `MessageNumber` tulajdonság kisebb vagy egyenlő, mint 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Mostantól, ha egy üzenetet küld a `mytopic` témakörben, az mindig kézbesíti az üzenetet az előfizetett a `mysubscription` előfizetését, és szelektív módon kézbesíti a `HighMessages` és `LowMessages` előfizetések (attól függően esetén a tartalmukat).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Üzenet küldése a Service Bus-témakörbe, az alkalmazás meghívja a `ServiceBusRestProxy->sendTopicMessage` metódust. A következő kód bemutatja, hogyan üzenet küldése a `mytopic` a témakörben korábban létrehozott belül a `MySBNamespace` szolgáltatásnévteret.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

Service Bus-témakörökbe küldött üzenetek olyan példányai a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage] [ BrokeredMessage] objektumok rendelkeznek alapvető tulajdonságok és metódusok, valamint a tulajdonságok, amelyek segítségével egyéni alkalmazásspecifikus tulajdonságokat. Az alábbi példa bemutatja, hogyan küldhető öt tesztüzenet az a `mytopic` a témakörben korábban hozott létre. A `setProperty` metódust használ a hozzáadhat egy egyéni tulajdonságot (`MessageNumber`) minden üzenethez. A `MessageNumber` tulajdonság értéke függ attól, minden üzenetet (Ez az érték segítségével meghatározhatja, melyik előfizetések kapni, ahogyan az a [előfizetés létrehozása](#create-a-subscription) szakaszt):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. Ez a témakör ezen méretét felső korlátja 5 GB-os. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
A legjobb módja az üzenetek fogadása egy előfizetésből egy `ServiceBusRestProxy->receiveSubscriptionMessage` metódust. Két különböző módban lehet üzeneteket fogadni: [*ReceiveAndDelete* és *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). A **PeekLock** az alapértelmezett érték.

A [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus egy olvasási kérést kap egy előfizetésben lévő üzenetre vonatkozóan, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * mód a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, van megjelölve, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, kimaradt az összeomlás előtt feldolgozott üzenetet.

Az alapértelmezett [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) üzenet fogadása a módban két szakaszból álló művelet lesz, amely lehetővé teszi olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második szakaszában a fogadott üzenethez történő átadásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `ServiceBusRestProxy->deleteMessage`. Amikor a Service Bus látja a `deleteMessage` hívást, feldolgozottként jelöli meg az üzenetet, és távolítsa el az üzenetsorból.

A következő példa bemutatja, hogyan fogadni és feldolgozni egy üzenetet az [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) módban (az alapértelmezett mód). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlockMessage` metódust a fogadott üzenethez (helyett a `deleteMessage` metódus). Hatására a Service Bus feloldja az az üzenetsorban lévő üzenet zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és adja meg elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `deleteMessage` kérés kiadása, akkor az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Az ilyen típusú feldolgozási gyakran nevezik *legalább egyszer* feldolgozása; azt jelenti, minden üzenetet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek, az alkalmazásfejlesztők hozzá kell adnia további logikát az alkalmazások kezelésére a duplikált üzenetek kézbesítését. Gyakran elért használatával a `getMessageId` metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A témakör vagy előfizetés törléséhez használja a `ServiceBusRestProxy->deleteTopic` vagy a `ServiceBusRestProxy->deleteSubscripton` módszereket, illetve. Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli.

Az alábbi példa bemutatja, hogyan lehet törölni a témakörbe `mytopic` és a regisztrált előfizetések.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

Használatával a `deleteSubscription` metódus, egymástól függetlenül is töröl egy előfizetést:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>További lépések
További információkért lásd: [üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
