---
title: Az Azure Service Bus témaköreinek használata az azure/service-bus Node.js csomaggal
description: Megtudhatja, hogyan használhatja a Service Bus-témaköröket és előfizetéseket az Azure-ban egy Node.js alkalmazásból az azure/service-bus csomag használatával."
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330446"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Rövid útmutató: A Service Bus-témakörök és előfizetések használata a Node.js és az azure-sb csomaggal
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre Node.js alkalmazásokat, amelyek üzeneteket küldhetnek egy Service Bus-témakörbe, és üzeneteket fogadhatnak egy Service Bus-előfizetésből az [azure-sb](https://www.npmjs.com/package/azure-sb) csomag használatával. A minták JavaScript-ben íródnak, és a Node.js [Azure modult](https://www.npmjs.com/package/azure) használják, amely belsőleg használja a `azure-sb` csomagot.

> [!IMPORTANT]
> Az [azure-sb](https://www.npmjs.com/package/azure-sb) csomag [a Service Bus REST futásidejű API-jait](/rest/api/servicebus/service-bus-runtime-rest)használja. Gyorsabb élményben lehet részeaz [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) új csomag használatával, amely a gyorsabb [AMQP 1.0 protokollt](service-bus-amqp-overview.md)használja. 
> 
> Ha többet szeretne megtudni az új csomag, olvassa el a [Service Bus-témakörök és előfizetések használata node.js és @azure/service-bus csomag,](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package)egyébként olvasson tovább, hogyan kell használni az [azure-csomag.](https://www.npmjs.com/package/azure)

Az itt tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése témakörbe 
- Üzenetek fogadása előfizetésből
- Témakörök és előfizetések törlése 

A témakörökről és az előfizetésekről a [Következő lépések](#next-steps) szakaszban olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja visual [studio- vagy MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Kövesse a [rövid útmutató lépéseit: Az Azure Portal használatával hozzon létre egy Service Bus-témakört, és a témakörelőfizetéseit](service-bus-quickstart-topics-subscriptions-portal.md) hozzon létre egy Service **Bus-névtér** létrehozásához és a **kapcsolati karakterlánc**bekéseléséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban a **Node.js** használatával hoz létre egy **témakört** és **egy előfizetést** a témakörhöz. 

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node.js alkalmazást. A Node.js alkalmazás létrehozásával kapcsolatos tudnivalókat a [Node.js alkalmazás létrehozása és központi telepítése egy Azure-webhelyen], [a Node.js felhőszolgáltatás][Node.js Cloud Service] a Windows PowerShell használatával vagy a WebMatrix webhely használatával című témakörben találja.

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
A Service Bus használatához töltse le a Node.js Azure-csomagot. Ez a csomag a Service Bus REST-szolgáltatásokkal kommunikáló tárak készletét tartalmazza.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Csomópontcsomag-kezelőt (NPM).
1. Nyisson meg egy parancssori felületet, például **a PowerShell** (Windows), **a Terminál** (Mac) vagy **a Bash** (Unix) felületet.
2. Nyissa meg azt a mappát, amelyben létrehozta a mintaalkalmazást.
3. Írja be az **npm install azure parancsot** a parancsablakba, amelynek a következő kimenetet kell eredményeznie:

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
3. Az **ls** paranccsal manuálisan ellenőrizheti, hogy létrejött-e **\_csomópontmodul-mappa.** Ebben a mappában keresse meg az **azure** csomagot, amely tartalmazza a Service Bus-témakörök eléréséhez szükséges könyvtárakat.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömb vagy más szövegszerkesztő segítségével adja hozzá az alkalmazást az alkalmazás **server.js** fájljának tetejét:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolat beállítása
Az Azure-modul beolvassa a környezeti változó `AZURE_SERVICEBUS_CONNECTION_STRING` a kapcsolati karakterlánc, amely a korábbi lépésből kapott, "A hitelesítő adatok beszerzése." Ha ez a környezeti változó nincs beállítva, `createServiceBusService`meg kell adnia a fiókadatokat a híváskor.

Egy Azure Cloud Service környezeti változóinak beállítását például a Környezeti változók beállítása című [témakörben ta-](../container-instances/container-instances-environment-variables.md#azure-cli-example)



## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **ServiceBusService** objektum lehetővé teszi a témakörök kelvalót. A következő kód létrehoz egy **ServiceBusService** objektumot. Adja hozzá a **server.js** fájl tetején, miután az azúrkék modul importálásáról szóló utasítást kapta:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ha meghívja `createTopicIfNotExists` a **ServiceBusService** objektumot, a megadott témakört adja vissza (ha létezik), vagy egy új témakör jön létre a megadott névvel. A következő `createTopicIfNotExists` kód a névvel ellátott `MyTopic`témakör létrehozásához vagy az ahhoz való csatlakozáshoz használatos:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

A `createTopicIfNotExists` metódus további beállításokat is támogat, amelyek lehetővé teszik az alapértelmezett témakörbeállítások , például az üzenet-életidő vagy a témakör maximális méretének felülbírálását. 

A következő példa a témakör maximális méretét 5 GB-ra állítja, egy perces életidővel:

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
A **ServiceBusService**használatával végrehajtott műveletekre választható szűrési műveletek alkalmazhatók. A szűrési műveletek közé tartozhat a naplózás, az automatikus újrapróbálkozás stb. A szűrők olyan objektumok, amelyek egy módszert implementáljanak az aláírással:

```javascript
function handle (requestOptions, next)
```

A kérelembeállítások előfeldolgozását követően `next`a metódus meghívja a hívásokat, és a következő aláírással továbbítja a visszahívást:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban és `returnObject` a (a kérésből a kiszolgálóra adott válasz) feldolgozása után a visszahívásnak vagy `finalCallback` meg kell hívnia a következőt (ha létezik), hogy folytassa más szűrők feldolgozását, vagy meg kell hívnia a szolgáltatás meghívásának befejezéséhez.

Az Azure SDK for Node.js tartalmaz két szűrőt (**ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**), amelyek újrapróbálkozási logikát implementálnak. A következő kód létrehoz egy **ServiceBusService** objektumot, amely az **ExponentialRetryPolicyFilter szűrőt**használja:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
A témakör-előfizetések is létre jönnek a **ServiceBusService** objektummal. Az előfizetések neve meg van nevezve, és rendelkezhetnek egy választható szűrővel, amely korlátozza az előfizetés virtuális várólistájába kézbesített üzenetek készletét.

> [!NOTE]
> Alapértelmezés szerint az előfizetések állandóak, amíg vagy azok, vagy a témakör társított, törlődnek. Ha az alkalmazás logikát tartalmaz egy előfizetés létrehozásához, először `getSubscription` ellenőrizze, hogy az előfizetés létezik-e a módszer használatával.
>
> Az [AutoDeleteOnIdle tulajdonság](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle)beállításával automatikusan törölheti az előfizetéseket.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az alapértelmezett szűrő, amelyet előfizetés létrehozásakor használnak. A **MatchAll** szűrő használatakor a rendszer a témakörbe közzétett összes üzenetet elhelyezi az előfizetés virtuális üzenetsorában. A következő példa létrehoz egy AllMessages nevű előfizetést, és az alapértelmezett **MatchAll** szűrőt használja.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Olyan szűrőket is létrehozhat, amelyek lehetővé teszik annak hatókörét, hogy mely témakörbe küldött üzenetek jelenjenek meg egy adott témakör-előfizetésen belül.

Az előfizetések által támogatott szűrőlegrugalmasabb típusa az **SqlFilter**, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrővel használható kifejezésekről további információt az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxis című dokumentumban talál.

Szűrők adhatók hozzá egy előfizetéshez a `createRule` **ServiceBusService** objektum metódusával. Ezzel a módszerrel új szűrőket adhat hozzá egy meglévő előfizetéshez.

> [!NOTE]
> Mivel az alapértelmezett szűrő automatikusan minden új előfizetésre vonatkozik, először el kell távolítania az alapértelmezett szűrőt, különben a **MatchAll** felülírja a többi megadott szűrőt. Az alapértelmezett szabályt eltávolíthatja `deleteRule` a **ServiceBusService** objektum metódusával.
>
>

A következő példa létrehoz `HighMessages` egy **SqlFilter** nevű előfizetést, amely `messagenumber` csak a 3-nál nagyobb egyéni tulajdonsággal rendelkező üzeneteket választja ki:

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

Hasonlóképpen a következő példa létrehoz `LowMessages` egy **SqlFilter** nevű előfizetést, `messagenumber` amely csak 3 vagy annál kisebb tulajdonsággal rendelkező üzeneteket jelöl ki:

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

Amikor egy üzenetet most `MyTopic`küld a rendszernek, az `AllMessages` a témakör-előfizetésre előfizetett fogadóknak `HighMessages` kerül, és szelektíven kézbesíti a és `LowMessages` a témakör-előfizetésre előfizetett fogadóknak (az üzenet tartalmától függően).

## <a name="how-to-send-messages-to-a-topic"></a>Hogyan küldjünk üzeneteket egy témához
Ha üzenetet szeretne küldeni egy Service Bus-témakörbe, az alkalmazásnak a `sendTopicMessage` **ServiceBusService** objektum metódusát kell használnia.
A Service Bus-témaköröknek küldött üzenetek **BrokeredMessage** objektumok.
**A BrokeredMessage-objektumok** szabványos tulajdonságokkal `Label` (például és `TimeToLive`), egy olyan szótárral rendelkeznek, amely egyéni alkalmazásspecifikus tulajdonságok tárolására szolgál, valamint karakterlánc-adatok törzse. Egy alkalmazás beállíthatja az üzenet törzsét azáltal, `sendTopicMessage` hogy egy karakterlánc-értéket ad át, és minden szükséges szabványos tulajdonságot az alapértelmezett értékek alapján tölt fel.

A következő példa bemutatja, hogyan `MyTopic`küldhet öt tesztüzenetet a rendszernek. Az `messagenumber` egyes üzenetek tulajdonságértéke a ciklus ismétlésétől függ (ez a tulajdonság határozza meg, hogy mely előfizetések kapják meg):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörben tartott üzenetek száma nincs korlátozva, de a témakörben lévő üzenetek teljes mérete is korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
Az üzenetek egy előfizetésből `receiveSubscriptionMessage` érkeznek a **ServiceBusService** objektum metódusával. Alapértelmezés szerint az üzenetek olvasás közben törlődnek az előfizetésből. A választható paramétert `isPeekLock` azonban **igaz** értékre állíthatja, hogy olvassa el (betekint) és zárolja az üzenetet anélkül, hogy az előfizetésből kiszeretné késebődni.

Az üzenet olvasásának és törlésének alapértelmezett viselkedése a fogadási művelet részeként a legegyszerűbb modell, és a legjobban olyan esetekben működik, amikor az alkalmazás hiba esetén nem képes feldolgozni az üzenetet. Ennek a viselkedésnek a megértéséhez fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd a feldolgozás előtt összeomlik. Mivel a Service Bus az üzenetet felhasználtként jelölte meg, majd amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenetet kihagyta.

Ha `isPeekLock` a paraméter **értéke igaz,** a fogadás kétlépcsős műveletté válik, amely lehetővé teszi olyan alkalmazások támogatását, amelyek nem tolerálják a nem fogadott üzeneteket. Amikor a Service Bus kérést kap, megkeresi a következő felhasználandó üzenetet, zárolja, hogy megakadályozza, hogy más fogyasztók megkapják, és visszaküldi azt az alkalmazásnak.
Miután az alkalmazás feldolgozza az üzenetet (vagy megbízhatóan tárolja a későbbi feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a **deleteMessage** metódus hívásával, és átadja az üzenetet paraméterként törölni. A **deleteMessage** metódus az üzenetet felhasználtként jelöli meg, és eltávolítja azt az előfizetésből.

A következő példa bemutatja, hogyan fogadhatók `receiveSubscriptionMessage`és dolgozhatók fel az üzenetek a használatával. A példa először fogad és töröl egy üzenetet a "LowMessages" előfizetésből, majd kap `isPeekLock` egy üzenetet a "HighMessages" előfizetésből, igaz értékre állítva. Ezután törli az `deleteMessage`üzenetet a következő használatával:

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni `unlockMessage` az üzenetet, akkor meghívhatja a metódust a **ServiceBusService** objektumon. Ez a módszer hatására a Service Bus feloldja az üzenetet az előfizetésen belül, és elérhetővé teszi, hogy újra megkapja. Ebben az esetben vagy ugyanazzal a fogyasztó alkalmazással, vagy egy másik fogyasztó alkalmazás.

Van is egy időtúltöltés társított egy üzenetet zárolva az előfizetésen belül. Ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúltöltés lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és elérhetővé teszi, hogy újra megkapja.

Abban az esetben, ha az alkalmazás összeomlik az üzenet feldolgozása után, de a `deleteMessage` metódus megküldése előtt, az üzenet újra kézbesítése az alkalmazáshoz, amikor újraindul. Ezt a viselkedést gyakran *nevezik legalább egyszer feldolgozáskor*. Ez azt illeti, minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyv nem tűri a duplikált feldolgozást, akkor logika hozzáadása kell az alkalmazáshoz az ismétlődő üzenetek kézbesítésének kezeléséhez. Használhatja az üzenet **MessageId** tulajdonságát, amely a kézbesítési kísérletek során állandó marad.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A témakörök és az előfizetések állandóak, kivéve, ha az [autoDeleteOnIdle tulajdonság](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) be van állítva, és explicit módon törölni kell az [Azure Portalon][Azure portal] keresztül vagy programozott módon.
A következő példa bemutatja, hogyan `MyTopic`lehet törölni a témakör neve:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő példa bemutatja, `HighMessages` hogyan `MyTopic` törölhet idát meg a témakörből megnevezett előfizetés:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta a Service Bus-témakörök alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* Lásd: [Várólisták, témakörök és előfizetések.][Queues, topics, and subscriptions]
* Az [SqlFilter][SqlFilter] API-referenciája.
* Látogasson el az [Azure SDK node][Azure SDK for Node] tárház a GitHubon.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js alkalmazás létrehozása és üzembe helyezése Egy Azure-webhelyen]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

