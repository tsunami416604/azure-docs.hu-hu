---
title: Az Azure Service Bus-üzenetsorok használata a node.js-szel – azure/service-bus |} A Microsoft Docs
description: Megtudhatja, hogyan használhatja a Service Bus-üzenetsorok az Azure Node.js-alkalmazásból.
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
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988357"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Service Bus-üzenetsorok használata a Node.js és az azure/szolgáltatásbusz csomag
> [!div class="op_multi_selector" title1="Programozási nyelv" title2="Node.js pacakge"]
> - [(Node.js |} azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js |} @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Ebben az oktatóanyagban elsajátíthatja, hogyan küldhet üzeneteket, és üzeneteket fogad egy Service Bus-üzenetsorba, az új Nodejs program írása [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) csomagot. Ezt a csomagot használja a gyorsabb [AMQP 1.0 protokoll](service-bus-amqp-overview.md) míg a korábbi [azure-sb](https://www.npmjs.com/package/azure-sb) használt csomag [Service Bus REST futásidejű API-k](/rest/api/servicebus/service-bus-runtime-rest). A minták JavaScript nyelven íródtak.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik egy üzenetsorba való együttműködéshez, kövesse lépéseket a [egy Service Bus-üzenetsor létrehozása az Azure portal](service-bus-quickstart-portal.md) várólista létrehozásához a cikkben. Jegyezze fel a kapcsolati karakterláncot a Service Bus-példány és a létrehozott üzenetsor neve. A minták ezeket az értékeket fogjuk használni.

> [!NOTE]
> - Ebben az oktatóanyagban együttműködik a mintákat, másolja ki és futtathat [Nodejs](https://nodejs.org/). Node.js-alkalmazás létrehozásával kapcsolatos útmutatásért lásd: [hozzon létre és helyezhet üzembe egy Node.js-alkalmazás Azure-webhelyen](../app-service/app-service-web-get-started-nodejs.md), vagy [Node.js felhőalapú szolgáltatás Windows PowerShell-lel](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Az új [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) csomag nem támogatja az üzenetsorok létrehozása még. Használja a [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) csomag, ha azt szeretné, hozhat létre programozott módon őket.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
Service Bus számára, az npm-csomag telepítéséhez nyisson meg egy parancssort, amelynek `npm` annak elérési úton, módosítsa a könyvtárat a mappára, ahol szeretné a mintákban rendelkezik, és futtassa a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
A Service Bus szolgáltatással való interakcióhoz várólista kezdődik hárítható el a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály és a használatával hozza létre a [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) osztály. Ha az üzenetsor-ügyfél, hozzon létre egy küldő, és mindkét [küldése](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) vagy [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metódust, hogy üzeneteket küldjön.

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com/)
2. Hozzon létre egy fájlt nevű `send.js` , és illessze be az alábbi kódot oda. Ez a kód 10 üzenetet küld az üzenetsorba.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
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
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Adja meg a kapcsolati karakterláncot, és az üzenetsor nevére a fenti kódban.
4. Ezután futtassa a parancsot `node send.js` hajtsa végre a fájlt a parancssorban.

Gratulálunk! Imént elküldött üzenetek Service Bus-üzenetsorba.

Üzenetek rendelkezik néhány alapvető tulajdonságok, például `label` és `messageId` , amely lehet küldésekor. Ha szeretne minden egyéni tulajdonságot, állítsa be, használja a `userProperties`, ez a kulcs-érték párokat az egyéni adatokat tárolhat json-objektum.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. Nem egy üzenetsorban tárolt üzenetek száma korlátozott, de korlátozva van az üzenetsor által tárolt üzenetek teljes mérete. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
A Service Bus szolgáltatással való interakcióhoz várólista kezdődik hárítható el a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály és a használatával hozza létre a [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) osztály. Ha az üzenetsor-ügyfél, hozzon létre fogadót, és mindkét [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) vagy [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) üzenetek fogadásához metódust.

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com/)
2. Hozzon létre egy fájlt nevű `recieve.js` , és illessze be az alábbi kódot oda. Ez a kód próbálja meg 10 üzenetek fogadása az üzenetsorból. Kap tényleges száma attól függ, az üzenetek száma, az üzenetsor és a hálózati késést.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Adja meg a kapcsolati karakterláncot, és az üzenetsor nevére a fenti kódban.
4. Ezután futtassa a parancsot `node receiveMessages.js` hajtsa végre a fájlt a parancssorban.

Gratulálunk! Csak fogadott üzeneteket egy Service Bus-üzenetsorba érkező.

A [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) veszi a metódus egy `ReceiveMode` értékekkel enum azaz [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ne felejtse el [rendezi az üzenetek](message-transfers-locks-settlement.md#settling-receive-operations) használatakor a `PeekLock` mód egyikével `complete()`, `abandon()`, `defer()`, vagy `deadletter()` módszerek az üzenet.

> [!NOTE]
> A Service Bus-erőforrások is kezelhetők [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/). A Service Bus Explorer lehetővé teszi, hogy a felhasználók csatlakozni a Service Bus-névtér és üzenetküldési entitások felügyelete egyszerű módon. Az eszköz például importálás/exportálás funkció vagy tesztelhetik, témakör, üzenetsorok, előfizetések, relay-szolgáltatások, a notification hubs és események hubok speciális szolgáltatásokat biztosítja. 

## <a name="next-steps"></a>További lépések
További tudnivalókért lásd a következőket.
- [Queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md) (Üzenetsorok, témakörök és előfizetések)
- Egyéb [Nodejs a Githubon a Service Bus-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

