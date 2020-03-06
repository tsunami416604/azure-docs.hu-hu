---
title: Azure Service Bus témakörök használata az Azure/Service-Bus Node. js-csomaggal
description: Megtudhatja, hogyan használhatja az Azure-ban Service Bus témaköröket és előfizetéseket egy Node. js-alkalmazásból az Azure/Service-Bus csomag használatával. "
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: c85b63b4a56e74b0fef9a122ec995b4106496cbe
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330446"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata a Node. js-sel és az Azure-SB csomaggal
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Node. js-alkalmazásokat az üzenetek egy Service Bus témakörbe való küldéséhez, és hogyan fogadhat üzeneteket egy Service Bus-előfizetésből az [Azure-SB](https://www.npmjs.com/package/azure-sb) csomag használatával. A mintákat JavaScript nyelven írták, és a Node. js [Azure-modult](https://www.npmjs.com/package/azure) használják, amely belsőleg használja a `azure-sb` csomagot.

> [!IMPORTANT]
> Az [Azure-SB](https://www.npmjs.com/package/azure-sb) csomag [Service Bus Rest futásidejű API-kat](/rest/api/servicebus/service-bus-runtime-rest)használ. A gyorsabb [AMQP 1,0 protokollt](service-bus-amqp-overview.md)használó új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) -csomag használatával gyorsabb élményt érhet el. 
> 
> Az új csomaggal kapcsolatos további információkért lásd: [Service Bus témakörök és előfizetések használata Node. js-vel és @azure/service-bus csomaggal](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), egyéb esetben az [Azure](https://www.npmjs.com/package/azure) -csomag használatának módjával kapcsolatban.

Az itt tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése egy témakörnek 
- Üzenetek fogadása egy előfizetésből
- Témakörök és előfizetések törlése 

A témakörökkel és az előfizetésekkel kapcsolatos további információkért lásd a [következő lépések](#next-steps) szakaszt.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörhöz](service-bus-quickstart-topics-subscriptions-portal.md) , és hozzon létre egy Service Bus **névteret** , és szerezze be a **kapcsolódási karakterláncot**.

    > [!NOTE]
    > Ebben a rövid útmutatóban egy **témakört** és egy **előfizetést** fog létrehozni a témakörhöz a **Node. js** használatával. 

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node. js-alkalmazást. A Node. js-alkalmazások létrehozásával kapcsolatos utasításokért lásd: [Node. js-alkalmazás létrehozása és üzembe helyezése Azure-webhelyen], [Node. js Cloud Service][Node.js Cloud Service] a Windows PowerShell használatával vagy webhelyről a WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
A Service Bus használatához töltse le a Node. js Azure-csomagot. Ez a csomag olyan kódtárakat tartalmaz, amelyek a Service Bus REST-szolgáltatásokkal kommunikálnak.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Node Package Managert (NPM)
1. Nyisson meg egy parancssori felületet, például a **PowerShell** (Windows), a **Terminal** (Mac) vagy a **bash** (UNIX) eszközt.
2. Navigáljon ahhoz a mappához, ahová létrehozta a minta alkalmazást.
3. Írja be a **NPM az Azure telepítését** a parancsablakban, amelynek a következő kimenetet kell eredményeznie:

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. Az **ls** parancs manuális futtatásával ellenőrizheti, hogy létrejött-e a **csomópont\_moduljainak** mappája. A mappában keresse meg azt az **Azure** -csomagot, amely a Service Bus témakörök eléréséhez szükséges kódtárakat tartalmazza.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömb vagy egy másik szövegszerkesztő használatával adja hozzá a következőt az alkalmazás **Server. js** fájljának elejéhez:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolatok beállítása
Az Azure-modul beolvassa a környezeti változót `AZURE_SERVICEBUS_CONNECTION_STRING` a korábbi lépésből beszerzett, "a hitelesítő adatok beszerzése" című témakörben leírtak szerint. Ha nincs beállítva ez a környezeti változó, a `createServiceBusService`meghívásakor meg kell adnia a fiók adatait.

Az Azure Cloud Service környezeti változóinak beállítására példát a [környezeti változók beállítása](../container-instances/container-instances-environment-variables.md#azure-cli-example)című témakörben talál.



## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **ServiceBusService** objektum lehetővé teszi, hogy a témakörökkel működjön. A következő kód létrehoz egy **ServiceBusService** objektumot. Adja hozzá a **Server. js** fájl elejéhez a következő utasítást az Azure-modul importálásához:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ha meghívja a `createTopicIfNotExists`t a **ServiceBusService** objektumon, akkor a rendszer a megadott témakört adja vissza (ha létezik), vagy új témakört hoz létre a megadott névvel. A következő kód a `createTopicIfNotExists` használatával hozza létre vagy csatlakozik a `MyTopic`nevű témakörhöz:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

A `createTopicIfNotExists` metódus további lehetőségeket is támogat, amelyek segítségével felülbírálhatja az alapértelmezett témakör-beállításokat, például az üzenetek élettartamát vagy a témakörök maximális méretét. 

Az alábbi példa a témakör maximális méretét 5 GB-ra állítja be egy perc alatt:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Szűrők
A nem kötelező szűrési műveletek alkalmazhatók a **ServiceBusService**használatával végrehajtott műveletekre. A szűrési műveletek magukban foglalhatják a naplózást, az automatikus újrapróbálkozásokat stb. A szűrők olyan objektumok, amelyek az aláírással ellátott metódust implementálják:

```javascript
function handle (requestOptions, next)
```

A kérési beállítások előfeldolgozásának végrehajtása után a metódus meghívja a `next`t, és a következő aláírással továbbítja a visszahívást:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban, és a `returnObject` feldolgozását követően (a kéréstől a kiszolgálónak küldött válasz alapján) a visszahívásnak a következőt kell megadnia (ha létezik) a további szűrők feldolgozásának folytatásához, vagy `finalCallback` meghívásához a szolgáltatás meghívásának befejezéséhez.

Az Azure SDK for Node.js tartalmaz két szűrőt (**ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**), amelyek újrapróbálkozási logikát implementálnak. A következő kód létrehoz egy **ServiceBusService** objektumot, amely a **ExponentialRetryPolicyFilter**használja:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
A témakör-előfizetések a **ServiceBusService** objektummal is létrejönnek. Az előfizetések névvel rendelkeznek, és rendelkezhetnek egy opcionális szűrővel, amely korlátozza az előfizetés virtuális várólistára kézbesített üzenetek készletét.

> [!NOTE]
> Alapértelmezés szerint az előfizetések mindaddig nem állandóak, amíg el nem végzik, sem a hozzájuk társított témakört. Ha az alkalmazás logikát tartalmaz az előfizetés létrehozásához, először ellenőrizze, hogy létezik-e az előfizetés a `getSubscription` metódussal.
>
> Az előfizetéseket a [AutoDeleteOnIdle tulajdonság](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle)beállításával automatikusan törölheti.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az előfizetés létrehozásakor használt alapértelmezett szűrő. A **MatchAll** szűrő használatakor a rendszer a témakörbe közzétett összes üzenetet elhelyezi az előfizetés virtuális üzenetsorában. A következő példában létrehozunk egy AllMessages nevű előfizetést, és az alapértelmezett **MatchAll** szűrőt használják.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Létrehozhat olyan szűrőket is, amelyek lehetővé teszik, hogy a témakörbe küldött üzenetek hatókörét egy adott témakör-előfizetésen belül lehessen megjeleníteni.

Az előfizetések által támogatott legrugalmasabb típusú szűrő a **SqlFilter**, amely a SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért tekintse át a [SqlFilter. SqlExpression][SqlFilter.SqlExpression] szintaxist.

A szűrők hozzáadhatók egy előfizetéshez a **ServiceBusService** objektum `createRule` metódusának használatával. Ez a módszer lehetővé teszi új szűrők hozzáadását egy meglévő előfizetéshez.

> [!NOTE]
> Mivel az alapértelmezett szűrőt a rendszer automatikusan alkalmazza az összes új előfizetésre, először el kell távolítania az alapértelmezett szűrőt, vagy a **MatchAll** felülbírálja az esetlegesen megadható egyéb szűrőket is. Az alapértelmezett szabályt a **ServiceBusService** objektum `deleteRule` metódusának használatával távolíthatja el.
>
>

Az alábbi példa egy `HighMessages` nevű előfizetést hoz létre egy olyan **SqlFilter** , amely csak olyan üzeneteket jelöl ki, amelyeken a 3 értéknél nagyobb egyéni `messagenumber` tulajdonság szerepel:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Hasonlóképpen, az alábbi példa egy `LowMessages` nevű előfizetést hoz létre egy **SqlFilter** , amely csak olyan üzeneteket választ ki, amelyek `messagenumber` tulajdonsága kisebb vagy egyenlő, mint 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Ha a rendszer elküld egy üzenetet a `MyTopic`nek, a rendszer a `AllMessages` témakör-előfizetésre előfizetett fogadóknak kézbesíti, és szelektíven kézbesíti azokat a fogadóknak, akik előfizetettek az `HighMessages`re és `LowMessages` témakör-előfizetésekre (az üzenet tartalmától függően).

## <a name="how-to-send-messages-to-a-topic"></a>Üzenetek küldése egy témakörbe
Ha üzenetet szeretne küldeni egy Service Bus témakörnek, az alkalmazásnak a **ServiceBusService** objektum `sendTopicMessage` metódusát kell használnia.
A Service Bus témakörökbe küldött üzenetek **BrokeredMessage** objektumok.
A **BrokeredMessage** -objektumok szabványos tulajdonságokkal rendelkeznek (például `Label` és `TimeToLive`), az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgáló szótár, valamint egy karakterlánc-adattörzs. Egy alkalmazás beállíthatja az üzenet törzsét úgy, hogy karakterlánc-értéket továbbít a `sendTopicMessage`nak, és az összes szükséges standard tulajdonságot alapértelmezett értékekkel tölti ki.

Az alábbi példa bemutatja, hogyan küldhet öt tesztüzenet `MyTopic`ba. Az egyes üzenetek `messagenumber` tulajdonságának értéke a hurok iterációjában változik (ez a tulajdonság határozza meg, hogy mely előfizetések kapják meg):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörben tárolt üzenetek száma nincs korlátozva, de a témakörben tárolt üzenetek teljes mérete korlátozott. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Az üzenetek egy előfizetésből érkeznek a **ServiceBusService** objektum `receiveSubscriptionMessage` metódusának használatával. Alapértelmezés szerint az üzenetek törlődnek az előfizetésből az olvasáskor. A nem kötelező paramétert azonban a **true** értékre állíthatja `isPeekLock` az olvasási (betekintés) értékre, és az üzenet zárolását anélkül, hogy törölné az előfizetésből.

Az üzenet olvasásának és törlésének alapértelmezett viselkedése a fogadási művelet részeként a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. A viselkedés megértéséhez vegye fontolóra azt a forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, akkor az alkalmazás újraindításakor és az üzenetek újbóli használatának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Ha a `isPeekLock` paraméter értéke TRUE ( **igaz**), a fogadás kétfázisú műveletvé válik, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem képesek a kihagyott üzenetek elvégzésére. Amikor Service Bus kap egy kérelmet, megkeresi a következő üzenetet, amelyet felhasznál, zárolja, hogy más fogyasztó ne fogadja, és visszaadja az alkalmazásnak.
Miután az alkalmazás feldolgozta az üzenetet (vagy megbízhatóan tárolja a későbbi feldolgozáshoz), a **deleteMessage** metódus meghívásával végrehajtja a fogadási folyamat második szakaszát, és paraméterként továbbítja az üzenetet a törléshez. A **deleteMessage** metódus felhasznált jelöli meg az üzenetet, és eltávolítja az előfizetésből.

Az alábbi példa azt mutatja be, hogyan fogadhatók el és dolgozhatók fel az üzenetek a `receiveSubscriptionMessage`használatával. A példa először fogad és töröl egy üzenetet a "LowMessages" előfizetésből, majd a "HighMessages" előfizetésből kap egy üzenetet, amely `isPeekLock` a True értékre van állítva. Ezután törli az üzenetet a `deleteMessage`használatával:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meghívhatja a `unlockMessage` metódust a **ServiceBusService** objektumra. Ezzel a módszerrel Service Bus az üzenet zárolásának feloldására az előfizetésen belül, és elérhetővé teheti azt újra. Ebben a példányban, akár egyazon alkalmazással, akár egy másik alkalmazással.

Az előfizetésen belül zárolt üzenethez is tartozik időtúllépés. Ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúllépés lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi azt újra.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `deleteMessage` metódus meghívása előtt, az üzenet újból megjelenik az alkalmazásban az újraindításkor. Ezt a viselkedést gyakran *legalább egyszeri feldolgozásnak*nevezik. Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újból el lesz juttatva. Ha a forgatókönyv nem tudja elviselni az ismétlődő feldolgozást, akkor a duplikált üzenetek kézbesítésének kezeléséhez adjon hozzá logikát az alkalmazáshoz. Használhatja az üzenet **MessageID** tulajdonságát, amely állandó marad a kézbesítési kísérletek között.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A témakörök és az előfizetések állandóak, kivéve, ha a [autoDeleteOnIdle tulajdonság](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) be van állítva, és explicit módon törölni kell őket a [Azure Portalon][Azure portal] vagy programozottan.
Az alábbi példa bemutatja, hogyan törölheti `MyTopic`nevű témakört:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. Az alábbi példa bemutatja, hogyan törölhet `HighMessages` nevű előfizetést a `MyTopic` témakörből:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte Service Bus témakörök alapjait, kövesse az alábbi hivatkozásokat további információért.

* Lásd: [várólisták, témakörök és előfizetések][Queues, topics, and subscriptions].
* Az [SqlFilter][SqlFilter] API-referenciája.
* Látogasson el az [Azure SDK for Node][Azure SDK for Node] adattárra a githubon.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node. js-alkalmazás létrehozása és üzembe helyezése Azure-webhelyen]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

