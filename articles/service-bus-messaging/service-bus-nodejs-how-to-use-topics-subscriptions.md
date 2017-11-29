---
title: "Azure Service Bus-üzenettémák és előfizetések használata Node.js |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a Service Bus-üzenettémák és előfizetések az Azure-ban from a Node.js-alkalmazás."
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: d9e463273fff0ecc198b0574443c4241dde7be79
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Hogyan használja a Service Bus-üzenettémák és előfizetések a Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az útmutató ismerteti a Service Bus-üzenettémák és előfizetések a Node.js-alkalmazások használatáról. Az ismertetett forgatókönyvek **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzenetküldésre** egy témakörbe **fogadása előfizetés üzeneteit**, és **üzenettémák és előfizetések törlése**. Az üzenettémakörökkel és előfizetésekkel kapcsolatos további információkért lásd a [További lépések](#next-steps) szakaszt.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Üres Node.js-alkalmazás létrehozása. A Node.js-alkalmazás létrehozása, lásd: [létrehozása és központi telepítése egy Node.js-alkalmazás az Azure webhelyén], [Node.js felhőalapú szolgáltatás] [ Node.js Cloud Service] Windows használatával PowerShell, vagy a webhelyet a WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
A Service Bus használatához le kell töltenie a Node.js Azure-csomag. Ez a csomag tartalmaz egy szalagtár szerepel, amely a Service Bus REST-szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Csomópont Package Manager (NPM) használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac) vagy **Bash** (Unix), lépjen abba a mappába, amelyben létrehozta a mintaalkalmazáshoz.
2. Típus **npm telepítése azure** a parancsablakban, amely eredményez a következő kimeneti:

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
Az Azure modul olvassa be a következő környezeti változó `AZURE_SERVICEBUS_CONNECTION_STRING` a kapcsolati karakterlánc az előző lépésben beszerzett, "a hitelesítő adatok beszerzése". Ha e környezeti változó nincs beállítva, meg kell adnia a fiókadatokat, meghívásakor `createServiceBusService`.

A környezeti változók beállítása az Azure-Felhőszolgáltatásban példát talál [Node.js Felhőszolgáltatás tárolási][Node.js Cloud Service with Storage].

Például egy Azure-webhely környezeti változókkal, lásd: [Node.js-webalkalmazás tárolóval][Node.js Web Application with Storage].

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **ServiceBusService** objektum lehetővé teszi a témakörök együttműködni. Az alábbi kód létrehoz egy **ServiceBusService** objektum. Az tetején adja hozzá a **server.js** fájl, az utasítást, hogy az azure modul importálása után:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Meghívásával `createTopicIfNotExists` a a **ServiceBusService** objektum, a megadott témakör az eredmény (ha van ilyen), vagy egy új téma a megadott néven jön létre. A következő kódban `createTopicIfNotExists` létrehozása vagy csatlakozás nevű üzenettémakör `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

A `createServiceBusService` metódus is támogatja a további beállításokat, amelyek lehetővé teszik a bírálja felül az alapértelmezett témakör beállításokat, például üzenet time to live vagy maximális témakör ezen méretét. Az alábbi példa méretét állítja be maximális témakör 5GB 1 perces élő időpontja:

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

Előfeldolgozása kérelmet a beállítások elvégzése után a metódushívások `next`, átadja a következő aláírással rendelkező visszahívás:

```javascript
function (returnObject, finalCallback, next)
```

A visszahívási, és feldolgozás után a `returnObject` (válasza a kérés a kiszolgáló), a visszahívás kell rá folytatni más szűrők mellett invoke vagy a meghívása `finalCallback` ellenkező esetben a szolgáltatás hívás befejezéséhez.

Két szűrőket, amelyek megvalósítják az újrapróbálkozási logika érhetők el az Azure SDK for Node.js, a **ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**. A következő létrehoz egy **ServiceBusService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Üzenettémakör-előfizetéseket is jönnek létre a **ServiceBusService** objektum. Előfizetések vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorában kézbesített üzenetek korlátoz.

> [!NOTE]
> Előfizetések állandó, és továbbra is létezik, vagy csak, vagy a következő témakörben társítva, a rendszer törli. Ha az alkalmazás előfizetés létrehozása a logikát tartalmaz, azt kell először ellenőrizze, hogy az előfizetés már léteznek használatával a `getSubscription` metódust.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az alapértelmezett szűrő, amely akkor használatos, ha nincs meghatározva szűrő egy új előfizetés létrehozásakor. Ha a **MatchAll** szűrővel, a témakörbe közzétett összes üzenetet kerülnek, az előfizetés virtuális üzenetsorában. A következő példa egy "AllMessages" nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrő.

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

Ha egy most üzenettel `MyTopic`, azt mindig kézbesíti a rendszer az előfizetett fogadó számára a `AllMessages` üzenettémakör-előfizetésre, és szelektív módon kézbesíti a a `HighMessages` és `LowMessages` témakör előfizetések () attól függően, az üzenet tartalma).

## <a name="how-to-send-messages-to-a-topic"></a>Üzenetek küldése egy témakörbe
A Service Bus-témakörbe való üzenetküldéshez az alkalmazást kell használnia a `sendTopicMessage` metódusában a **ServiceBusService** objektum.
Service Bus-üzenettémakörök küldött üzenetek **BrokeredMessage** objektumok.
**BrokeredMessage** objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például `Label` és `TimeToLive`), amellyel az egyéni alkalmazásspecifikus tulajdonságokat, valamint egy karakterláncadatokat törzsét. Az alkalmazás beállíthatja az üzenet törzsét úgy, hogy a karakterlánc-érték a `sendTopicMessage` és a szabványos tulajdonságait tölti fel a alapértelmezett értékek szerint szükséges.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az `MyTopic`. Vegye figyelembe, hogy a `messagenumber` minden üzenetet tulajdonság értékének meg az a ciklus ismétléseinek számától függően változik (Ez határozza meg, hogy melyik előfizetések fogják megkapni):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetés
Üzenetek fogadása egy előfizetés használatával a `receiveSubscriptionMessage` metódust a **ServiceBusService** objektum. Alapértelmezés szerint az üzenetek törlődnek az előfizetésből, olvasott; azonban (betekintés) olvasni és zárolni az üzenetet úgy, hogy a nem kötelező paraméter az előfizetés törlése nélkül `isPeekLock` való **igaz**.

Olvasási és az üzenet törlése a fogadási művelet részeként alapértelmezett viselkedése a legegyszerűbb modell, és forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén a legjobban. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Ha a `isPeekLock` paraméter értéke **igaz**, a receive két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak.
Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával **deleteMessage** metódus és a törlendő az üzenet egy a paraméter. A **deleteMessage** metódus lesz használnak az üzenetet, és távolítsa el az előfizetésből.

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlockMessage` metódust a **ServiceBusService** objektum. Ennek hatására a Service Bus feloldja az üzenet az előfizetésen belüli zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van lévő az előfizetéshez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási előtt időkorlát lejárta (például, ha az alkalmazás összeomlik), majd a Service Bus automatikusan feloldja az üzenet és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `deleteMessage` metódust, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*, ez azt jelenti, hogy minden üzenet legalább egyszer dolgoz fel, de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a **MessageId** az üzenet, amely állandó marad a kézbesítési kísérletek tulajdonságát.

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

A témakör is törlése eltávolítja az adott témakörre regisztrált előfizetése. Az előfizetések független módon is törölhetők. A következő példa bemutatja, hogyan nevű előfizetés törlése `HighMessages` a a `MyTopic` témakör:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Következő lépések
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
[létrehozása és központi telepítése egy Node.js-alkalmazás az Azure webhelyén]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-cloud-service-nodejs.md
