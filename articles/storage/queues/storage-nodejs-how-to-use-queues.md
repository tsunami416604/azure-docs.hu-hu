---
title: "Node.js-ből a Queue storage használata |} Microsoft Docs"
description: "Útmutató az Azure Queue szolgáltatás segítségével hozza létre, és törli az üzenetsorok, és helyezze, get, és törli az üzenetet. A minta Node.js nyelven írt."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 97522abd05d60eeaa2cc8dd07d3ab81d7f1d5fb9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást a Microsoft Azure Queue szolgáltatást használó általános forgatókönyvhöz. A minták íródtak, a Node.js API használatával. Az ismertetett forgatókönyvek **beszúrása**, **megtekintésekor**, **első**, és **törlése** üzenetek várólistára, valamint **létrehozása és törlése várólisták**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js-alkalmazás létrehozása
Üres Node.js-alkalmazás létrehozása. A Node.js-alkalmazás létrehozása utasításokért lásd: [Node.js-webalkalmazás létrehozása az Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [létrehozása és központi telepítése egy Azure Cloud Service a Node.js-alkalmazás](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) Windows PowerShell használatával vagy [hozza létre, és a Node.js webalkalmazás telepítése az Azure-ban a Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Állítsa be az alkalmazását tároló elérése érdekében
Az Azure storage használatához szüksége az Azure Storage szolgáltatás SDK a Node.js, amely tartalmaz egy kényelmi szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Csomópont Package Manager (NPM) használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac) vagy **Bash** (Unix), lépjen abba a mappába, amelyben létrehozta a mintaalkalmazáshoz.
2. Típus **npm telepítése azure-tároló** a parancsablakban. A parancs kimenetében a következőhöz hasonló.
 
    ```
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Manuálisan futtatható a **ls** parancs futtatásával ellenőrizze, hogy egy **csomópont\_modulok** mappa hozták létre. A mappában megtalálja az **azure-tároló** csomagot, amely tartalmazza a könyvtárak kell érniük a tárhelyet.

### <a name="import-the-package"></a>A csomag importálása
A Jegyzettömbben vagy más szövegszerkesztőben, adja hozzá a következő végighaladva a **server.js** fájl tartalmát, ha szeretne használni a tárolási:

```
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Az Azure Storage-kapcsolat beállítása
Az azure-moduljának olvassák a környezeti változók AZURE\_tárolási\_FIÓKOT és az AZURE\_tárolási\_hozzáférés\_kulcs, vagy AZURE\_tárolási\_kapcsolat\_KARAKTERLÁNCOT az Azure storage-fiókhoz való kapcsolódáshoz szükséges adatokat. Ha ezek a környezeti változók nem, meg kell adnia a fiókadatokat, meghívásakor **createQueueService**.

A környezeti változók beállítása példát a [Azure Portal](https://portal.azure.com) egy Azure-webhelyre, lásd: [Node.js webalkalmazás az Azure Table szolgáltatással].

## <a name="how-to-create-a-queue"></a>Útmutató: A várólista létrehozása
Az alábbi kód létrehoz egy **QueueService** objektum, amely lehetővé teszi a várólisták használata.

```
var queueSvc = azure.createQueueService();
```

Használja a **createQueueIfNotExists** metódus, amely a megadott várólista adja vissza, ha már létezik, vagy új várólista létrehozása a megadott névvel, ha még nem létezik.

```
queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
  if(!error){
    // Queue created or exists
  }
});
```

Ha a várólista létrejön, `result.created` értéke true. Ha a várólista létezik, `result.created` értéke "false".

### <a name="filters"></a>Szűrők
Választható szűrési műveletek használatával végrehajtott műveletek alkalmazhatók **QueueService**. Műveletek szűrésének lehetnek naplózási, automatikus újrapróbálkozása, stb. A metódus aláírása megvalósító objektumok szűrők a következők:

```
function handle (requestOptions, next)
```

Ezután a előfeldolgozása kérelmet a beállítások, a metódus hívása a "Tovább" típusra a következő aláírással rendelkező visszahívás kell:

```
function (returnObject, finalCallback, next)
```

A visszahívási, és a returnObject (válasza a kérés a kiszolgáló) feldolgozása után a visszahívás kell mellett meghívni, ha más szűrők feldolgozásának folytatásához létezik, vagy egyszerűen meghívása finalCallback ellenkező esetben a szolgáltatás hívásához fog.

Két szűrőket, amelyek megvalósítják az újrapróbálkozási logika érhetők el az Azure SDK for Node.js, a **ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**. A következő létrehoz egy **QueueService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: A várólista üzenet beszúrása
Üzenet beszúrása egy üzenetsort, használja a **createMessage** módszert, hozzon létre egy új üzenetet, és vegye fel a várólistára.

```
queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: A következő üzenet megtekintése
Is bepillanthat, hogy egy sor elején található üzenetbe anélkül, hogy eltávolítaná az üzenetsorból meghívásával a **peekMessages** metódust. Alapértelmezés szerint **peekMessages** betekintés egyetlen üzenetben.

```
queueSvc.peekMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
  }
});
```

A `result` az üzenet tartalmazza.

> [!NOTE]
> Használatával **peekMessages** Ha nincsenek a várólistán lévő üzenetek nem hibát adnak vissza, azonban nem adja vissza.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet created
Egy üzenet feldolgozása egy két lépésből álló folyamat:

1. Az üzenet feldolgozásához.
2. Az üzenet törlése.

Egy üzenet feldolgozásához, használja a **getMessages**. Így az üzenetek nem látható a várólistán, így más ügyfelek nem képes feldolgozni azokat. Az alkalmazás rendelkezik egy üzenet feldolgozása után hívja **deleteMessage** törli-e az üzenetsorból. A következő példa egy üzenetet kap, majd törli őket:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
    var message = result[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> Alapértelmezés szerint az üzenet csak rejtett 30 másodpercig, amely után is látható, más ügyfelek számára. A jelenlegitől eltérő értéket is megadhat `options.visibilityTimeout` rendelkező **getMessages**.
> 
> [!NOTE]
> Használatával **getMessages** Ha nincsenek a várólistán lévő üzenetek nem hibát adnak vissza, azonban nem adja vissza.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Az aszinkron üzenet tartalmának módosítása
Módosíthatja a tartalmát egy üzenet helyben a várólista használatával **updateMessage**. Az alábbi példa frissíti a szöveges üzenet:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Got the message
    var message = result[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: További beállítások üzenetmozgatót üzenetek
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból:

* `options.numOfMessages`-Beolvasása az üzenetkötegek (legfeljebb 32.)
* `options.visibilityTimeout`– Állítsa be a hosszabb vagy rövidebb láthatatlansági időkorlátot.

Az alábbi példában a **getMessages** módszer segítségével 15 üzenetek egy hívásban. Ezután minden üzenetet használatával feldolgozza a hurok. Ez a metódus által visszaadott összes üzenet öt perc értékre állítja a láthatatlansági időkorlátot is.

```
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = result[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
A **getQueueMetadata** metaadatainak beolvasása, beleértve a várólistán üzenetek hozzávetőleges számát adja vissza.

```
queueSvc.getQueueMetadata('myqueue', function(error, result, response){
  if(!error){
    // Queue length is available in result.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Útmutató: A lista várólisták
Várólisták listájának lekéréséhez használja **listQueuesSegmented**. Egy adott előtag alapján szűrt listáját olvashatja, **listQueuesSegmentedWithPrefix**.

```
queueSvc.listQueuesSegmented(null, function(error, result, response){
  if(!error){
    // result.entries contains the list of queues
  }
});
```

Az összes várólistán nem adható vissza, ha `result.continuationToken` első paramétere használható **listQueuesSegmented** vagy a második paraméter **listQueuesSegmentedWithPrefix** további eredmények beolvasásához.

## <a name="how-to-delete-a-queue"></a>Útmutató: A várólista törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a **deleteQueue** a várólista-objektum metódust.

```
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Törlés nélkül törli az összes üzenetet az üzenetsorból, használjon **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Hogyan: közös hozzáférésű Jogosultságkód használata
Megosztott hozzáférési aláírásokkal (SAS), amelyek egy biztonságos várólisták részletes hozzáférést biztosítanak a tárfiók neve vagy a kulcsok megadása nélkül. SAS korlátozott hozzáférést biztosít a várólisták, például egy mobil alkalmazás üzeneteket küldhet gyakran használják.

A megbízható alkalmazások, például egy felhőalapú szolgáltatás létrehoz egy SAS használatával a **generateSharedAccessSignature** , a **QueueService**, és a nem megbízható vagy félig megbízható alkalmazáshoz. Ha például egy mobilalkalmazást. A biztonsági Társítások létrejön egy házirendet, amely a kezdő és záró dátumát, amely alatt a SAS érvénytelen ismerteti, valamint a hozzáférési szintet a biztonsági Társítások jogosult kap.

Az alábbi példa létrehoz egy új megosztott elérési házirendet, amely lehetővé teszi a biztonsági Társítások jogosult üzenetek hozzáadása a várólista, és 100 perc létrehozása után lejár.

```
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Vegye figyelembe, hogy az állomás információit meg kell adni is, mivel esetén megadása kötelező a biztonsági Társítások jogosult megpróbál hozzáférni a várólista.

Az ügyfélalkalmazás majd használja a biztonsági Társításait **QueueServiceWithSAS** szemben a várólista műveletek végrehajtásához. A következő példa a várólista csatlakozik, és létrehoz egy üzenetet.

```
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

A SAS létre lett hozva a hozzáférés hozzáadása, ha volt kísérlet olvasása, frissítése vagy törli az üzenetet, mert akkor kell hibát jelez.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Egy hozzáférés-vezérlési lista (ACL) segítségével állítsa be a hozzáférési házirend tartozó SAS korlátozására. Ez akkor hasznos, ha a várólista hozzáférés, de különböző hozzáférési házirendek biztosít az egyes ügyfelek több ügyféllel engedélyez.

Hozzáférés-vezérlési Listában hozzáférési házirendeket, tömbje segítségével minden házirendhez társított azonosítójú van megvalósítva. A következő példában két szabályzatokat; határoz meg. egy "felhasználó1" és "felhasználó2":

```
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Az alábbi példa lekérdezi az aktuális hozzáférés-Vezérlési **Várólista_neve**, majd hozzáadja az új házirendek **setQueueAcl**. Ez a megközelítés lehetővé teszi, hogy:

```
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Miután a hozzáférés-vezérlési lista van beállítva, majd a házirend-azonosító alapján SAS hozhatók létre. Az alábbi példa létrehoz egy új SAS-kód "felhasználó2":

```
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről.

* Látogasson el a [Azure Storage csapat blogja][Azure Storage Team Blog].
* Látogasson el a [Azure Storage szolgáltatás SDK csomópont] [ Azure Storage SDK for Node] GitHub tárházából.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Node.js-webalkalmazás létrehozása az Azure App Service-ben](../../app-service/app-service-web-get-started-nodejs.md)

[Node.js alkalmazás létrehozása és üzembe helyezése Azure Cloud Service-ben](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
