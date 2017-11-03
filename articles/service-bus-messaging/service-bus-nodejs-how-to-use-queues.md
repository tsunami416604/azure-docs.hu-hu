---
title: "Service Bus-üzenetsorok használata a node.js |} Microsoft Docs"
description: "Útmutató a Service Bus-üzenetsorok használata Node.js-alkalmazás az Azure-ban."
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 5b309534f7aef602610cfdb6aa784d180551e1ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Service Bus-üzenetsorok használata Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk ismerteti a Service Bus-üzenetsorok használata Node.js. A minták JavaScript nyelven íródtak, és a Node.js Azure modullal. Az ismertetett forgatókönyvek **üzenetsorok létrehozása**, **üzenetek küldése és fogadása**, és **várólisták törlése**. A várólisták további információkért lásd: a [további lépések](#next-steps) szakasz.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Üres Node.js-alkalmazás létrehozása. A Node.js-alkalmazás létrehozása, lásd: [létrehozása és központi telepítése egy Node.js-alkalmazás Azure-webhely][Create and deploy a Node.js application to an Azure Website], vagy [Node.js Felhőszolgáltatás] [ Node.js Cloud Service] Windows PowerShell használatával.

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
Azure Service Bus használatára, töltse le, és a Node.js Azure csomagot használja. Ez a csomag tartalmaz egy szalagtár szerepel, amely a Service Bus REST-szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Csomópont Package Manager (NPM) használja a csomag beszerzése
1. Használja a **Windows PowerShell, a Node.js** parancssori ablakban navigáljon a **c:\\csomópont\\sbqueues\\WebRole1** mappa, amelyben a minta létrehozása az alkalmazás.
2. Típus **npm telepítése azure** a parancsablakban, amely eredményez a kimenet az alábbihoz hasonló:

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
3. Manuálisan futtatható a **ls** parancs futtatásával ellenőrizze, hogy egy **node_modules** mappa hozták létre. A mappában található a **azure** csomag, amely tartalmazza a Service Bus-üzenetsorok eléréséhez szükséges könyvtárak.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömbben vagy más szövegszerkesztőben, adja hozzá a következő elejéhez a **server.js** fájl tartalmát:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Az Azure Service Bus-kapcsolat beállítása
Az Azure modul olvassa be a következő környezeti változó `AZURE_SERVICEBUS_CONNECTION_STRING` beszerzése a Service Bus való kapcsolódáshoz szükséges adatokat. Ha e környezeti változó nincs beállítva, meg kell adnia a fiókadatokat, meghívásakor `createServiceBusService`.

A környezeti változók beállítása a konfigurációs fájlban az Azure-Felhőszolgáltatásban példát lásd: [Node.js Felhőszolgáltatás tárolási][Node.js Cloud Service with Storage].

A környezeti változók beállítása példát a [Azure-portálon] [ Azure portal] egy Azure-webhelyre, lásd: [Node.js-webalkalmazás tárolóval] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusService** objektum lehetővé teszi, hogy a Service Bus-üzenetsorok használata. Az alábbi kód létrehoz egy **ServiceBusService** objektum. Az tetején adja hozzá a **server.js** fájl, az utasítást, hogy az Azure modul importálása után:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Meghívásával `createQueueIfNotExists` a a **ServiceBusService** objektum, a megadott várólista ad vissza (ha van ilyen), vagy létrehoz egy új várólistát a megadott névvel. A következő kódban `createQueueIfNotExists` létrehozása vagy csatlakozás nevű üzenetsor `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

A `createServiceBusService` metódus is támogatja a további beállításokat, amelyek lehetővé teszik a bírálja felül az alapértelmezett üzenet time to live vagy maximális Várólistaméret például várólista-beállításokat. Az alábbi példa 5 GB, és egyesével élettartam (TTL) értéke 1 perc állítja a várólista maximális hossza:

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
Választható szűrési műveletek használatával végrehajtott műveletek alkalmazhatók **ServiceBusService**. Műveletek szűrésének lehetnek naplózási, automatikus újrapróbálkozása, stb. A metódus aláírása megvalósító objektumok szűrők a következők:

```javascript
function handle (requestOptions, next)
```

Ezután a kérelem a beállítások előzetes feldolgozás, a metódust kell hívni `next`, átadja a következő aláírással rendelkező visszahívás:

```javascript
function (returnObject, finalCallback, next)
```

A visszahívási, és feldolgozás után a `returnObject` (válasza a kérés a kiszolgáló), a visszahívás vagy kell meghívni `next` folytatni többi szűrőt, vagy egyszerűen meghívása létezésének `finalCallback`, amely karakterlánccal végződik-e a szolgáltatás hívása.

Két szűrőket, amelyek megvalósítják az újrapróbálkozási logika érhetők el az Azure SDK for Node.js, a `ExponentialRetryPolicyFilter` és `LinearRetryPolicyFilter`. Az alábbi kód létrehoz egy `ServiceBusService` objektum, amely használja a `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Egy üzenet küldhető egy Service Bus-üzenetsorba, az alkalmazás hívások a `sendQueueMessage` metódust a **ServiceBusService** objektum. Üzenetek küldenek (és a fogadott) Service Bus-üzenetsor **BrokeredMessage** objektumokat, és rendelkezik egy szabványos tulajdonságkészlettel (például **címke** és **TimeToLive**), egy egyéni alkalmazásspecifikus tulajdonságokat, és egy tetszőleges alkalmazásadatokból álló törzzsel tárolására használt szótárban. Az alkalmazás beállíthatja az üzenet törzsét úgy, hogy egy karakterláncot az üzenetben. Egyéb szabványos tulajdonságokat a rendszer feltölti az alapértelmezett értékekkel.

A következő példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `myqueue` használatával `sendQueueMessage`:

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

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Üzenetek fogadása egy várólista használja a `receiveQueueMessage` metódust a **ServiceBusService** objektum. Alapértelmezés szerint az üzenetek törlődnek a várólistából, olvasott; azonban (betekintés) olvasni és zárolni az üzenet törlése az üzenetsorból úgy, hogy a nem kötelező paraméter nélkül `isPeekLock` való **igaz**.

Olvasási és az üzenet törlése a fogadási művelet részeként alapértelmezett viselkedése a legegyszerűbb modell, és forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén a legjobban. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Ha a `isPeekLock` paraméter értéke **igaz**, a receive két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával `deleteMessage` metódus és a törlendő paraméterként message. A `deleteMessage` metódus az üzenetet, feldolgozottként jelöli meg, és eltávolítja az üzenetsorból.

A következő példa bemutatja, hogyan használatával üzenetek fogadása és feldolgozása `receiveQueueMessage`. A példa első fogadja és törli az üzenetet, és majd kap egy üzenetet használatával `isPeekLock` beállítása **igaz**, majd törli az üzenet használatával `deleteMessage`:

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlockMessage` metódust a **ServiceBusService** objektum. Ennek hatására a Service Bus feloldja az üzenet a várólistában zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Még nincs hozzárendelve az üzenetsorban lévő időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (pl., ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és meg fogja teszi elérhető ismételt fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `deleteMessage` metódust, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*, ez azt jelenti, hogy minden üzenet legalább egyszer dolgoz fel, de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a **MessageId** az üzenet, amely állandó marad a kézbesítési kísérletek tulajdonságát.

## <a name="next-steps"></a>Következő lépések
A várólisták kapcsolatos további tudnivalókért lásd a következőket.

* [Üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions]
* [Az Azure SDK csomópont] [ Azure SDK for Node] GitHub tárházából
* [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]:../cosmos-db/table-storage-cloud-service-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
