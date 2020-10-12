---
title: Az Azure üzenetsor-tároló használata Node.js – Azure Storage-ból
description: Ismerje meg, hogyan hozhat létre és törölhet várólistákat az Azure Queue szolgáltatás használatával. Megtudhatja, hogyan szúrhat be, kérhet le és törölhet üzeneteket Node.js használatával.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 77c35ae4b9e845cd3c0f638407c0d71c36fcf9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289684"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Az Azure üzenetsor-tároló használata a Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket a Microsoft Azure Queue szolgáltatás használatával. A minták a Node.js API használatával íródnak. A tárgyalt forgatókönyvek közé tartozik az üzenetsor-üzenetek beszúrása, bepillantása, beolvasása és törlése. Emellett megtudhatja, hogyan hozhat létre és törölhet várólistákat.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

Ha üres Node.js alkalmazást szeretne létrehozni, tekintse meg [a Node.js Webalkalmazás létrehozása Azure app Serviceban][Create a Node.js web app in Azure App Service]című témakört, amely [egy Node.js alkalmazás Azure Cloud Service-be történő létrehozását és üzembe helyezését segíti elő][Build and deploy a Node.js application to an Azure Cloud Service] a Windows PowerShell vagy a [Visual Studio Code][Visual Studio Code]használatával.

## <a name="configure-your-application-to-access-storage"></a>Az alkalmazás konfigurálása a tároló elérésére

A [javascripthez készült Azure Storage ügyféloldali kódtára][Azure Storage client library for JavaScript] a tárolási Rest-szolgáltatásokkal kommunikáló kényelmi könyvtárak készletét tartalmazza.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Node Package Managert (NPM)

1. Olyan parancssori felületet használjon, mint például a PowerShell (Windows), a Terminal (Mac) vagy a bash (UNIX), Navigáljon arra a mappára, ahol létrehozta a minta alkalmazást.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

1. Írja be a **NPM az \@ Azure/Storage-üzenetsor telepítése** parancsot a parancsablakban.

1. Ellenőrizze, hogy létrejött-e a **csomópont- \_ modulok** mappája. A mappában megtalálja az ** \@ Azure/Storage-üzenetsor** csomagot, amely tartalmazza a tárterület eléréséhez szükséges ügyféloldali kódtárat.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Írja be az **npm install azure-storage** kifejezést a parancsablakba.

1. Ellenőrizze, hogy létrejött-e a **csomópont- \_ modulok** mappája. A mappában megtalálja az **Azure-Storage** csomagot, amely tartalmazza a tárolóhoz hozzáférő kódtárakat.

---

### <a name="import-the-package"></a>A csomag importálása

A Kódszerkesztő használatával adja hozzá a következőt a JavaScript-fájlhoz, ahol a várólistákat használni kívánja.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Üzenetsor létrehozása

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

A következő kód lekéri egy nevű környezeti változó értékét, `AZURE_STORAGE_CONNECTION_STRING` és egy [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) objektum létrehozásához használja. A rendszer ezután a **QueueServiceClient** objektumot használja egy [QueueClient](/javascript/api/@azure/storage-queue/queueclient) objektum létrehozásához. A **QueueClient** objektum lehetővé teszi, hogy egy adott várólistával működjön.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Ha a várólista már létezik, kivételt dobott a rendszer.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Az Azure-modul beolvassa a környezeti változókat `AZURE_STORAGE_ACCOUNT` `AZURE_STORAGE_ACCESS_KEY` , illetve `AZURE_STORAGE_CONNECTION_STRING` Az Azure Storage-fiókhoz való kapcsolódáshoz szükséges információkat. Ha a környezeti változók nincsenek beállítva, a **createQueueService**meghívásakor meg kell adnia a fiók adatait.

A következő kód létrehoz egy **QueueService** objektumot, amely lehetővé teszi a várólistákkal való munkavégzést.

```javascript
var queueSvc = azure.createQueueService();
```

A **createQueueIfNotExists** metódus meghívásával hozzon létre egy új várólistát a megadott névvel, vagy adja vissza a várólistát, ha az már létezik.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Ha a várólista létrejött, `result.created` igaz. Ha a várólista létezik, a `result.created` hamis.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Üzenet beszúrása egy várólistába

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Ha üzenetet szeretne hozzáadni egy várólistához, hívja meg a [üzenetküldés](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metódust.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Ha üzenetet szeretne beszúrni egy várólistába, hívja meg a **createMessage** metódust egy új üzenet létrehozásához és a várólistához való hozzáadásához.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Betekintés a következő üzenetbe

A várólistán lévő üzenetekben anélkül is betekintést nyerhet, hogy a **peekMessages** metódus meghívásával eltávolítja őket a várólistából.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Alapértelmezés szerint a [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) egyetlen üzenetben jelenik meg. A következő példa a várólista első öt üzenetében betekintést mutat be. Ha ötnél kevesebb üzenet látható, akkor a rendszer csak a látható üzeneteket adja vissza.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Alapértelmezés szerint a **peekMessages** egyetlen üzenetben jelenik meg.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

A `result` tartalmazza az üzenetet.

---

A **peekMessages** meghívása, ha a várólistában nem található üzenetek hibaüzenetet adnak vissza. Azonban a rendszer nem ad vissza üzeneteket.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Várólistán lévő üzenet tartalmának módosítása

Az alábbi példa egy üzenet szövegét frissíti.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

A [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-)meghívásával módosítsa a várólistán lévő üzenet tartalmát. 

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

A **updateMessage**meghívásával módosítsa a várólistán lévő üzenet tartalmát. 

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Üzenet leállítása

Az üzenet egy kétfázisú folyamat:

1. Az üzenet beolvasása.

1. Törölje az üzenetet.

A következő példa egy üzenetet kap, majd törli.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Ha üzenetet szeretne kapni, hívja meg a [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metódust. A hívás során az üzenetek láthatatlanok maradnak a várólistában, így más ügyfelek nem tudják feldolgozni azokat. Miután az alkalmazás feldolgozta az üzenetet, a [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) meghívásával törölje azt a várólistából.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Alapértelmezés szerint az üzenetek csak 30 másodpercig vannak elrejtve. 30 másodperc elteltével más ügyfelek is láthatják. A **receiveMessages**hívásakor megadhat egy másik értéket a [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) beállításával.

A **receiveMessages** meghívása, ha a várólistában nem található üzenetek hibaüzenetet adnak vissza. Azonban a rendszer nem ad vissza üzeneteket.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Ha üzenetet szeretne kapni, hívja meg a **getMessages** metódust. A hívás során az üzenetek láthatatlanok maradnak a várólistában, így más ügyfelek nem tudják feldolgozni azokat. Miután az alkalmazás feldolgozta az üzenetet, a **deleteMessage** meghívásával törölje azt a várólistából.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Alapértelmezés szerint az üzenetek csak 30 másodpercig vannak elrejtve. 30 másodperc elteltével más ügyfelek is láthatják. A with GetMessages használatával más értéket is megadhat `options.visibilityTimeout` . **getMessages**

A **getMessages** használata, ha a várólistában nem található üzenetek nem adnak vissza hibát. Azonban a rendszer nem ad vissza üzeneteket.

---

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások a dequeuing üzenetek számára

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Az üzenetek lekérését kétféleképpen lehet testreszabni egy várólistából:

* [Options. numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) – üzenetek kötegének beolvasása (legfeljebb 32.)
* [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) – nagyobb vagy rövidebb láthatósági időtúllépést állíthat be.

A következő példa a **receiveMessages** metódus használatával öt üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `for` hurok használatával. Emellett a metódus által visszaadott összes üzenet esetében öt percre állítja be a láthatósági időtúllépést.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Az üzenetek lekérését kétféleképpen lehet testreszabni egy várólistából:

* `options.numOfMessages` – Üzenetek kötegének beolvasása (legfeljebb 32.)
* `options.visibilityTimeout` -Egy többé vagy rövidebb láthatósági időkorlát beállítása.

A következő példa a **getMessages** metódus használatával 15 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `for` hurok használatával. Emellett a metódus által visszaadott összes üzenet esetében öt percre állítja be a láthatósági időtúllépést.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>A várólista hosszának beolvasása

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

A [getProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) metódus a várólistához tartozó metaadatokat adja vissza, beleértve a várólistán várakozó üzenetek hozzávetőleges számát is.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

A **getQueueMetadata** metódus a várólistához tartozó metaadatokat adja vissza, beleértve a várólistán várakozó üzenetek hozzávetőleges számát is.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Várólisták listázása

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

A várólisták listájának lekéréséhez hívja meg a [QueueServiceClient. listQueues](). Egy adott előtag által szűrt lista lekéréséhez adja meg a [Options. prefix beállítást](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) a **listQueues**-hívásban.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

A várólisták listájának lekéréséhez használja a **listQueuesSegmented**. Egy adott előtag által szűrt lista lekéréséhez használja a **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Ha az összes várólistát nem lehet visszaadni, adja át `result.continuationToken` a **listQueuesSegmented** első paraméterét vagy a **listQueuesSegmentedWithPrefix** második paraméterét a további találatok lekéréséhez.

---

## <a name="how-to-delete-a-queue"></a>Üzenetsor törlése

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a [deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metódust a **QueueClient** objektumon.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Ha törölni szeretné a várólista összes üzenetét anélkül, hogy törölné, hívja meg a [clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-).

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **deleteQueue** metódust a várólista-objektumon.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Ha törölni szeretné a várólista összes üzenetét anélkül, hogy törölné, hívja meg a **clearMessages**.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a várólista-tárolás alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

* Az Újdonságok megismeréséhez látogasson el az [Azure Storage csapatának blogjára][Azure Storage Team Blog] .
* Látogasson el az [Azure Storage ügyféloldali kódtárara][Azure Storage client library for JavaScript] a githubon található JavaScript-adattárhoz

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
