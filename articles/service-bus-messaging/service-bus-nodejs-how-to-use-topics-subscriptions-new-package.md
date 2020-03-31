---
title: Azure/service-bus témakörök és előfizetések használata a Node.js használatával
description: 'Rövid útmutató: Ismerje meg, hogyan használhatja a Service Bus-témaköröket és előfizetéseket az Azure-ban egy Node.js alkalmazásból.'
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
ms.openlocfilehash: 6088b4c54ed16c5ef46d2c0671e619884cad29d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330617"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Rövid útmutató: A Service Bus-témakörök és előfizetések használata a Node.js és az azure/service-bus csomaggal
Ebben az oktatóanyagban megtudhatja, hogyan írhat node.js programot, amely üzeneteket küld egy [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) Service Bus-témakörnek, és üzeneteket fogadhat egy Service Bus-előfizetésből az új csomag használatával. Ez a csomag a gyorsabb [AMQP 1.0 protokollt](service-bus-amqp-overview.md) használja, míg a régebbi [azure-sb](https://www.npmjs.com/package/azure-sb) csomag [a Service Bus REST futásidejű API-jait](/rest/api/servicebus/service-bus-runtime-rest)használta. A minták JavaScript nyelven íródnak.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs témaköre és előfizetése a munka, kövesse az Azure Portal használata című [témakörlépéseit, és hozzon létre egy Service Bus-témakört és előfizetési](service-bus-quickstart-topics-subscriptions-portal.md) cikket azok létrehozásához. Vegye figyelembe a Service Bus-példány kapcsolati karakterláncát, valamint a létrehozott témakör és előfizetés nevét. Ezeket az értékeket fogjuk használni a mintákban.

> [!NOTE]
> - Ez az oktatóanyag olyan mintákkal működik, amelyeket [a Nodejs](https://nodejs.org/)használatával másolhat és futtathat. A Node.js alkalmazás létrehozásáról a [Node.js alkalmazás létrehozása és központi telepítése egy Azure-webhelyen](../app-service/app-service-web-get-started-nodejs.md)vagy [a Node.js felhőszolgáltatás a Windows PowerShell használatával című témakörben](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)talál.
> - Az [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) új csomag még nem támogatja a topcis és az előfizetések létrehozását. Kérjük, [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) használja a csomagot, ha programozott módon szeretné létrehozni őket.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus npm csomagjának telepítéséhez `npm` nyisson meg egy parancssort, amely az útjában van, módosítsa a könyvtárat arra a mappára, ahol a mintákat szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus-témakörrel való interakció a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály példányosításával kezdődik, és a [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) osztály példányosításával. Miután rendelkezik a témakör ügyféllel, létrehozhat egy feladót, és [üzenetek küldésére](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) vagy [elküldésérehasználhatja](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) a Küldés vagy a Batch módot.

1. A kedvenc szerkesztő, például a [Visual Studio-kód megnyitása](https://code.visualstudio.com/)
2. Hozzon létre `send.js` egy nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet küld a témának.

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
3. Írja be a kapcsolati karakterláncot és a témakör nevét a fenti mezőbe.
4. Ezután futtassa a parancsot `node send.js` egy parancssorban a fájl végrehajtásához. 

Gratulálunk! Csak üzeneteket küldött egy Service Bus-várólistába.

Az üzenetek nek `label` vannak `messageId` olyan szabványos tulajdonságai, mint például, amelyeket a küldéskor beállíthat. Ha egyéni tulajdonságokat szeretne beállítani, `userProperties`használja a , amely egy json objektum, amely képes az egyéni adatok kulcs-érték párjaitárolására.

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. Nincs korlátozva a témában tartott üzenetek száma, de a témakörben lévő üzenetek teljes mérete korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. A kvótákról további információt a [Service Bus-kvóták című témakörben talál.](service-bus-quotas.md)

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
A Service Bus-előfizetéssel való interakció a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály példányosításával kezdődik, és a [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) osztály példányosításával. Miután rendelkezik az előfizetési ügyféllel, létrehozhat egy fogadót, és [üzenetek fogadására](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) vagy registerMessageHandler metódusával fogadhatja a receiveMessages vagy [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metódust.

1. A kedvenc szerkesztő, például a [Visual Studio-kód megnyitása](https://code.visualstudio.com/)
2. Hozzon létre `recieve.js` egy nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet kísérel meg az előfizetéséből. A tényleges en kapott száma az előfizetésben és a hálózati késésben lévő üzenetek számától függ.

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
3. Írja be a kapcsolati karakterláncot és a témakör és az előfizetés nevét a fenti kódba.
4. Ezután futtassa a parancsot `node receiveMessages.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Most kapott üzeneteket egy Service Bus-előfizetéstől.

A [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) metódus `ReceiveMode` egy olyan felsorítást vesz fel, amelynek [értékei ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ne felejtse el [rendezni az üzeneteket,](message-transfers-locks-settlement.md#settling-receive-operations) ha `defer()`a `deadletter()` módot a `PeekLock` , `complete()` `abandon()`, vagy az üzenetben lévő módszerek bármelyikével használja.

## <a name="subscription-filters-and-actions"></a>Előfizetési szűrők és műveletek
A Service Bus támogatja [az előfizetések szűrőit és műveleteket,](topic-filters.md)amely lehetővé teszi a bejövő üzenetek szűrését egy előfizetésre, és szerkesztheti azok tulajdonságait.

Miután rendelkezik egy `SubscriptionClient` példányt, használhatja az alábbi módszereket, hogy levegye, adja hozzá és távolítsa el a szabályokat az előfizetésen a szűrők és műveletek szabályozásához.

- getRules
- addRule
- eltávolításI szabály

Minden előfizetésrendelkezik egy alapértelmezett szabállyal, amely a valódi szűrőt használja az összes bejövő üzenet engedélyezéséhez. Amikor új szabályt ad hozzá, ne felejtse el eltávolítani az alapértelmezett szűrőt annak érdekében, hogy az új szabályszűrője működjön. Ha egy előfizetésnem rendelkezik szabályokkal, akkor nem kap üzeneteket.

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>Következő lépések
További információ: a következő források.

- [Várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
- Pénztár más [Nodejs-minták a Service Bus-hoz a GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)


