---
title: Azure Service Bus-üzenettémák és előfizetések használata Node.js |} Microsoft Docs
description: Megtudhatja, hogyan használja a Service Bus-üzenettémák és előfizetések az Azure-ban from a Node.js-alkalmazás.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: d3a7ebd135f705a6a3ea91feb4e037a9ed6d0c79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641305"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Hogyan használja a Service Bus-üzenettémák és előfizetések a Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az útmutató ismerteti a Service Bus-üzenettémák és előfizetések a Node.js-alkalmazások használatáról. Az ismertetett forgatókönyvek a következők:

- Üzenettémák és előfizetések létrehozása 
- Előfizetés-szűrők létrehozása 
- A témakör üzenetek küldése 
- Üzenetek fogadása egy előfizetésből
- Üzenettémák és előfizetések törlése 

Üzenettémakörökkel és előfizetésekkel kapcsolatos további információkért lásd: [további lépések](#next-steps) szakasz.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Üres Node.js-alkalmazás létrehozása. A Node.js-alkalmazás létrehozása, lásd: [Létrehozhat és telepíthet egy Node.js-alkalmazás az Azure webhelyén], [Node.js felhőalapú szolgáltatás] [ Node.js Cloud Service] Windows használatával PowerShell, vagy a webhelyet a WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
A Service Bus használatához le kell töltenie a Node.js Azure-csomag. Ez a csomag tartalmaz egy szalagtár szerepel, amely a Service Bus REST-szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Csomópont Package Manager (NPM) használja a csomag beszerzése
1. Például nyisson meg egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix).
2. Lépjen abba a mappába, amelyben létrehozta a mintaalkalmazáshoz.
3. Típus **npm telepítése azure** a parancsablakban, amely eredményez a következő kimeneti:

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
3. Manuálisan futtatható a **ls** parancs futtatásával ellenőrizze, hogy egy **csomópont\_modulok** mappa hozták létre. A mappában található a **azure** csomag, amely tartalmazza a Service Bus-üzenettémakörök eléréséhez szükséges könyvtárak.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömbben vagy más szövegszerkesztőben, adja hozzá a következő elejéhez a **server.js** fájl tartalmát:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
Az Azure modul olvassa be a következő környezeti változó `AZURE_SERVICEBUS_CONNECTION_STRING` a kapcsolati karakterlánc az előző lépésben beszerzett, "a hitelesítő adatok beszerzése." Ha e környezeti változó nincs beállítva, meg kell adnia a fiókadatokat, meghívásakor `createServiceBusService`.

A környezeti változók beállítása az Azure-Felhőszolgáltatásban példát talál [Node.js Felhőszolgáltatás tárolási][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **ServiceBusService** objektum lehetővé teszi a témakörök együttműködni. Az alábbi kód létrehoz egy **ServiceBusService** objektum. Az tetején adja hozzá a **server.js** fájl, az utasítást, hogy az azure modul importálása után:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ha meghívja a `createTopicIfNotExists` a a **ServiceBusService** objektum, a megadott témakör ad vissza (ha van ilyen), vagy egy új téma a megadott néven jön létre. A következő kódban `createTopicIfNotExists` létrehozása vagy csatlakozás nevű üzenettémakör `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

A `createServiceBusService` metódus is támogatja a további beállításokat, amelyek lehetővé teszik a bírálja felül az alapértelmezett témakör beállításokat, például üzenet time to live vagy maximális témakör ezen méretét. 

Az alábbi példa méretét állítja be maximális témakör 5 GB perces élő időpontja:

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
Választható szűrési műveletek használatával végrehajtott műveletek alkalmazhatók **ServiceBusService**. Műveletek szűrésének lehetnek naplózási, automatikus újrapróbálkozása, stb. A metódus aláírása megvalósító objektumok szűrők a következők:

```javascript
function handle (requestOptions, next)
```

Előfeldolgozása kérelmet a beállítások elvégzése után a metódushívások `next`, és adja át a következő aláírással rendelkező visszahívás:

```javascript
function (returnObject, finalCallback, next)
```

A visszahívási, és feldolgozás után a `returnObject` (válasza a kérés a kiszolgáló), a visszahívás kell meghívni mellett (ha van ilyen) más szűrők feldolgozása a folytatáshoz, vagy meghívása `finalCallback` a szolgáltatás hívás befejezéséhez.

Két szűrőket, amelyek megvalósítják az újrapróbálkozási logika érhetők el az Azure SDK for Node.js, a **ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**. A következő létrehoz egy **ServiceBusService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Üzenettémakör-előfizetéseket is jönnek létre a **ServiceBusService** objektum. Előfizetések megnevezett, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorában kézbesített üzenetek korlátoz.

> [!NOTE]
> Előfizetések következetesek vagy csak, vagy a következő témakörben társítva, a rendszer törli. Ha az alkalmazás előfizetés létrehozása a logikát tartalmaz, akkor először győződjön meg arról hogy létezik-e az előfizetés használatával a `getSubscription` metódust.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az alapértelmezett szűrő előfizetés létrehozásakor használható. A **MatchAll** szűrő használatakor a rendszer a témakörbe közzétett összes üzenetet elhelyezi az előfizetés virtuális üzenetsorában. A következő példa egy AllMessages nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrő.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Szűrők, amelyek lehetővé teszik, hogy a hatókör, amely egy témakörbe küldött üzenetek kell jelenik meg egy adott üzenettémakör-előfizetésben belül is létrehozhat.

A szűrő előfizetések által támogatott legrugalmasabb típusú a **SqlFilter**, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért tekintse át a [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] szintaxist.

Szűrők használatával is hozzáadhatók előfizetés a `createRule` metódusában a **ServiceBusService** objektum. Ez a módszer lehetővé teszi új szűrők hozzáadása egy meglévő előfizetéshez.

> [!NOTE]
> Mivel minden új előfizetés automatikusan alkalmazza az alapértelmezett szűrő, távolítsa el az alapértelmezett szűrő vagy a **MatchAll** felülírják más szűrőket is megadhat. Az alapértelmezett szabály eltávolításához használja a `deleteRule` metódusában a **ServiceBusService** objektum.
>
>

A következő példa egy nevű előfizetést hoz létre `HighMessages` rendelkező egy **SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `messagenumber` tulajdonságának értéke nagyobb, mint 3:

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

Hasonlóképpen, a következő példa egy nevű előfizetést hoz létre `LowMessages` rendelkező egy **SqlFilter** , amely csak rendelkező üzeneteket választja ki egy `messagenumber` tulajdonságának értéke kisebb vagy egyenlő, mint 3:

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

Ha egy most üzenettel `MyTopic`, akkor kézbesíti a rendszer az előfizetett fogadó számára a `AllMessages` üzenettémakör-előfizetésre, és szelektív módon kézbesíti a a `HighMessages` és `LowMessages` üzenettémakör-előfizetéseket (attól függően, az üzenet tartalma).

## <a name="how-to-send-messages-to-a-topic"></a>Üzenetek küldése egy témakörbe
A Service Bus-témakörbe való üzenetküldéshez az alkalmazást kell használnia a `sendTopicMessage` metódusában a **ServiceBusService** objektum.
Service Bus-üzenettémakörök küldött üzenetek **BrokeredMessage** objektumok.
**BrokeredMessage** objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például `Label` és `TimeToLive`), amellyel az egyéni alkalmazásspecifikus tulajdonságokat, valamint egy karakterláncadatokat törzsét. Az alkalmazás beállíthatja az üzenet törzsét úgy, hogy a karakterlánc-érték a `sendTopicMessage` és a szabványos tulajdonságait tölti fel a alapértelmezett értékek szerint szükséges.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az `MyTopic`. A `messagenumber` minden üzenetet tulajdonság értékének meg az a ciklus ismétléseinek számától függően változik (Ez határozza meg, melyik előfizetések fogják megkapni):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma nincs korlátozva, de a témakörök által tárolt üzenetek teljes mérete korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetés
Üzenetek fogadása egy előfizetés használatával a `receiveSubscriptionMessage` metódust a **ServiceBusService** objektum. Üzenetek alapértelmezés szerint törlődnek az előfizetésből, beolvasni azokat. Azonban a nem kötelező paraméter állítható `isPeekLock` való **igaz** (betekintés) és az üzenet zárolása anélkül, hogy törölné az előfizetés.

Olvasási és az üzenet törlése a fogadási művelet részeként alapértelmezett viselkedése a legegyszerűbb modell, és forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén a legjobban. Szeretné megtudni, ez a viselkedés, fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Ha a `isPeekLock` paraméter értéke **igaz**, a receive kétszakaszos művelet, ami lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Service Bus kérelmet kap, ha azt talál a következő üzenet felhasználását, zárolja azt, hogy más fogyasztók ne fogadjon azt, és visszaadja az alkalmazásnak.
Miután az alkalmazás feldolgozza az üzenetet (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával **deleteMessage** metódust, és az üzeneteket paraméterként törlése. A **deleteMessage** metódus feldolgozottként jelöli meg a az üzenetet, és eltávolítása az előfizetésből.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával `receiveSubscriptionMessage`. A példa első fogadása és üzenet törlése a "LowMessages" előfizetés, és majd üzenetet kap a "HighMessages" előfizetést a `isPeekLock` igaz értékre állítva. Majd törli az üzenet használatával `deleteMessage`:

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlockMessage` metódust a **ServiceBusService** objektum. Ez a módszer hatására a Service Bus feloldja az üzenet az előfizetésen belüli zárolását, és tegye elérhetővé az újbóli fogadását. Ebben a példában a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás.

Az előfizetés lévő társított időtúllépés is van. Ha az alkalmazás nem tudja feldolgozni az üzenetet, járjon le a zárolás időtúllépését (például, ha az alkalmazás összeomlik), a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `deleteMessage` metódust, az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ez a jelenség gyakran nevezik *legalább egyszer feldolgozása*. Ez azt jelenti, hogy minden üzenet legalább egyszer fel, de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. A forgatókönyvben nem lehetségesek, majd adja logika kezelni a duplikált üzenetek kézbesítése az alkalmazáshoz. Használhatja a **MessageId** tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Üzenettémák és előfizetések következetesek, és explicit módon kell lennie vagy törölte a [Azure-portálon] [ Azure portal] vagy programon keresztül.
A következő példa bemutatja, hogyan nevű üzenettémakör törlése `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő példa bemutatja, hogyan nevű előfizetés törlése `HighMessages` a a `MyTopic` témakör:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenettémakörök alapjait, az alábbi hivatkozásokból további.

* Lásd: [üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions].
* Az [SqlFilter][SqlFilter] API-referenciája.
* Látogasson el a [Azure SDK for csomópont] [ Azure SDK for Node] GitHub tárházából.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Létrehozhat és telepíthet egy Node.js-alkalmazás az Azure webhelyén]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

