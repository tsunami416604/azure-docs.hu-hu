---
title: 'Gyors útmutató: Azure Service Bus témakörök és előfizetések használata a Node. js használatával'
description: 'Gyors útmutató: megtudhatja, hogyan használhatja Service Bus témaköröket és előfizetéseket az Azure-ban egy Node. js-alkalmazásból.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: fa6f40eba02ffe171dc521f952e0d00fc35fc7e6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721662"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata a Node. js-sel és az Azure/Service-Bus csomaggal
> [!div class="op_multi_selector" title1="Programozási nyelv" title2="Node. js-csomagjának felvétele"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Ebből az oktatóanyagból megtudhatja, hogyan írhat egy Node. js-programot, amely üzeneteket küld egy Service Bus témakörnek, és üzeneteket fogad egy Service Bus előfizetésből az új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomag használatával. Ez a csomag a gyorsabb [AMQP 1,0 protokollt](service-bus-amqp-overview.md) használja, míg a régebbi [Azure-sb-](https://www.npmjs.com/package/azure-sb) csomag [Service Bus Rest futásidejű API-kat](/rest/api/servicebus/service-bus-runtime-rest)használ. A mintákat JavaScript nyelven írták.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik a témával és az előfizetéssel, akkor a létrehozásához kövesse a [Use Azure Portal (Service Bus témakörök és előfizetések](service-bus-quickstart-topics-subscriptions-portal.md) létrehozása című cikk lépéseit). Jegyezze fel a Service Bus-példányhoz tartozó kapcsolatok karakterláncát, valamint a létrehozott témakör és előfizetés nevét. Ezeket az értékeket a mintákban fogjuk használni.

> [!NOTE]
> - Ez az oktatóanyag olyan példákkal működik, amelyeket a [NodeJS](https://nodejs.org/)használatával másolhat és futtathat. A Node. js-alkalmazások létrehozásával kapcsolatos útmutatásért lásd: [Node. js-alkalmazás létrehozása és telepítése Azure-webhelyre](../app-service/app-service-web-get-started-nodejs.md)vagy [Node. js felhőalapú szolgáltatás a Windows PowerShell használatával](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Az új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomag még nem támogatja a topcis és-előfizetések létrehozását. Ha programozott módon szeretné létrehozni őket, használja a [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) csomagot.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus NPM-csomagjának telepítéséhez nyisson meg egy parancssort, amely az elérési útjában `npm` van, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus témakörrel való interakció a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály létrehozásával kezdődik, és a használatával a [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) osztály létrehozásához használatos. Miután megkapta a témakör-ügyfelet, létrehozhat egy küldőt, és használhatja a [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) vagy a [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metódust az üzenetek küldéséhez.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy `send.js` nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet küld a témakörnek.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
        try {
            for (let i = 0; i < 10; i++) {
              const message= {
                body: `Hello world! ${i}`,
                label: `test`,
                userProperties: {
                    myCustomPropertyName: `my custom property value ${i}`
                }
              };
              console.log(`Sending message: ${message.body}`);
              await sender.send(message);
            }

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Adja meg a kapcsolatok karakterláncát és a témakör nevét a fenti kódban.
4. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához `node send.js`. 

Gratulálunk! Csak egy Service Bus üzenetsor számára küldött üzeneteket.

Az üzenetek olyan szabványos tulajdonságokkal rendelkeznek, mint a `label` és a `messageId`, amelyet a küldéskor beállíthat. Ha egyéni tulajdonságokat szeretne beállítani, használja a `userProperties`, amely egy JSON-objektum, amely az egyéni adatai kulcs-érték párokat képes tárolni.

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A témakörben tárolt üzenetek száma nincs korlátozva, de a témakörben tárolt üzenetek teljes mérete korlátozott. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
A Service Bus-előfizetésekkel való interakció a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály létrehozásával és a [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) osztály létrehozásához használattal kezdődik. Ha már rendelkezik az előfizetési ügyféllel, létrehozhat egy fogadót, és használhat [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) vagy [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metódust is az üzenetek fogadásához.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy `recieve.js` nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet próbál fogadni az előfizetésből. A kapott tényleges darabszám az előfizetésben és a hálózati késésben lévő üzenetek számától függ.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. A fenti kódban adja meg a témakör és az előfizetés nevét.
4. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához `node receiveMessages.js`.

Gratulálunk! Csak Service Bus-előfizetésből érkezett üzenetek.

A [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) metódus egy `ReceiveMode`, amely egy [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)értékű enumerálás. Ne feledje, hogy [rendezze az üzeneteket](message-transfers-locks-settlement.md#settling-receive-operations) , ha a `PeekLock` módot használja az üzenet `complete()`, `abandon()`, `defer()`vagy `deadletter()` metódusának használatával.

## <a name="subscription-filters-and-actions"></a>Előfizetési szűrők és műveletek
Service Bus támogatja az [előfizetések szűrőit és műveleteit](topic-filters.md), így a beérkező üzeneteket szűrheti egy előfizetésre, és szerkesztheti a tulajdonságait.

Ha már rendelkezik egy `SubscriptionClient` egy példányával, az alábbi módszerekkel kérheti le, adhat hozzá és távolíthat el szabályokat az előfizetéshez a szűrők és műveletek vezérléséhez.

- getRules
- addRule
- removeRule

Minden előfizetéshez tartozik egy alapértelmezett szabály, amely a True szűrőt használja az összes bejövő üzenet engedélyezéséhez. Új szabály hozzáadásakor ne felejtse el eltávolítani az alapértelmezett szűrőt, hogy a szűrő az új szabályban működjön. Ha egy előfizetéshez nem tartozik szabály, akkor nem fog üzenetet kapni.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
További tudnivalókat az alábbi forrásokban talál.

- [Üzenetsorok, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
- Egyéb NodeJS-minták kifizetése a [githubon Service Bus](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)


