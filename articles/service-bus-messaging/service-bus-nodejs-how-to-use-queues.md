---
title: Node.js Azure Service Bus Queues használata az Azure-SB-csomaggal
description: Megtudhatja, hogyan hozhat létre Node.js-alkalmazásokat az üzenetek küldéséhez és fogadásához egy Azure Service Bus-várólistából az Azure-SB csomag használatával.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-javascript
ms.openlocfilehash: cebbab4045b3f8af061bfececff1af9ed70c50de
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065125"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Gyors útmutató: Service Bus-várólisták használata az Azure-ban Node.js és az Azure-SB csomaggal
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Node.js-alkalmazásokat az üzenetek küldéséhez és fogadásához egy Azure Service Bus-várólistából az [Azure-SB](https://www.npmjs.com/package/azure-sb) csomag használatával. A minták JavaScript nyelven íródtak, és a Node.js [Azure-modult](https://www.npmjs.com/package/azure) használják, amely belsőleg használja az Azure-SB-csomagot.

> [!IMPORTANT]
> Az [Azure-SB](https://www.npmjs.com/package/azure-sb) csomag [Service Bus Rest futásidejű API-kat](/rest/api/servicebus/service-bus-runtime-rest)használ. A [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) gyorsabb [AMQP 1,0 protokollt](service-bus-amqp-overview.md)használó új felülettel gyorsabban hozzájuthat. 
> 
> Ha többet szeretne megtudni az új csomagról, tekintse meg a [Service Bus Queues használata Node.js és @azure/service-bus csomaggal](./service-bus-nodejs-how-to-use-queues-new-package.md)című témakört, máskülönben folytassa az olvasással, hogy az [Azure](https://www.npmjs.com/package/azure) -csomag hogyan használható.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket.
    1. Olvassa el Service Bus **várólisták**gyors **áttekintését** . 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolatok karakterláncának**beolvasása. 

        > [!NOTE]
        > Az ebben az oktatóanyagban található Node.js használatával hozzon létre egy **várólistát** a Service Bus névtérben. 
 

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node.js alkalmazást. Node.js alkalmazások létrehozásával kapcsolatos utasításokért lásd: [Node.js alkalmazás létrehozása és telepítése Azure-webhelyre][Create and deploy a Node.js application to an Azure Website], vagy [Node.js Cloud Service][Node.js Cloud Service] a Windows PowerShell használatával.

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
Azure Service Bus használatához töltse le és használja a Node.js Azure-csomagot. Ez a csomag olyan kódtárakat tartalmaz, amelyek a Service Bus REST-szolgáltatásokkal kommunikálnak.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Node Package Managert (NPM)
1. A **Node.jsWindows PowerShell** segítségével navigáljon a **c: \\ Node \\ sbqueues \\ webrole1 webes** mappába, amelyben létrehozta a minta alkalmazást.
2. Írja be a **NPM az Azure** -t a parancsablakban, amelynek az alábbi példához hasonló kimenetet kell eredményeznie:

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
3. A **node_modules** mappa létrehozásának ellenőrzéséhez manuálisan is futtathatja az **ls** parancsot. A mappában keresse meg azt az **Azure** -csomagot, amely a Service Bus Queues eléréséhez szükséges kódtárakat tartalmazza.

### <a name="import-the-module"></a>A modul importálása
A Jegyzettömb vagy egy másik szövegszerkesztő használatával adja hozzá a következőt az alkalmazás **server.js** fájljának elejéhez:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Azure Service Bus-kapcsolatok beállítása
Az Azure-modul beolvassa a környezeti változót a `AZURE_SERVICEBUS_CONNECTION_STRING` Service Bushoz való kapcsolódáshoz szükséges információk beszerzéséhez. Ha nincs beállítva ez a környezeti változó, a meghívásakor meg kell adnia a fiók adatait `createServiceBusService` .

Ha egy Azure-webhelyhez tartozó [Azure Portal][Azure portal] környezeti változóit kívánja beállítani, tekintse meg a [Node.js webalkalmazás tárolóval][Node.js Web Application with Storage]című témakört.

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusService** objektum lehetővé teszi Service Bus várólistákkal való munkát. A következő kód létrehoz egy **ServiceBusService** objektumot. Adja hozzá a **server.js** fájl elejéhez a következő utasítást az Azure-modul importálásához:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ha meghívja a `createQueueIfNotExists` **ServiceBusService** objektumot, a rendszer a megadott várólistát adja vissza (ha létezik), vagy új várólistát hoz létre a megadott névvel. A következő kód a `createQueueIfNotExists` nevű várólista létrehozásához vagy kapcsolódásához használja `myqueue` :

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

A `createServiceBusService` metódus további beállításokat is támogat, amelyek lehetővé teszik az alapértelmezett várólista-beállítások, például az üzenet élettartamának vagy a várólista maximális méretének felülbírálását. A következő példa a várólista maximális méretét 5 GB-ra állítja, az élettartam (TTL) értéke pedig 1 perc:

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
A nem kötelező szűrési műveletek alkalmazhatók a **ServiceBusService**használatával végrehajtott műveletekre. A szűrési műveletek magukban foglalhatják a naplózást, az automatikus újrapróbálkozásokat stb. A szűrők olyan objektumok, amelyek az aláírással ellátott metódust implementálják:

```javascript
function handle (requestOptions, next)
```

Miután megtette az előfeldolgozást a kérés beállításaiban, a metódusnak hívnia kell a `next` visszahívást a következő aláírással:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban, és a `returnObject` (a kéréstől a kiszolgálónak küldött válasz) feldolgozását követően a visszahívásnak meg kell hívnia, `next` hogy továbbra is feldolgozza-e más szűrőket, vagy meghívja a `finalCallback` szolgáltatást, amely befejezi a szolgáltatás meghívását.

Az újrapróbálkozási logikát megvalósító két szűrő szerepel a Node.js készült Azure SDK-ban `ExponentialRetryPolicyFilter` és a-ben `LinearRetryPolicyFilter` . A következő kód egy objektumot hoz létre `ServiceBusService` , amely a következőket használja `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzenetet szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás meghívja a `sendQueueMessage` metódust a **ServiceBusService** objektumon. A (és onnan érkező) Service Bus várólisták **BrokeredMessage** objektumok, és a szabványos tulajdonságok (például a **label** és a **TimeToLive**), az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgáló szótár, valamint a tetszőleges alkalmazásadatok törzse. Egy alkalmazás beállíthatja az üzenet törzsét úgy, hogy egy karakterláncot küld üzenetként. A kötelező szabványos tulajdonságok alapértelmezett értékekkel vannak feltöltve.

Az alábbi példa bemutatja, hogyan küldhet tesztüzenet üzenetet a nevű várólistára a következő `myqueue` paranccsal `sendQueueMessage` :

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

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A várólistán lévő üzenetek száma nincs korlátozva, de a várólista által tárolt üzenetek teljes méretére vonatkozó korlát szerepel. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Az üzenetek a `receiveQueueMessage` **ServiceBusService** objektum metódusának használatával érkeznek egy várólistából. Alapértelmezés szerint az üzenetek törlődnek a várólistából az olvasáskor. a nem kötelező paraméter igaz értékre állításával azonban elolvashatja (betekintést), és zárolhatja az üzenetet anélkül, hogy törölné a várólistából `isPeekLock` . **true**

Az üzenet olvasásának és törlésének alapértelmezett viselkedése a fogadási művelet részeként a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. A viselkedés megértéséhez vegye fontolóra azt a forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel Service Bus az üzenetet felhasználva fogja megtekinteni, majd az alkalmazás újraindításakor és az üzenetek újrafelhasználásakor a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Ha a `isPeekLock` paraméter értéke TRUE ( **igaz**), a fogadás kétlépéses művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), a metódus meghívásával végrehajtja a fogadási folyamat második szakaszát, `deleteMessage` és megadja az üzenet paraméterként való törlését. A `deleteMessage` metódus felhasználja az üzenetet, és eltávolítja azt a várólistából.

Az alábbi példa bemutatja, hogyan fogadhat és dolgozhat fel üzeneteket a használatával `receiveQueueMessage` . A példa először fogad és töröl egy üzenetet, majd a `isPeekLock` **true**értékre állítással fogad egy üzenetet, majd törli az üzenetet a következő használatával `deleteMessage` :

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meghívhatja a `unlockMessage` metódust a **ServiceBusService** objektumon. Ennek hatására a rendszer a várólistán belüli üzenet zárolásának feloldását Service Bus teszi, és elérhetővé teheti, hogy ugyanazt a fogyasztót használó alkalmazás vagy egy másik alkalmazás használja.

A várólistán lévő üzenethez is van egy időkorlát társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `deleteMessage` metódus hívása előtt, akkor az üzenet az újraindításkor újra bekerül az alkalmazásba. Ezt a megközelítést gyakran *legalább egyszer*kell feldolgozni, azaz minden üzenet feldolgozása legalább egyszer történik, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyv nem tudja elviselni a duplikált feldolgozást, az alkalmazások fejlesztőinek további logikát kell hozzáadniuk az alkalmazáshoz az üzenetek ismétlődő kézbesítésének kezeléséhez. Ez gyakran az üzenet **MessageID** tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek során.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések
A várólistákkal kapcsolatos további tudnivalókért tekintse meg a következő forrásokat.

* [Queues, topics, and subscriptions][Queues, topics, and subscriptions]
* [Azure SDK a Node][Azure SDK for Node] adattárhoz a githubon
* [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md