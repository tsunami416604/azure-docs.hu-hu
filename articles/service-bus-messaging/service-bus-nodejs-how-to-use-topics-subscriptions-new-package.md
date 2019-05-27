---
title: Az Azure Service Bus-üzenettémák és előfizetések használata a node.js környezettel |} A Microsoft Docs
description: Megtudhatja, hogyan használhatja a Service Bus-üzenettémák és előfizetések az Azure Node.js-alkalmazásból.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992133"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Service Bus-üzenettémák és előfizetések használata a Node.js és az azure/szolgáltatásbusz csomag
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js |} azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js |} @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Ebben az oktatóanyagban elsajátíthatja, hogyan írhat egy Node.js üzeneteket küldeni a Service Bus-témakörbe, illetve üzeneteket fogadhat a Service Bus-előfizetés az új program [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) csomagot. Ezt a csomagot használja a gyorsabb [AMQP 1.0 protokoll](service-bus-amqp-overview.md) míg a korábbi [azure-sb](https://www.npmjs.com/package/azure-sb) használt csomag [Service Bus REST futásidejű API-k](/rest/api/servicebus/service-bus-runtime-rest). A minták JavaScript nyelven íródtak.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik egy üzenettémát és egy előfizetést szeretne dolgozni, kövesse lépéseket a [az Azure Portalon hozhat létre egy Service Bus témakörök és előfizetések](service-bus-quickstart-topics-subscriptions-portal.md) cikk kell létrehoznia őket. Jegyezze fel a kapcsolati karakterláncot a Service Bus-példány és a témakör és a létrehozott előfizetés nevét. A minták ezeket az értékeket fogjuk használni.

> [!NOTE]
> - Ebben az oktatóanyagban együttműködik a mintákat, másolja ki és futtathat [Nodejs](https://nodejs.org/). Node.js-alkalmazás létrehozásával kapcsolatos útmutatásért lásd: [hozzon létre és helyezhet üzembe egy Node.js-alkalmazás Azure-webhelyen](../app-service/app-service-web-get-started-nodejs.md), vagy [Node.js felhőalapú szolgáltatás Windows PowerShell-lel](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Az új [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) csomag topcis és-előfizetések létrehozása még nem támogatja. Használja a [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) csomag, ha azt szeretné, hozhat létre programozott módon őket.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
Service Bus számára, az npm-csomag telepítéséhez nyisson meg egy parancssort, amelynek `npm` annak elérési úton, módosítsa a könyvtárat a mappára, ahol szeretné a mintákban rendelkezik, és futtassa a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus szolgáltatással való interakcióhoz témakör kezdődik hárítható el a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály és a használatával hozza létre a [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) osztály. Miután a témakör ügyfél, létrehozhat egy küldő és választhat [küldése](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) vagy [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metódust, hogy üzeneteket küldjön.

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com/)
2. Hozzon létre egy fájlt nevű `send.js` , és illessze be az alábbi kódot oda. Ez a kód 10 üzenetet küld a témakörbe.

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
3. Adja meg a kapcsolati karakterláncot, és az üzenettéma nevére a fenti kódban.
4. Ezután futtassa a parancsot `node send.js` hajtsa végre a fájlt a parancssorban. 

Gratulálunk! Imént elküldött üzenetek Service Bus-üzenetsorba.

Üzenetek rendelkezik néhány alapvető tulajdonságok, például `label` és `messageId` , amely lehet küldésekor. Ha szeretne minden egyéni tulajdonságot, állítsa be, használja a `userProperties`, ez a kulcs-érték párokat az egyéni adatokat tárolhat json-objektum.

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. Nem található a témakörökben tárolt üzenetek száma korlátozott, de, a témakörök által tárolt üzenetek teljes mérete korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
A Service Bus szolgáltatással való interakcióhoz előfizetéssel kezdődik hárítható el a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály és a használatával hozza létre a [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) osztály. Ha az előfizetés-ügyfél, hozzon létre fogadót, és mindkét [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) vagy [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) üzenetek fogadásához metódust.

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com/)
2. Hozzon létre egy fájlt nevű `recieve.js` , és illessze be az alábbi kódot oda. Ez a kód próbálja meg 10 üzenetet fogadni az előfizetésből. Kap tényleges száma attól függ, hogy az előfizetés és a hálózati késés üzenetek száma.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
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
3. A kapcsolati karakterláncot, és a témakör és előfizetés nevét adja meg a fenti kódban.
4. Ezután futtassa a parancsot `node receiveMessages.js` hajtsa végre a fájlt a parancssorban.

Gratulálunk! Csak fogadott üzeneteket egy Service Bus-előfizetésből.

A [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) veszi a metódus egy `ReceiveMode` értékekkel enum azaz [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ne felejtse el [rendezi az üzenetek](message-transfers-locks-settlement.md#settling-receive-operations) használatakor a `PeekLock` mód egyikével `complete()`, `abandon()`, `defer()`, vagy `deadletter()` módszerek az üzenet.

## <a name="subscription-filters-and-actions"></a>Előfizetés-szűrők és műveletek
A Service Bus támogatja [szűrők és a műveletek az előfizetések](topic-filters.md), amely lehetővé teszi, hogy egy előfizetéshez a bejövő üzenetek szűrése és azok tulajdonságainak szerkesztése.

Ha már van egy példányát egy `SubscriptionClient` is használhatja az alábbi módszerek való beolvasása, adja hozzá, és távolítsa el az előfizetésben a szűrők és a műveletek a szabályokat.

- getRules
- addRule
- removeRule

Minden előfizetési csomaghoz tartozik egy alapértelmezett szabály, amely az összes bejövő üzenetek engedélyezése a valódi szűrőt használ. Amikor hozzáad egy új szabályt, ne felejtse el eltávolítani az alapértelmezett szűrő ahhoz, hogy a szűrőt az új szabály működik. Ha egy előfizetéshez nem tartozik szabály, akkor nincs üzeneteket fogja kapni.

> [!NOTE]
> A Service Bus-erőforrások is kezelhetők [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/). A Service Bus Explorer lehetővé teszi, hogy a felhasználók csatlakozni a Service Bus-névtér és üzenetküldési entitások felügyelete egyszerű módon. Az eszköz például importálás/exportálás funkció vagy tesztelhetik, témakör, üzenetsorok, előfizetések, relay-szolgáltatások, a notification hubs és események hubok speciális szolgáltatásokat biztosítja. 

## <a name="next-steps"></a>További lépések
További tudnivalókért lásd a következőket.

- [Queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md) (Üzenetsorok, témakörök és előfizetések)
- Egyéb [Nodejs a Githubon a Service Bus-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)


