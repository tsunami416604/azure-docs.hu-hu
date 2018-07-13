---
title: A Service Bus-üzenetsorok használata Node.js-ben |} A Microsoft Docs
description: Megtudhatja, hogyan használhatja a Service Bus-üzenetsorok az Azure Node.js-alkalmazásból.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 7bc291c3a453b4dec5e6c47ab4a7f2e2ac2b9967
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232781"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Service Bus-üzenetsorok használata a node.js használatával

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk ismerteti a Service Bus-üzenetsorok használata a node.js használatával. A Kódminták JavaScript nyelven íródtak, és használja az Node.js Azure-modul. Az ismertetett forgatókönyvek között megtalálható **üzenetsorok létrehozása**, **üzenetek küldése és fogadása**, és **üzenetsorok törlése**. Üzenetsorok további információkért lásd: a [további lépések](#next-steps) szakaszban.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node.js-alkalmazás. Node.js-alkalmazás létrehozásával kapcsolatos útmutatásért lásd: [hozzon létre és helyezhet üzembe egy Node.js-alkalmazás Azure-webhelyen][Create and deploy a Node.js application to an Azure Website], vagy [Node.js Felhőszolgáltatás] [ Node.js Cloud Service] Windows PowerShell használatával.

## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
Az Azure Service Bus, töltse le, és használhatja az Node.js Azure-csomagot. Ez a csomag tartalmaz-kódtárak, amely a Service Bus REST-szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Használja a Node Package Manager (NPM) a csomag beszerzése
1. Használja a **node.js-hez készült Windows PowerShell** parancssori ablakban, keresse meg a **c:\\csomópont\\sbqueues\\WebRole1** mappa, amelyben létrehozta a minta az alkalmazás.
2. Típus **npm telepítése azure** a parancssori ablakba, amely kell eredményeznie, a következőhöz hasonló kimenetet:

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
3. A **node_modules** mappa létrehozásának ellenőrzéséhez manuálisan is futtathatja az **ls** parancsot. Keresse meg a mappán belül a **azure** csomagot, amely tartalmazza a Service Bus-üzenetsorok eléréséhez szükséges kódtárakat.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömb vagy egy másik szövegszerkesztőt, adja hozzá a következő elejéhez a **server.js** fájlt az alkalmazás:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Egy Azure Service Bus-kapcsolat beállítása
Az Azure-modul olvassa be a környezeti változó `AZURE_SERVICEBUS_CONNECTION_STRING` a Service Bus a csatlakozáshoz szükséges információk beszerzéséhez. Ha nincs beállítva ehhez a környezeti változóhoz, meg kell adnia a fiókadatok hívásakor `createServiceBusService`.

A környezeti változók beállítása példa a [az Azure portal] [ Azure portal] egy Azure-webhelyen talál [a Storage Node.js-webalkalmazás] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusService** objektum lehetővé teszi, hogy a Service Bus-üzenetsorok. Az alábbi kód létrehoz egy **ServiceBusService** objektum. Tetején adja hozzá a **server.js** fájl, az utasítást, hogy az Azure-modul importálása után:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Meghívásával `createQueueIfNotExists` a a **ServiceBusService** objektumot, a megadott várólista adja vissza (ha van ilyen), vagy létrehoz egy új várólistát a megadott néven. A következő kódban `createQueueIfNotExists` létrehozása vagy csatlakozás az üzenetsorhoz nevű `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

A `createServiceBusService` módszer is támogatja a további lehetőségeket, amelyek lehetővé teszik például a valós idejű vagy maximális Várólistaméret üzenet ideje alapértelmezett várólista beállításainak felülbírálására. Az alábbi példa a várólista maximális mérete 5 GB-os és egy időt élettartam (TTL) értékének 1 percre állítja:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Szűrők
Választható szűrési műveletek használatával végrehajtott műveletek alkalmazhatók **ServiceBusService**. Műveletek szűrésének is tartalmazhat, naplózás, automatikus újrapróbálkozása, stb. A szűrők olyan objektumok, amelyek metódusokat implementálnak az alábbi aláírással:

```javascript
function handle (requestOptions, next)
```

Ezután a előfeldolgozásához kérelem a beállítások, a metódust kell meghívnia `next`, átadja egy visszahívást, az alábbi aláírással:

```javascript
function (returnObject, finalCallback, next)
```

A visszahívás, és feldolgozás után a `returnObject` (a válasz a kérelemből a kiszolgálóhoz), a visszahívás vagy musí vyvolat `next` folytatni a többi szűrőt, vagy egyszerűen meghívása létezésének `finalCallback`, amely befejezi a szolgáltatás Meghívási.

Két szűrők, amelyek újrapróbálkozási logikát alkalmazzák a a nodejs-hez készült Azure SDK mellékelt `ExponentialRetryPolicyFilter` és `LinearRetryPolicyFilter`. Az alábbi kód létrehoz egy `ServiceBusService` objektum, amely használja a `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Üzenet küldése a Service Bus-üzenetsorba, az alkalmazás meghívja a `sendQueueMessage` metódust a **ServiceBusService** objektum. Üzeneteket küldenek (és a fogadott) a Service Bus-üzenetsor **BrokeredMessage** objektumokat, és a egy szabványos tulajdonságkészlettel rendelkezik (például **címke** és **TimeToLive**), amely egy egyéni alkalmazásspecifikus tulajdonságokat, és a egy tetszőleges alkalmazásadatokból álló törzzsel törzs tárolására használt szótárban. Az alkalmazás beállíthatja az üzenet törzsét egy karakterláncot adja át az üzenetnek számít. Minden szükséges alapvető tulajdonságainak alapértelmezett értékekkel fel van töltve.

Az alábbi példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `myqueue` használatával `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Egy üzenetsor használatával fogadása a `receiveQueueMessage` metódust a **ServiceBusService** objektum. Alapértelmezés szerint az üzenetek törlődnek az üzenetsorból, az olvasott; azonban olvasása (betekintési), és az üzenet zárolása törlése az üzenetsorból által a nem kötelező paraméter nélkül `isPeekLock` való **igaz**.

Az alapértelmezett viselkedést, beolvasása, illetve a fogadás művelet részeként az üzenet törlése a legegyszerűbb modell, és forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén a legjobban. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Ha a `isPeekLock` paraméter értéke **igaz**, a receive két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `deleteMessage` metódust, és a paramétert a törlendő üzenet megadása. A `deleteMessage` módszer jelöli meg az üzenetet, és eltávolítja az üzenetsorból.

Az alábbi példa bemutatja, hogyan használatával üzenetek fogadásához és feldolgozásához `receiveQueueMessage`. A példában először kap, és törli az üzenetet, és majd kap egy üzenetet az `isPeekLock` beállítása **igaz**, majd törli az üzenetet használatával `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlockMessage` metódust a **ServiceBusService** objektum. Ennek hatására a Service Bus feloldja az az üzenetsorban lévő üzenet zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (pl., ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását lesz, és adja meg elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `deleteMessage` módszert hívja meg, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*, vagyis minden üzenetet legalább egyszer dolgozza, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el használatával a **üzenetazonosító** tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Üzenetsorokkal kapcsolatos további tudnivalókért lásd a következőket.

* [Üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions]
* [Az Azure SDK a Node] [ Azure SDK for Node] tárházban a Githubon
* [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
