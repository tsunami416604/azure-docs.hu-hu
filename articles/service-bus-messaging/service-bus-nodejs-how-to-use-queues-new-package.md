---
title: Az azure/service-bus várólisták használata a Node.js webhelyen
description: Megtudhatja, hogyan írhat Nodejs programot üzenetek küldéséhez és fogadásához @azure/service-bus egy Service Bus-várólistából az új csomag használatával.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330651"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Rövid útmutató: A Service Bus-várólisták használata a Node.js és az azure/service-bus csomag használatával
Ebben az oktatóanyagban megtudhatja, hogyan írhat nodejs programot üzenetek küldéséhez és [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) fogadásához egy Service Bus-várólistából az új csomag használatával. Ez a csomag a gyorsabb [AMQP 1.0 protokollt](service-bus-amqp-overview.md) használja, míg a régebbi [azure-sb](https://www.npmjs.com/package/azure-sb) csomag [a Service Bus REST futásidejű API-jait](/rest/api/servicebus/service-bus-runtime-rest)használta. A minták JavaScript nyelven íródnak.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs várólistája, amivel dolgozhatna, kövesse az Azure Portal használata című lépéseit [a Service Bus-várólista-cikk létrehozásához](service-bus-quickstart-portal.md) egy várólista létrehozásához. Jegyezze fel a Service Bus-példány kapcsolati karakterláncát és a létrehozott várólista nevét. Ezeket az értékeket fogjuk használni a mintákban.

> [!NOTE]
> - Ez az oktatóanyag olyan mintákkal működik, amelyeket [a Nodejs](https://nodejs.org/)használatával másolhat és futtathat. A Node.js alkalmazás létrehozásáról a [Node.js alkalmazás létrehozása és központi telepítése egy Azure-webhelyen](../app-service/app-service-web-get-started-nodejs.md)vagy [a Node.js felhőszolgáltatás ban a Windows PowerShell használatával](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)című témakörben talál.
> - Az [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) új csomag még nem támogatja a várólisták létrehozását. Kérjük, [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) használja a csomagot, ha programozott módon szeretné létrehozni őket.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus npm csomagjának telepítéséhez `npm` nyisson meg egy parancssort, amely az útjában van, módosítsa a könyvtárat arra a mappára, ahol a mintákat szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
A Service Bus-várólistával való interakció a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály példányosításával kezdődik, és a [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) osztály példányosításával. Miután rendelkezik a várólista-ügyféllel, létrehozhat egy feladót, és üzenetek küldésére vagy elküldésére használhatja a [küldési](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) vagy [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metódust.

1. A kedvenc szerkesztő, például a [Visual Studio-kód megnyitása](https://code.visualstudio.com/)
2. Hozzon létre `send.js` egy nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet küld a várólistára.

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
3. Írja be a kapcsolati karakterláncot és a várólista nevét a fenti kódba.
4. Ezután futtassa a parancsot `node send.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Csak üzeneteket küldött egy Service Bus-várólistába.

Az üzenetek nek `label` vannak `messageId` olyan szabványos tulajdonságai, mint például, amelyeket a küldéskor beállíthat. Ha egyéni tulajdonságokat szeretne beállítani, `userProperties`használja a , amely egy json objektum, amely képes az egyéni adatok kulcs-érték párjaitárolására.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. Nincs korlátozva a várólistában tartott üzenetek száma, de van egy korlát a várólistában lévő üzenetek teljes méretét. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. A kvótákról további információt a [Service Bus-kvóták című témakörben talál.](service-bus-quotas.md)

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából
A Service Bus-várólistával való interakció a [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) osztály példányosításával kezdődik, és a [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) osztály példányosításával. Miután rendelkezik a várólista-ügyféllel, létrehozhat egy fogadót, és [üzenetek fogadására vagy](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) registerMessageHandler metódusával fogadhatja a receiveMessage-et vagy a [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metódust.

1. A kedvenc szerkesztő, például a [Visual Studio-kód megnyitása](https://code.visualstudio.com/)
2. Hozzon létre `recieve.js` egy nevű fájlt, és illessze be az alábbi kódot. Ez a kód 10 üzenetet kísérel meg fogadni a várólistából. A tényleges enkapott szám a várólistában lévő üzenetek számától és a hálózati késéstől függ.

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
3. Írja be a kapcsolati karakterláncot és a várólista nevét a fenti kódba.
4. Ezután futtassa a parancsot `node receiveMessages.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Most kapott üzeneteket egy Service Bus-várólistából.

A [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) metódus `ReceiveMode` egy olyan felsorítást vesz fel, amelynek [értékei ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) és [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Ne felejtse el [rendezni az üzeneteket,](message-transfers-locks-settlement.md#settling-receive-operations) ha `defer()`a `deadletter()` módot a `PeekLock` , `complete()` `abandon()`, vagy az üzenetben lévő módszerek bármelyikével használja.

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
További információ: a következő források.
- [Várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
- Pénztár más [Nodejs-minták a Service Bus-hoz a GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)

