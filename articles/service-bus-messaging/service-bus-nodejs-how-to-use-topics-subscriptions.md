---
title: Az Azure Service Bus-üzenettémák és előfizetések használata a node.js környezettel |} A Microsoft Docs
description: Megtudhatja, hogyan használhatja a Service Bus-üzenettémák és előfizetések az Azure Node.js-alkalmazásból.
services: service-bus-messaging
documentationcenter: nodejs
author: spelluru
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: fbb43d07296ca573f0c4cb9f1e10e005633ade06
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700096"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Hogyan használható a Service Bus-üzenettémák és előfizetések a node.js használatával

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az útmutató azt ismerteti, hogyan használható a Service Bus-üzenettémák és előfizetések Node.js-alkalmazásokból. Az ismertetett forgatókönyvek a következők:

- Üzenettémák és előfizetések létrehozása 
- Előfizetés-szűrők létrehozása 
- Üzenetek küldése egy üzenettémakörbe 
- Üzenetek fogadása egy előfizetésből
- Üzenettémák és előfizetések törlése 

Üzenettémakörökkel és előfizetésekkel kapcsolatos további információkért lásd: [további lépések](#next-steps) szakaszban.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node.js-alkalmazás. Node.js-alkalmazás létrehozásával kapcsolatos útmutatóért lásd: [Hozzon létre és telepíthet egy Node.js-alkalmazást az Azure-webhelyekre], [Node.js Felhőszolgáltatás] [ Node.js Cloud Service] Windows használatával A PowerShell vagy WebMatrix-webhely.

## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
Service Bus használata a Node.js az Azure-csomag letöltése. Ez a csomag tartalmaz-kódtárak, amely a Service Bus REST-szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Használja a Node Package Manager (NPM) a csomag beszerzése
1. Például nyisson meg egy parancssori felület **PowerShell** (Windows), **terminálon** (Mac), vagy **Bash** (Unix).
2. Lépjen abba a mappába, amelyben létrehozta a mintaalkalmazáshoz.
3. Típus **npm telepítése azure** a parancssori ablakba, amely kell eredményeznie, a következő kimenet:

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
3. Manuálisan futtathatja a **ls** paranccsal ellenőrizheti, hogy egy **csomópont\_modulok** mappában jött létre. Keresse meg a mappán belül a **azure** csomagot, amely tartalmazza a Service Bus-üzenettémakörök eléréséhez szükséges kódtárakat.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömb vagy egy másik szövegszerkesztőt, adja hozzá a következő elejéhez a **server.js** fájlt az alkalmazás:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
Az Azure-modul olvassa be a környezeti változó `AZURE_SERVICEBUS_CONNECTION_STRING` az előző lépésben beszerzett kapcsolati karakterláncot, az "a hitelesítő adatok beszerzése." Ha nincs beállítva ehhez a környezeti változóhoz, meg kell adnia a fiókadatok hívásakor `createServiceBusService`.

Példa a környezeti változók beállítása az Azure Cloud Services számára, lásd: [Storage Node.js Felhőszolgáltatás][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **ServiceBusService** objektum lehetővé teszi, hogy a témakörök. Az alábbi kód létrehoz egy **ServiceBusService** objektum. Tetején adja hozzá a **server.js** fájl, az utasítást, hogy az azure-modul importálása után:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ha felhívja `createTopicIfNotExists` a a **ServiceBusService** objektumot, az adott témakörbe adja vissza (ha van ilyen), vagy egy új témakör, a megadott névvel jön létre. A következő kódban `createTopicIfNotExists` létrehozása vagy csatlakozás nevű témakört `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

A `createServiceBusService` módszer is támogatja a további beállítások, így lehetővé teszi, például az üzenet élettartamának idején vagy a témakör maximális méretének alapértelmezett témakör beállításainak felülbírálására. 

Az alábbi példa a témakör maximális mérete 5 GB-os az egy egy perces élettartam állítja:

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
Választható szűrési műveletek használatával végrehajtott műveletek alkalmazhatók **ServiceBusService**. Műveletek szűrésének is tartalmazhat, naplózás, automatikus újrapróbálkozása, stb. A szűrők olyan objektumok, amelyek metódusokat implementálnak az alábbi aláírással:

```javascript
function handle (requestOptions, next)
```

Után hajt végre a kérést a beállítások alkalmazásával feldolgozza azokat, a metódushívások `next`, és átadja egy visszahívást, az alábbi aláírással:

```javascript
function (returnObject, finalCallback, next)
```

A visszahívási, és feldolgozás után a `returnObject` (a válasz a kérelemből a kiszolgálóhoz), a visszahívás kell meghívni mellett (ha létezik) más szűrők feldolgozása a folytatáshoz, vagy meghívása `finalCallback` a szolgáltatásmeghívási befejezéséhez.

Az Azure SDK for Node.js tartalmaz két szűrőt (**ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**), amelyek újrapróbálkozási logikát implementálnak. A következő létrehoz egy **ServiceBusService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Üzenettémakör-előfizetéseket is jönnek létre az a **ServiceBusService** objektum. Előfizetés neve, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorának üzenetet korlátoz.

> [!NOTE]
> Bármelyik, amíg előfizetések állandó, vagy a témakör társítva, a rendszer törli. Ha az alkalmazás-előfizetés létrehozása logikát tartalmaz, akkor kell először ellenőrizze, hogy az előfizetés használatával a `getSubscription` metódust.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő abban az esetben az alapértelmezett szűrő, egy előfizetés létrehozásakor használt. A **MatchAll** szűrő használatakor a rendszer a témakörbe közzétett összes üzenetet elhelyezi az előfizetés virtuális üzenetsorában. Az alábbi példa egy AllMessages nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrőt.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Szűrők, amelyek lehetővé teszik, hogy a hatókör, amely a témakörbe küldött üzenetek meg kell jelennie egy adott témakör-előfizetésben is létrehozhat.

A legrugalmasabb típusú szűrő előfizetések által támogatott a **SqlFilter**, amely megvalósítja az SQL92 egy részhalmazát. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további részletekért tekintse át a [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] szintaxist.

Szűrők használatával is hozzáadhatók egy előfizetést a `createRule` módszere a **ServiceBusService** objektum. Ez a módszer lehetővé teszi új szűrőket hozzáadni egy meglévő előfizetéshez.

> [!NOTE]
> Mivel az alapértelmezett szűrő automatikusan alkalmazza minden új előfizetés, távolítsa el az alapértelmezett szűrő vagy a **MatchAll** bármely más szűrők, megadhatja azt felülírja. Eltávolíthatja az alapértelmezett szabály használatával a `deleteRule` módszere a **ServiceBusService** objektum.
>
>

Az alábbi példa egy nevű előfizetést hoz létre `HighMessages` együtt egy **SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `messagenumber` 3-nál nagyobb tulajdonság:

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

Hasonlóképpen, az alábbi példa egy nevű előfizetést hoz létre `LowMessages` az egy **SqlFilter** , amely csak rendelkező üzeneteket választja ki egy `messagenumber` tulajdonság kisebb vagy egyenlő, mint 3:

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

Ha egy már üzenettel `MyTopic`, érkeznek fogadónak is, amelyre Ön feliratkozott a `AllMessages` témakör-előfizetés, és szelektív módon kézbesíti a `HighMessages` és `LowMessages` üzenettémakör-előfizető (attól függően, az üzenet tartalmának).

## <a name="how-to-send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Egy üzenetet küld egy Service Bus-témakörbe, az alkalmazás használatához a `sendTopicMessage` módszere a **ServiceBusService** objektum.
Service Bus-témakörökbe küldött üzenetek vannak **BrokeredMessage** objektumokat.
**BrokeredMessage** objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például `Label` és `TimeToLive`), egyéni alkalmazásspecifikus tulajdonságokat használt, valamint egy karakterláncadatokat törzse. Az alkalmazás beállíthatja az üzenet törzsét egy karakterláncértéket történő átadásával az `sendTopicMessage` és a szükséges alapvető tulajdonságainak alapértelmezett értékek alapján lesz kitöltve.

Az alábbi példa bemutatja, hogyan küldhető öt tesztüzenet az `MyTopic`. A `messagenumber` egyes üzenetek tulajdonság értéke a ciklus ismétléseinek a változik (Ez határozza meg, melyik előfizetések megkapni):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Nem található a témakörökben tárolt üzenetek száma korlátozott, de, a témakörök által tárolt üzenetek teljes mérete korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Üzenetek kapott egy előfizetés az a `receiveSubscriptionMessage` metódust a **ServiceBusService** objektum. Üzenetek alapértelmezés szerint törlődnek az előfizetésből, beolvasni azokat. Ugyanakkor beállíthatja a nem kötelező paraméter `isPeekLock` való **igaz** (betekintési) és az üzenet zárolása anélkül, hogy törölné az előfizetést.

Az alapértelmezett viselkedést, beolvasása, illetve a fogadás művelet részeként az üzenet törlése a legegyszerűbb modell, és forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén a legjobban. Ez a viselkedés megismeréséhez, fontolja meg egy forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Ha a `isPeekLock` paraméter értéke **igaz**, a fogadás kétszakaszos művelet lesz, amely lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. A Service Bus-kérést kap, amikor azt talál fel a következő üzenet, zárolja azt, hogy más fogyasztók számára fogadni, és visszaadja az alkalmazásnak.
Miután az alkalmazás feldolgozza az üzenetet (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), a fogadási folyamat második fázisa befejezné meghívásával **deleteMessage** módot, és az üzenet törlése egy paraméterként átadja. A **deleteMessage** metódus feldolgozottként jelöli meg az üzenetet, és eltávolítja az előfizetésből.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával `receiveSubscriptionMessage`. A példában először fogadása és törli az üzenetet az "LowMessages" előfizetésből, és üzenetet fogad a "HighMessages" előfizetés használatával `isPeekLock` igaz értékre kell állítani. Ezt követően pedig törli az üzenetet használatával `deleteMessage`:

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlockMessage` metódust a **ServiceBusService** objektum. Ez a módszer hatására a Service Bus feloldja az üzenet az előfizetésen belüli zárolását, és tegye elérhetővé számára az újbóli fogadását. Ebben a példányban, vagy az ugyanazon vagy egy másik fogyasztó alkalmazás általi.

Emellett van egy előfizetésen belül zárolva üzenethez társított időtúllépés. Ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejáratáig (például, ha az alkalmazás összeomlik), Service Bus automatikusan feloldja az üzenet, és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `deleteMessage` módszert hívja meg, az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ez a jelenség gyakran nevezik *legalább egyszer feldolgozása*. Ez azt jelenti, hogy minden üzenet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek, akkor hozzá kell adnia logikai az alkalmazás kezeli a duplikált üzenetek kézbesítését. Használhatja a **üzenetazonosító** tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Üzenettémák és előfizetések állandóak, és explicit módon kell-e törölve keresztül a [az Azure portal] [ Azure portal] vagy programozott módon.
A következő példa bemutatja, hogyan lehet törölni a témakör nevű `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. Az alábbi példa bemutatja, hogyan törölhet egy nevű előfizetést `HighMessages` származó a `MyTopic` témakör:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenettémakörök alapjait, kövesse az alábbi hivatkozások további.

* Lásd: [üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions].
* Az [SqlFilter][SqlFilter] API-referenciája.
* Látogasson el a [Azure SDK a Node][Azure SDK for Node] tárházban a Githubon.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Hozzon létre és telepíthet egy Node.js-alkalmazást az Azure-webhelyekre]: ./app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

