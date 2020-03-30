---
title: Az Azure Service Bus témakörei php-vel való használata
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Service Bus témaköreit és előfizetéseit egy PHP-alkalmazásból.
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
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760674"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Rövid útmutató: A Service Bus témakörei és előfizetései php-vel

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk bemutatja, hogyan használhatja a Service Bus-témakörök és előfizetések. A minták PHP-ben íródnak, és az [Azure SDK php-t](https://github.com/Azure/azure-sdk-for-php)használják. A tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése témakörbe 
- Üzenetek fogadása előfizetésből
- Témakörök és előfizetések törlése

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja visual [studio- vagy MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Kövesse a [rövid útmutató lépéseit: Az Azure Portal használatával hozzon létre egy Service Bus-témakört, és a témakörelőfizetéseit](service-bus-quickstart-topics-subscriptions-portal.md) hozzon létre egy Service **Bus-névtér** létrehozásához és a **kapcsolati karakterlánc**bekéseléséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban a **PHP** használatával létrehoz egy **témakört** és **egy előfizetést** a témakörre. 

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
Az azure Blob szolgáltatáshoz hozzáférő PHP-alkalmazások létrehozásához csak az [Azure SDK PHP-hez](https://github.com/Azure/azure-sdk-for-php) tartozó osztályok hivatkozása a kódon belül. Bármilyen fejlesztői eszközzel létrehozhatja az alkalmazást vagy a Jegyzettömböt.

> [!NOTE]
> A PHP telepítéséhez az [OpenSSL kiterjesztésnek](https://php.net/openssl) is telepítve és engedélyezve kell lennie.
> 
> 

Ez a cikk ismerteti, hogyan használhatja a php-alkalmazásokon belül helyileg, vagy egy Azure webes szerepkörön, feldolgozói szerepkörön vagy webhelyen futó kódon belül meghívható szolgáltatásfunkciókat.

## <a name="get-the-azure-client-libraries"></a>Az Azure ügyfélkódtárak beszereznie

### <a name="install-via-composer"></a>Telepítés zeneszerzőn keresztül
1. Hozzon létre egy **composer.json** nevű fájlt a projekt gyökerében, és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Töltse le **a [composer.phar][composer-phar]** programot a projekt gyökérfájljában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökér
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
A Service Bus API-k használata:

1. Hivatkozzon az automatikus betöltőfájlra a [require_once][require-once] utasítás használatával.
2. Hivatkozzon a használható osztályokra.

A következő példa bemutatja, hogyan lehet felvenni az automatikus betöltő fájlt, és hivatkozik a **ServiceBusService** osztály.

> [!NOTE]
> Ez a példa (és a cikk ben található egyéb példák) feltételezi, hogy a PHP ügyfélkódtárakat a Composer-en keresztül telepítette az Azure-hoz. Ha a könyvtárakat manuálisan vagy PEAR csomagként telepítette, hivatkoznia kell a **WindowsAzure.php** automatikus betöltőfájljára.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

A következő példákban az `require_once` utasítás mindig megjelenik, de csak a példa végrehajtásához szükséges osztályokra hivatkozik.

## <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolat beállítása
A Service Bus-ügyfél példányosításához először érvényes kapcsolati karakterláncmal kell rendelkeznie ebben a formátumban:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ahol `Endpoint` jellemzően a `https://[yourNamespace].servicebus.windows.net`formátum .

Bármely Azure-szolgáltatás ügyfél létrehozásához `ServicesBuilder` az osztályt kell használnia. A következőket teheti:

* Adja át a kapcsolati karakterláncot közvetlenül hozzá.
* A **CloudConfigurationManager (CCM)** segítségével több külső forrást is ellenőrizhet a kapcsolati karakterlánchoz:
  * Alapértelmezés szerint jön-val támogat egy külső forrás - környezeti változók.
  * Új forrásokat a `ConnectionStringSource` osztály kiterjesztésével adhat hozzá.

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
Felügyeleti műveleteket hajthat végre a Service `ServiceBusRestProxy` Bus-témakörökhöz az osztályon keresztül. Az `ServiceBusRestProxy` objektum a `ServicesBuilder::createServiceBusService` gyári metóduson keresztül jön létre egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a token engedélyeket a kezeléséhez.

A következő példa bemutatja, hogyan `ServiceBusRestProxy` hozhat `ServiceBusRestProxy->createTopic` létre egy `mytopic` nevet `MySBNamespace` és hívhat fel egy névtérben elnevezett témakört:

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
> Az objektumokon `listTopics` `ServiceBusRestProxy` lévő metódus segítségével ellenőrizheti, hogy létezik-e már megadott nevű témakör egy szolgáltatásnévtérben.
> 
> 

## <a name="create-a-subscription"></a>Előfizetés létrehozása
A témakör-előfizetések is `ServiceBusRestProxy->createSubscription` létre jönnek a módszerrel. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha új előfizetés létrehozásakor nincs megadva szűrő, a **MatchAll** szűrő (alapértelmezett) lesz használva. A **MatchAll** szűrő használatos, a témakörben közzétett összes üzenet az előfizetés virtuális várólistájába kerül. A következő példa létrehoz `mysubscription` egy nevű előfizetést, és az alapértelmezett **MatchAll** szűrőt használja.

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
Beállíthat szűrőket, amelyek lehetővé teszik annak meghatározását, hogy mely üzenetek jelenjenek meg egy adott üzenettémakör-előfizetésben. Az előfizetések által támogatott szűrőlegrugalmasabb típusa az [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SqlFilters szűrőről további információt az [SqlFilter.SqlExpression tulajdonság című][sqlfilter]témakörben talál.

> [!NOTE]
> Az előfizetés minden szabálya egymástól függetlenül dolgozza fel a bejövő üzeneteket, és hozzáadja az eredményüzeneteket az előfizetéshez. Emellett minden új előfizetés rendelkezik egy alapértelmezett **szabály** objektum egy szűrővel, amely hozzáadja az összes üzenetet a témakörből az előfizetéshez. Ha csak a szűrőnek megfelelő üzeneteket szeretne fogadni, el kell távolítania az alapértelmezett szabályt. Az alapértelmezett szabályt a `ServiceBusRestProxy->deleteRule` módszerrel távolíthatja el.
> 
> 

A következő példa létrehoz `HighMessages` egy **SqlFilter** nevű előfizetést, amely `MessageNumber` csak a 3-nál nagyobb egyéni tulajdonsággal rendelkező üzeneteket választja ki. Az egyéni tulajdonságok üzenetekhez való hozzáadásáról az [Üzenetek küldése témakörbe](#send-messages-to-a-topic) című témakörben olvashat.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Ez a kód további névtér `WindowsAzure\ServiceBus\Models\SubscriptionInfo`használatát igényli: .

Hasonlóképpen a következő példa létrehoz `LowMessages` egy `SqlFilter` előfizetést nevű, amely `MessageNumber` csak azokat az üzeneteket választja ki, amelyek tulajdonsága legfeljebb 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Most, amikor egy üzenetet `mytopic` küld a témakör, mindig kézbesíti a `mysubscription` fogadók előfizetett az előfizetésre, `HighMessages` és `LowMessages` szelektíven kézbesített a fogadók előfizetett és előfizetések (attól függően, hogy az üzenet tartalmát).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ha üzenetet szeretne küldeni egy Service Bus-témakörnek, az alkalmazás meghívja a `ServiceBusRestProxy->sendTopicMessage` metódust. A következő kód bemutatja, hogyan `mytopic` küldhet üzenetet `MySBNamespace` a szolgáltatás névterében korábban létrehozott témakörnek.

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

A Service Bus-üzenettémakörbe küldött üzenetek a [BrokeredMessage][BrokeredMessage] osztály példányai. [A BrokeredMessage-objektumok][BrokeredMessage] szabványos tulajdonságokkal és metódusokkal, valamint egyéni alkalmazásspecifikus tulajdonságok tárolására használható tulajdonságokkal rendelkeznek. A következő példa bemutatja, hogyan `mytopic` küldhet öt tesztüzenetet a korábban létrehozott témakörnek. A `setProperty` módszer rel, hogy adjunk`MessageNumber`egy egyéni tulajdonság ( ) minden üzenet. A `MessageNumber` tulajdonság értéke az egyes üzenetektől függően változik (ezzel az értékkel meghatározhatja, hogy mely előfizetések kapják meg, ahogy az az [Előfizetés létrehozása](#create-a-subscription) szakaszban látható):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. Ez a felső korlát a téma méretére 5 GB. A kvótákról további információt a [Service Bus-kvóták című témakörben talál.][Service Bus quotas]

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
Az előfizetésből érkező üzenetek fogadásának legjobb `ServiceBusRestProxy->receiveSubscriptionMessage` módja egy módszer használata. Az üzenetek két különböző módban fogadhatók: [ *ReceiveAndDelete* és *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). A **PeekLock** az alapértelmezett érték.

A [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus egy olvasási kérést kap egy előfizetésben lévő üzenetre vonatkozóan, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * mód a legegyszerűbb modell, és működik a legjobban olyan esetekben, amikor egy alkalmazás tolerálja nem dolgoz fel egy üzenetet, ha a hiba bekövetkezik. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet felhasználtként jelölte meg, majd amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenetet kihagyta.

Az alapértelmezett [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) módban az üzenet fogadása kétlépcsős műveletté válik, amely lehetővé teszi olyan alkalmazások támogatását, amelyek nem tolerálják a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeni feldolgozáshoz), a `ServiceBusRestProxy->deleteMessage`fogadási folyamat második szakaszát a fogadási üzenet nek való átadásával fejezi be. Amikor a Service `deleteMessage` Bus látja a hívást, az üzenetet felhasználásként jelöli meg, és eltávolítja a várólistából.

A következő példa bemutatja, hogyan fogadhat és dolgozhat fel egy üzenetet [peeklock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) módban (ez az alapértelmezett mód). 

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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Útmutató: az alkalmazásösszeomlások és az olvashatatlan üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni `unlockMessage` az üzenetet, akkor meghívhatja a metódust a fogadott üzenetben (a `deleteMessage` metódus helyett). Ez okozza a Service Bus, hogy oldja fel az üzenetet a várólistán belül, és elérhetővé teszi, hogy újra kell fogadni, akár ugyanazt a fogyasztó alkalmazás, vagy egy másik fogyasztó alkalmazás.

Időtúltöltés is van társítva a várólistán belül zárolt üzenethez, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és újra fogadható.

Abban az esetben, ha az alkalmazás összeomlik `deleteMessage` az üzenet feldolgozása után, de a kérelem kiadása előtt, majd az üzenet újra kézbesítése az alkalmazáshoz, amikor újraindul. Az ilyen típusú feldolgozást gyakran *legalább egyszer feldolgozásnak* nevezik; ez azt illeti, minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyv nem tűri a duplikált feldolgozást, akkor az alkalmazásfejlesztőknek további logikát kell hozzáadniuk az alkalmazásokhoz az ismétlődő üzenetek kézbesítésének kezeléséhez. Ez gyakran az üzenet `getMessageId` módszerével érhető el, amely a kézbesítési kísérletek során állandó marad.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Témakör vagy előfizetés törléséhez használja `ServiceBusRestProxy->deleteTopic` a `ServiceBusRestProxy->deleteSubscripton` vagy a metódusokat. Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli.

A következő példa bemutatja, `mytopic` hogyan törölheti a megnevezett témakört és a regisztrált előfizetéseket.

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

A módszer `deleteSubscription` használatával egymástól függetlenül törölheti az előfizetést:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
További információt [a Várólisták, témakörök és előfizetések című témakörben talál.][Queues, topics, and subscriptions]

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
