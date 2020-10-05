---
title: Azure-és Service-Bus-témakörök és-előfizetések használata Node.js
description: 'Gyors útmutató: megtudhatja, hogyan használhatja Service Bus témaköröket és előfizetéseket az Azure-ban egy Node.js alkalmazásból.'
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 4a1bb3346d50825dcab799477794cb138ac2de91
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326286"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata Node.js és az Azure/Service-Bus csomaggal
Ebből az oktatóanyagból megtudhatja, hogyan írhat egy Node.js programot, amely üzeneteket küld egy Service Bus témakörnek, és üzeneteket fogad egy Service Bus előfizetésből az új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomag használatával. Ez a csomag a gyorsabb [AMQP 1,0 protokollt](service-bus-amqp-overview.md) használja, míg a régebbi [Azure-sb-](https://www.npmjs.com/package/azure-sb) csomag [Service Bus Rest futásidejű API-kat](/rest/api/servicebus/service-bus-runtime-rest)használ. A mintákat JavaScript nyelven írták.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik a témával és az előfizetéssel, akkor a létrehozásához kövesse a [Use Azure Portal (Service Bus témakörök és előfizetések](service-bus-quickstart-topics-subscriptions-portal.md) létrehozása című cikk lépéseit). Jegyezze fel a Service Bus-példányhoz tartozó kapcsolatok karakterláncát, valamint a létrehozott témakör és előfizetés nevét. Ezeket az értékeket a mintákban fogjuk használni.

> [!NOTE]
> - Ez az oktatóanyag olyan példákkal működik, amelyeket a [NodeJS](https://nodejs.org/)használatával másolhat és futtathat. Node.js alkalmazások létrehozásával kapcsolatos utasításokért lásd: [Node.js alkalmazás létrehozása és telepítése Azure-webhelyre](../app-service/quickstart-nodejs.md), vagy [Node.js Cloud Service a Windows PowerShell használatával](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Az új [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomag még nem támogatja a topcis és-előfizetések létrehozását. [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)Ha programozott módon szeretné létrehozni őket, használja a csomagot.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus NPM-csomagjának telepítéséhez nyisson meg egy parancssort, amely `npm` az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus témakörrel való interakció a [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) osztály létrehozásával kezdődik, és a használatával a [TopicClient](/javascript/api/@azure/service-bus/topicclient) osztály létrehozásához használatos. Miután megkapta a témakör-ügyfelet, létrehozhat egy küldőt, és használhatja a [Send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) vagy a [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metódust az üzenetek küldéséhez.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy nevű fájlt `send.js` , és illessze be az alábbi kódot. Ez a kód 10 üzenetet küld a témakörnek.

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
4. Ezután futtassa a parancsot `node send.js` egy parancssorban a fájl végrehajtásához. 

Gratulálunk! Csak egy Service Bus üzenetsor számára küldött üzeneteket.

Az üzenetek szabványos tulajdonságai, például a küldéskor megadhatók `label` `messageId` . Ha egyéni tulajdonságokat kíván beállítani, használja a parancsot `userProperties` , amely egy JSON-objektum, amely az egyéni adatai kulcs-érték párokat képes tárolni.

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A témakörben tárolt üzenetek száma nincs korlátozva, de a témakörben tárolt üzenetek teljes mérete korlátozott. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
A Service Bus-előfizetésekkel való interakció a [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) osztály létrehozásával és a [SubscriptionClient](/javascript/api/@azure/service-bus/subscriptionclient) osztály létrehozásához használattal kezdődik. Ha már rendelkezik az előfizetési ügyféllel, létrehozhat egy fogadót, és használhat [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) vagy [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metódust is az üzenetek fogadásához.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy nevű fájlt `receive.js` , és illessze be az alábbi kódot. Ez a kód 10 üzenetet próbál fogadni az előfizetésből. A kapott tényleges darabszám az előfizetésben és a hálózati késésben lévő üzenetek számától függ.

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
4. Ezután futtassa a parancsot `node receive.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Csak Service Bus-előfizetésből érkezett üzenetek.

A [createReceiver](/javascript/api/@azure/service-bus/subscriptionclient#createreceiver-receivemode-) metódus egy, a `ReceiveMode` [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és a [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)értékeket tartalmazó enumerálást vesz igénybe. Ne feledje, hogy [rendezze az üzeneteket](message-transfers-locks-settlement.md#settling-receive-operations) , ha a módot használja az `PeekLock` üzenet bármelyikének, `complete()` `abandon()` `defer()` vagy `deadletter()` metódusának használatával.

## <a name="subscription-filters-and-actions"></a>Előfizetési szűrők és műveletek
Service Bus támogatja az [előfizetések szűrőit és műveleteit](topic-filters.md), így a beérkező üzeneteket szűrheti egy előfizetésre, és szerkesztheti a tulajdonságait.

Ha már rendelkezik egy példányával `SubscriptionClient` , használhatja az alábbi metódusokat az előfizetéshez tartozó szabályok beszerzéséhez, hozzáadásához és eltávolításához a szűrők és műveletek vezérléséhez.

- getRules
- addRule
- removeRule

Minden előfizetéshez tartozik egy alapértelmezett szabály, amely a True szűrőt használja az összes bejövő üzenet engedélyezéséhez. Új szabály hozzáadásakor ne felejtse el eltávolítani az alapértelmezett szűrőt, hogy a szűrő az új szabályban működjön. Ha egy előfizetéshez nem tartozik szabály, akkor nem fog üzenetet kapni.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
További tudnivalókat az alábbi forrásokban talál.

- [Queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md)
- Egyéb NodeJS-minták kifizetése a [githubon Service Bus](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)
