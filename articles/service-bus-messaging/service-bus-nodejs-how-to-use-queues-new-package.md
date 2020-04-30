---
title: Az Azure/Service-Bus-várólisták használata a Node. js-ben
description: Megtudhatja, hogyan írhat NodeJS programot az üzenetek küldéséhez és fogadásához egy Service Bus-várólistából az @azure/service-bus új csomag használatával.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: c2e24e9dea2c8463294c85f04c9e4d7d2da17261
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330651"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Gyors útmutató: Service Bus Queues használata Node. js-sel és az Azure/Service-Bus csomaggal
Ebből az oktatóanyagból megtudhatja, hogyan írhat NodeJS programot az üzenetek küldésére és fogadására egy Service Bus-sorból az új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomag használatával. Ez a csomag a gyorsabb [AMQP 1,0 protokollt](service-bus-amqp-overview.md) használja, míg a régebbi [Azure-sb-](https://www.npmjs.com/package/azure-sb) csomag [Service Bus Rest futásidejű API-kat](/rest/api/servicebus/service-bus-runtime-rest)használ. A mintákat JavaScript nyelven írták.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket. Jegyezze fel a Service Bus példányának és a létrehozott várólista nevét. Ezeket az értékeket a mintákban fogjuk használni.

> [!NOTE]
> - Ez az oktatóanyag olyan példákkal működik, amelyeket a [NodeJS](https://nodejs.org/)használatával másolhat és futtathat. A Node. js-alkalmazások létrehozásával kapcsolatos útmutatásért lásd: [Node. js-alkalmazás létrehozása és telepítése Azure-webhelyre](../app-service/app-service-web-get-started-nodejs.md)vagy [Node. js felhőalapú szolgáltatás a Windows PowerShell használatával](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Az új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomag még nem támogatja a várólisták létrehozását. Ha programozott módon [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) szeretné létrehozni őket, használja a csomagot.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus NPM-csomagjának telepítéséhez nyisson meg egy parancssort, amely `npm` az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
A Service Bus üzenetsor használata a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály létrehozásával és a [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) osztály létrehozásával kezdődik. Ha már rendelkezik a várólista-ügyféllel, létrehozhat egy feladót, és használhatja a [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) vagy a [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metódust az üzenetek küldéséhez.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy `send.js` nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet küld a várólistába.

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
3. Adja meg a kapcsolatok karakterláncát és a várólista nevét a fenti kódban.
4. Ezután futtassa a parancsot `node send.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Csak egy Service Bus üzenetsor számára küldött üzeneteket.

Az üzenetek szabványos tulajdonságai, például `label` `messageId` a küldéskor megadhatók. Ha egyéni tulajdonságokat kíván beállítani, használja a `userProperties`parancsot, amely egy JSON-objektum, amely az egyéni adatai kulcs-érték párokat képes tárolni.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A várólistán lévő üzenetek száma nincs korlátozva, de a várólista által tárolt üzenetek teljes méretére vonatkozó korlát szerepel. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
A Service Bus üzenetsor használata a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály létrehozásával és a [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) osztály létrehozásával kezdődik. Ha már rendelkezik a várólista-ügyféllel, létrehozhat egy fogadót, és használhatja a [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) vagy a [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metódust is az üzenetek fogadásához.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy `recieve.js` nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet próbál fogadni a várólistából. A tényleges szám a várólistában lévő üzenetek számától és a hálózati késéstől függ.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
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
3. Adja meg a kapcsolatok karakterláncát és a várólista nevét a fenti kódban.
4. Ezután futtassa a parancsot `node receiveMessages.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Egy Service Bus üzenetsor üzeneteit fogadta.

A [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) metódus egy `ReceiveMode` , a [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)értékeket tartalmazó enumerálást vesz igénybe. Ne feledje, hogy [rendezze az üzeneteket](message-transfers-locks-settlement.md#settling-receive-operations) , ha a `PeekLock` módot használja az üzenet bármelyikének `abandon()` `defer()` `deadletter()` `complete()`, vagy metódusának használatával.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések
További tudnivalókat az alábbi forrásokban talál.
- [Queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md)
- Egyéb NodeJS-minták kifizetése a [githubon Service Bus](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

