---
title: 'Rövid útmutató: Az Azure Queue storage tára v12 – JavaScript'
description: Ismerje meg, hogyan használhatja az Azure Queue JavaScript v12-kódtár segítségével egy várólista létrehozásához és üzenetek hozzáadásához a várólistához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet idát várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 59a5308d2c0a1fa2e1f38f2fe3da3a2cc29448be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199784"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Rövid útmutató: Azure Queue storage ügyfélkódtár v12 JavaScripthez

Ismerkedés az Azure Queue storage ügyféltár 12-es javascript-verziójával. Az Azure Queue storage egy olyan szolgáltatás, amely nagy számú üzenetet tárol későbbi lekéréshez és feldolgozáshoz. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

Használja az Azure Queue storage ügyfélkódtár v12 JavaScript hez:

* Üzenetsor létrehozása
* Üzenetek hozzáadása várólistához
* Betekintés a várólistában lévő üzenetekbe
* Üzenet frissítése a várólistában
* Üzenetek fogadása várólistából
* Üzenetek törlése várólistából
* Üzenetsor törlése

[API-referenciadokumentáció–](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | [forráskód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [csomag (node package manager)](https://www.npmjs.com/package/@azure/storage-queue) | [minták](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure storage-fiók – [tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Az operációs rendszer aktuális [Node.js fájljai.](https://nodejs.org/en/download/)

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Queue storage ügyfélkódtár v12 JavaScript.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *queues-quickstart-v12*nevű Node.js alkalmazásnevet.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Váltson át az újonnan létrehozott *várólisták-quickstart-v12* könyvtárra.

    ```console
    cd queues-quickstart-v12
    ```

1. Hozzon létre egy *package.json*nevű új szövegfájlt. Ez a fájl határozza meg a Node.js projektet. Mentse a fájlt a *várólisták-quickstart-v12* könyvtárba. Itt van a fájl tartalma:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Ha szeretné, beírhatja `author` a saját nevét a mezőbe.

### <a name="install-the-package"></a>A csomag telepítése

Miközben továbbra is a *várólisták-quickstart-v12* könyvtárban, telepítse az Azure Queue `npm install` storage ügyfélrendszertár JavaScript-csomag a parancs használatával.

```console
npm install
```

 Ez a parancs beolvassa a *package.json* fájlt, és telepíti az Azure Queue storage ügyfélkódtár v12 JavaScript-csomag és az összes olyan kódtár, amelytől függ.

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. Másik új szövegfájl megnyitása a kódszerkesztőben
1. Az `require` Azure és a Node.js modulok betöltéséhez kezdeményezett hívások hozzáadása
1. A program struktúrájának létrehozása, beleértve a legalapvetőbb kivételkezelést

    Itt a kód:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Mentse az új fájlt *várólista-quickstart-v12.js* a *várólista-quickstart-v12* könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. A várólista-üzenet mérete legfeljebb 64 KB lehet. Egy várólista több millió üzenetet tartalmazhat, a tárfiók teljes kapacitáskorlátjáig. A várólistákat gyakran használják az aszinkron feldolgozáshoz használt munka hátralékának létrehozására. A várólista-tárolás háromféle erőforrást kínál:

* A tárfiók
* Várólista a tárfiókban
* Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Várólista tárolási architektúrájának diagramja](./media/storage-queues-introduction/queue1.png)

Az alábbi JavaScript-osztályok segítségével kezelheti ezeket az erőforrásokat:

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` A lehetővé teszi a tárfiók összes várólistájának kezelését.
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): `QueueClient` Az osztály lehetővé teszi egy adott várólista és üzenetének kezelését és kezelését.
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` Az osztály a [várólistán lévő receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) hívásakor visszaadott egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan teheti meg a következő műveleteket az Azure Queue storage ügyfélkódtárjától javascripthez:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Üzenetsor létrehozása](#create-a-queue)
* [Üzenetek hozzáadása várólistához](#add-messages-to-a-queue)
* [Betekintés a várólistában lévő üzenetekbe](#peek-at-messages-in-a-queue)
* [Üzenet frissítése a várólistában](#update-a-message-in-a-queue)
* [Üzenetek fogadása várólistából](#receive-messages-from-a-queue)
* [Üzenetek törlése várólistából](#delete-messages-from-a-queue)
* [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát a [tárolási kapcsolat karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból.

Adja hozzá ezt `main` a kódot a függvényhez:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új várólista nevét. Az alábbi kód hozzáfűz egy UUID értéket a várólista nevéhez annak érdekében, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólistanevek csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődniük. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 és 63 karakter közötti nek is kell lennie. A várólisták elnevezéséről további információt az [Elnevezési várólisták és a metaadatok](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)című témakörben talál.

Hozzon létre egy példányt a [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) osztályból. Ezután hívja meg a [létrehozási](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) módszert a várólista létrehozásához a tárfiókban.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása várólistához

A következő kódrészlet a [sendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metódus hívásával üzeneteket ad a várólistához. Menti a harmadik `sendMessage` hívásból visszaadott [QueueMessage-et](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) is. A `sendMessageResponse` visszaküldött üzenet a program későbbi részében az üzenet tartalmának frissítésére szolgál.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés a várólistában lévő üzenetekbe

A [peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) metódus meghívásával tekintsünk be a várólistában lévő üzenetekbe. A `peekMessages` metódus egy vagy több üzenetet olvas le a várólista elejéről, de nem változtatja meg az üzenet láthatóságát.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Üzenet frissítése a várólistában

Az [updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) metódus felhívásával frissítheti az üzenet tartalmát. A `updateMessage` módszer módosíthatja az üzenetek láthatósági időtúlságát és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely legfeljebb 64 KB méretű. Az új tartalommellett adja `messageId` `popReceipt` át a kódot korábban mentett választ. A `sendMessageResponse` tulajdonságok azonosítják a frissítandó üzenetet.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából

Töltse le a korábban hozzáadott üzeneteket a [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metódus felhívásával.  A `numberOfMessages` mezőben adja meg a híváshoz legfeljebb fogadott üzenetek számát.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése várólistából

Üzenetek törlése a várólistából a fogadás és feldolgozás után. Ebben az esetben a feldolgozás csak az üzenet megjelenítése a konzolon.

Az üzenetek törlése a [deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) metódus hívásával. A kifejezetten nem törölt üzenetek végül ismét láthatóvá válnak a várólistában, hogy újabb esélyt kaphassanak azok feldolgozására.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód törli az okat az erőforrásokat, amelyeket az alkalmazás a várólista [törlési](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) módszerrel történő törlésével hozott létre.

Adja hozzá ezt a `main` kódot a függvény végéhez, és mentse a fájlt:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre, és három üzenetet ad hozzá egy Azure-várólistához. A kód felsorolja az üzeneteket a várólistában, majd beolvassa és törli őket, mielőtt véglegesen törli a várólistát.

A konzolablakban keresse meg a *várólista-quickstart-v12.js* fájlt tartalmazó `node` könyvtárat, majd hajtsa végre a következő parancsot az alkalmazás futtatásához.

```console
node queues-quickstart-v12.js
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Lépjen végig a hibakeresőben lévő kódon, és ellenőrizze az [Azure Portalt](https://portal.azure.com) a folyamat során. Ellenőrizze a tárfiókban, hogy ellenőrizze, hogy a várólistában lévő üzenetek létrejönnek és törlődnek.Check your storage account to verify messages in the queue are created and deleted.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket JavaScript-kód használatával. Ezután megtanulta betekinteni, beolvasni és törölni az üzeneteket. Végül megtanulta, hogyan kell törölni egy üzenetvárólistát.

Oktatóanyagok, minták, gyorsindítások és egyéb dokumentációk esetén látogasson el a következő oldalra:

> [!div class="nextstepaction"]
> [Az Azure for JavaScript dokumentációja](https://docs.microsoft.com/azure/javascript/)

* További információ: [Az Azure Storage Queue ügyfélkódtár javascripthez.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
* További Azure Queue storage mintaalkalmazások megtekintéséhez folytassa az [Azure Queue storage ügyfélkódtár v12 JavaScript-mintáival.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)
