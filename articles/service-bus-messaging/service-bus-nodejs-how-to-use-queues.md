---
title: Az Azure Service Bus-várólisták használata a Node.js-ben az azure-sb csomag használatával
description: Megtudhatja, hogyan hozhat létre Node.js alkalmazásokat üzenetekküldéséhez és fogadásához egy Azure Service Bus-várólistából az azure-sb csomag használatával.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7ee3939c1a1b450f2458267ab0b70e3924a4869b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330600"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Rövid útmutató: A Service Bus-várólisták használata az Azure-ban a Node.js és az azure-sb csomaggal
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre Node.js alkalmazásokat üzenetekküldéséhez és fogadásához egy Azure Service Bus-várólistából az [azure-sb](https://www.npmjs.com/package/azure-sb) csomag használatával. A minták JavaScript-ben íródnak, és a Node.js [Azure modult](https://www.npmjs.com/package/azure) használják, amely belsőleg használja az azure-sb csomagot.

> [!IMPORTANT]
> Az [azure-sb](https://www.npmjs.com/package/azure-sb) csomag [a Service Bus REST futásidejű API-jait](/rest/api/servicebus/service-bus-runtime-rest)használja. Gyorsabb élményt kaphat az új, [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) amely a gyorsabb [AMQP 1.0 protokollt](service-bus-amqp-overview.md)használja. 
> 
> Ha többet szeretne megtudni az új csomag, olvassa el a [Service @azure/service-bus Bus-várólisták használata node.js és csomag,](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package)egyébként olvasson tovább, hogyan kell használni az [azure-csomagot.](https://www.npmjs.com/package/azure)

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs várólistája, amivel dolgozhatna, kövesse az Azure Portal használata című lépéseit [a Service Bus-várólista-cikk létrehozásához](service-bus-quickstart-portal.md) egy várólista létrehozásához.
    1. Olvassa el a Service Bus **várólistáinak**gyors **áttekintését.** 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolati karakterlánc bekapcsolása**. 

        > [!NOTE]
        > Ebben az oktatóanyagban a Node.js használatával hozzon létre egy **várólistát** a Service Bus névtérben. 
 

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node.js alkalmazást. A Node.js alkalmazás létrehozásáról a [Node.js alkalmazás létrehozása és üzembe helyezése egy Azure-webhelyre][Create and deploy a Node.js application to an Azure Website]vagy a Windows PowerShell használatával a [Node.js felhőszolgáltatás][Node.js Cloud Service] című témakörben talál.

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
Az Azure Service Bus használatához töltse le és használja a Node.js Azure-csomagot. Ez a csomag a Service Bus REST-szolgáltatásokkal kommunikáló tárak készletét tartalmazza.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Csomópontcsomag-kezelőt (NPM).
1. A **Windows PowerShell for Node.js** parancsablakban keresse meg azt a **c:\\\\sbqueues\\WebRole1** mappát, amelyben létrehozta a mintaalkalmazást.
2. Írja be az **npm install azure parancsot** a parancsablakba, amelynek a következő példához hasonló kimenetet kell eredményeznie:

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
3. A **node_modules** mappa létrehozásának ellenőrzéséhez manuálisan is futtathatja az **ls** parancsot. Ebben a mappában keresse meg az **azure** csomagot, amely tartalmazza a Service Bus-várólisták eléréséhez szükséges könyvtárakat.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömb vagy más szövegszerkesztő segítségével adja hozzá az alkalmazást az alkalmazás **server.js** fájljának tetejét:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Azure Service Bus-kapcsolat beállítása
Az Azure-modul beolvassa a környezeti változót `AZURE_SERVICEBUS_CONNECTION_STRING` a Service Bushoz való csatlakozáshoz szükséges információk beszerzéséhez. Ha ez a környezeti változó nincs beállítva, meg `createServiceBusService`kell adnia a fiókadatokat a híváskor.

Egy példa a környezeti változók beállítására az [Azure Portalon][Azure portal] egy Azure-webhelyhez, lásd: [Node.js webalkalmazás a tárterülettel.][Node.js Web Application with Storage]

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusService** objektum lehetővé teszi a Service Bus-várólisták. A következő kód létrehoz egy **ServiceBusService** objektumot. Adja hozzá a **server.js** fájl tetején, miután az Azure-modul importálására vonatkozó utasítás:

```javascript
var serviceBusService = azure.createServiceBusService();
```

A `createQueueIfNotExists` **ServiceBusService** objektum meghívásával a megadott várólistát a dis kontbéna (ha létezik), vagy egy új várólistát hoz létre a megadott névvel. A következő `createQueueIfNotExists` kód a nevű várólista létrehozásához vagy az azokhoz való csatlakozáshoz használatos: `myqueue`

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

A `createServiceBusService` módszer további beállításokat is támogat, amelyek lehetővé teszik az alapértelmezett várólista-beállítások felülbírálását, például az üzenet teljesítési idejét vagy a várólista maximális méretét. A következő példa a várólista maximális méretét 5 GB-ra állítja, és az élő idő (TTL) értéke 1 perc:

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
A **ServiceBusService**használatával végrehajtott műveletekre választható szűrési műveletek alkalmazhatók. A szűrési műveletek közé tartozhat a naplózás, az automatikus újrapróbálkozás stb. A szűrők olyan objektumok, amelyek egy módszert implementáljanak az aláírással:

```javascript
function handle (requestOptions, next)
```

Miután elvégezte az előfeldolgozást a kérelem `next`beállításaiban, a metódusnak meg kell hívnia a hívást, és a következő aláírással kell átadnia a visszahívást:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban és `returnObject` a (a kéréstől a kiszolgálóra adott válasz) feldolgozása után a visszahívásnak meg kell hívnia, `next` ha létezik más szűrők feldolgozásának folytatásához, vagy meg kell hívnia `finalCallback`a szolgáltatás meghívását.

Az újrapróbálkozási logikát megvalósító két szűrő ttartalmazza az Azure `ExponentialRetryPolicyFilter` `LinearRetryPolicyFilter`SDK for Node.js és a. A következő kód `ServiceBusService` létrehoz egy `ExponentialRetryPolicyFilter`objektumot, amely a következőt használja:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzenetet szeretne küldeni egy Service Bus-várólistába, az alkalmazás meghívja a `sendQueueMessage` metódust a **ServiceBusService** objektumon. A Service Bus várólistáiba küldött (és onnan érkező) üzenetek **BrokeredMessage-objektumok,** amelyek szabványos tulajdonságokkal (például **A Címke** és **a TimeToLive),** egy egyéni alkalmazásspecifikus tulajdonságok tárolására használt szótárral és tetszőleges alkalmazásadatokat tartalmazó szótárral rendelkeznek. Az alkalmazás beállíthatja az üzenet törzsét egy karakterlánc üzenetként történő átadásával. A rendszer minden szükséges szabványos tulajdonságot alapértelmezett értékekkel tölt fel.

A következő példa bemutatja, hogyan küldhet tesztüzenetet a megnevezett `myqueue` várólistába a következő használatával: `sendQueueMessage`

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

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Nincs korlátozva a várólistában tartott üzenetek száma, de van egy korlát a várólistában lévő üzenetek teljes méretét. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. A kvótákról további információt a [Service Bus-kvóták című témakörben talál.][Service Bus quotas]

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából
Az üzenetek egy várólistából `receiveQueueMessage` érkeznek a **ServiceBusService** objektum metódusával. Alapértelmezés szerint az üzenetek olvasás közben törlődnek a várólistából; azonban elolvashatja (betekintheti) és zárolhatja az üzenetet anélkül, hogy `isPeekLock` kiszeretné őket a várólistából, ha a választható paramétert **igaz**értékre állítja.

Az üzenet olvasásának és törlésének alapértelmezett viselkedése a fogadási művelet részeként a legegyszerűbb modell, és a legjobban olyan esetekben működik, amikor az alkalmazás elviseli az üzenet feldolgozását, ha hiba történik. Ennek a viselkedésnek a megértéséhez fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd a feldolgozás előtt összeomlik. Mivel a Service Bus az üzenetet felhasználásként jelölte meg, majd amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenet hiányozni fog.

Ha `isPeekLock` a paraméter **értéke igaz,** a fogadás kétlépcsős műveletté válik, amely lehetővé teszi a hiányzó üzeneteket nem tűrő alkalmazások támogatását. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeli feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a hívási `deleteMessage` módszerrel, és paraméterként megadja a törölni kell az üzenetet. A `deleteMessage` metódus az üzenetet felhasználásként jelöli meg, és eltávolítja a várólistából.

A következő példa bemutatja, hogyan `receiveQueueMessage`fogadhat és dolgozhat fel üzeneteket a használatával. A példa először fogad és töröl egy üzenetet, `isPeekLock` majd egy üzenetet kap az `deleteMessage` **igaz**értékkel, majd törli az üzenetet a következő használatával:

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni `unlockMessage` az üzenetet, akkor meghívhatja a metódust a **ServiceBusService** objektumon. ez azt eredményezi, hogy a Service Bus feloldja az üzenetet a várólistán belül, és elérhetővé teszi, hogy újra megkapja, akár ugyanazt a fogyasztó alkalmazást, akár egy másik fogyasztó alkalmazást.

Van egy időtúltöltés is a várólistán belül zárolt üzenethez társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és végrehajtja azt. újra fogadható.

Abban az esetben, ha az alkalmazás összeomlik `deleteMessage` az üzenet feldolgozása után, de a metódus megküldése előtt, akkor az üzenet újra kézbesítésre kerül az alkalmazáshoz, amikor újraindul. Ezt a megközelítést gyakran *nevezik legalább egyszer feldolgozása*, azaz minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyv nem tolerálja a duplikált feldolgozást, majd az alkalmazásfejlesztőknek további logikát kell hozzáadniuk az alkalmazásukhoz az ismétlődő üzenetek kézbesítésének kezeléséhez. Gyakran érhető el az üzenet **MessageId** tulajdonságával, amely a kézbesítési kísérletek során állandó marad.

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
A várólistákról az alábbi forrásokban olvashat bővebben.

* [Várólisták, témakörök és előfizetések][Queues, topics, and subscriptions]
* [Azure SDK a Node][Azure SDK for Node] tárház a GitHubon
* [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
