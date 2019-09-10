---
title: Az Azure üzenetsor-tároló használata a Node. js-ben – Azure Storage
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue szolgáltatás használatával, valamint hogyan szúrhat be, kérhet le és törölhet üzeneteket. Node. js-ben írt minták.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 83aecc9c05fec5869a82930f2d687ac907981c00
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858664"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket a Microsoft Azure Queue szolgáltatás használatával. A mintákat a Node. js API-val kell írni. A tárgyalt forgatókönyvek közé tartozik például a várólista-üzenetek **beszúrása**, **bepillantása**, **beolvasása**és **törlése** , valamint a **várólisták létrehozása és törlése**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node. js-alkalmazás létrehozása
Hozzon létre egy üres Node. js-alkalmazást. A Node. js-alkalmazások létrehozásával kapcsolatos utasításokért lásd: [Node. js-Webalkalmazás létrehozása Azure app Serviceban](../../app-service/app-service-web-get-started-nodejs.md), a [Node. js-alkalmazások kiépítése és üzembe helyezése egy Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) -ben a Windows PowerShell vagy a [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)használatával.

## <a name="configure-your-application-to-access-storage"></a>Az alkalmazás konfigurálása a tároló elérésére
Az Azure Storage használatához szüksége lesz a Node. js-hez készült Azure Storage SDK-ra, amely a tárolási REST-szolgáltatásokkal kommunikáló kényelmi könyvtárak készletét tartalmazza.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Node Package Managert (NPM)
1. Használjon parancssori felületet (például **PowerShell** -(Windows-) **terminál** (Mac) vagy **bash** (UNIX), Navigáljon arra a mappára, ahol létrehozta a minta alkalmazást.
2. Írja be az **npm install azure-storage** kifejezést a parancsablakba. A parancs kimenete az alábbihoz fog hasonlítani.
 
    ```bash
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

3. Az **ls** parancs manuális futtatásával ellenőrizheti, hogy létrejött-e a **csomópont\_-modulok** mappája. A mappában található az **azure-storage** csomag, amely a tárhely eléréséhez szükséges kódtárakat tartalmazza.

### <a name="import-the-package"></a>A csomag importálása
A Jegyzettömb vagy egy másik szövegszerkesztő használatával adja hozzá a következőt annak az alkalmazásnak a **Server. js** fájljához, ahol a Storage-t használni szeretné:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Azure Storage-beli kapcsolatok beállítása
Az Azure-modul beolvassa a környezeti\_változókat az Azure\_Storage\_-\_fiókkal és az Azure\_Storage\_-hozzáférési kulccsal vagy az Azure\_Storage-kapcsolattal\_ Az Azure Storage-fiókhoz való kapcsolódáshoz szükséges adatok KARAKTERLÁNCa. Ha ezek a környezeti változók nincsenek beállítva, a **createQueueService**meghívásakor meg kell adnia a fiók adatait.

## <a name="how-to-create-a-queue"></a>kézikönyv: Várólista létrehozása
A következő kód létrehoz egy **QueueService** objektumot, amely lehetővé teszi a várólistákkal való munkavégzést.

```javascript
var queueSvc = azure.createQueueService();
```

Használja a **createQueueIfNotExists** metódust, amely visszaadja a megadott várólistát, ha már létezik, vagy létrehoz egy új várólistát a megadott névvel, ha még nem létezik.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Ha a várólista létrejött, `result.created` igaz. Ha a várólista létezik, `result.created` a hamis.

### <a name="filters"></a>Szűrők
A nem kötelező szűrési műveletek alkalmazhatók a **QueueService**használatával végrehajtott műveletekre. A szűrési műveletek magukban foglalhatják a naplózást, az automatikus újrapróbálkozásokat stb. A szűrők olyan objektumok, amelyek metódusokat implementálnak az alábbi aláírással:

```javascript
function handle (requestOptions, next)
```

Miután megtette az előfeldolgozását a kérelmekre vonatkozó beállításokon, a metódusnak a "Next" függvényt kell meghívnia a következő aláírással:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban, és a returnObject feldolgozását követően (a kérésből a kiszolgálóra irányuló válasz) a visszahívás a következőt kell megnyitnia, ha a szolgáltatás továbbra is feldolgozza más szűrőket, vagy egyszerűen hívja meg a finalCallback, máskülönben a szolgáltatás befejezését. hívása.

Az Azure SDK for Node.js tartalmaz két szűrőt (**ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**), amelyek újrapróbálkozási logikát implementálnak. A következő létrehoz egy **QueueService** objektumot, amely a **ExponentialRetryPolicyFilter**használja:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>kézikönyv: Üzenet beszúrása egy várólistába
Egy üzenet üzenetsorbe való beszúrásához a **createMessage** metódus használatával hozzon létre egy új üzenetet, és vegye fel azt a várólistába.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>kézikönyv: Betekintés a következő üzenetbe
A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistából a **peekMessages** metódus meghívásával. Alapértelmezés szerint a **peekMessages** egyetlen üzenetben jelenik meg.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

A `result` tartalmazza az üzenetet.

> [!NOTE]
> A **peekMessages** használata, ha a várólista nem ad vissza hibaüzenetet, de a rendszer nem ad vissza üzeneteket.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>kézikönyv: A következő üzenet elküldése a sorból
Az üzenetek feldolgozása kétfázisú folyamat:

1. Az üzenet várólistára helyezése.
2. Törölje az üzenetet.

Egy üzenet eltávolításához használja az **getMessages**. Így az üzenetek láthatatlanná válnak a várólistában, így más ügyfelek nem tudják feldolgozni azokat. Miután az alkalmazás feldolgozta az üzenetet, a **deleteMessage** meghívásával törölje azt a várólistából. A következő példa egy üzenetet kap, majd törli:

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

> [!NOTE]
> Alapértelmezés szerint az üzenet csak 30 másodpercig van elrejtve, ami azt jelzi, hogy más ügyfelek számára látható. A with `options.visibilityTimeout` **getMessages**használatával más értéket is megadhat.
> 
> [!NOTE]
> A **getMessages** használata, ha a várólista nem ad vissza hibaüzenetet, de a rendszer nem ad vissza üzeneteket.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>kézikönyv: Várólistán lévő üzenet tartalmának módosítása
Az üzenetsor tartalmát a **updateMessage**használatával módosíthatja a várólistán. Az alábbi példa egy üzenet szövegét frissíti:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>kézikönyv: További beállítások a dequeuing üzenetek számára
Az üzenetek lekérését kétféleképpen lehet testreszabni egy várólistából:

* `options.numOfMessages`– Üzenetek kötegének beolvasása (legfeljebb 32.)
* `options.visibilityTimeout`-Egy többé vagy rövidebb láthatósági időkorlát beállítása.

A következő példa a **getMessages** metódus használatával 15 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket a for loop használatával. Emellett a metódus által visszaadott összes üzenet esetében öt percre állítja be a láthatósági időtúllépést.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
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

## <a name="how-to-get-the-queue-length"></a>kézikönyv: Várólista hosszának beolvasása
A **getQueueMetadata** a várólistához tartozó metaadatokat adja vissza, beleértve a várólistán várakozó üzenetek hozzávetőleges számát is.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>kézikönyv: Várólisták listázása
A várólisták listájának lekéréséhez használja a **listQueuesSegmented**. Egy adott előtag által szűrt lista lekéréséhez használja a **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Ha az összes várólistát nem lehet `result.continuationToken` visszaadni, a **listQueuesSegmented** első paramétereként vagy a **listQueuesSegmentedWithPrefix** második paramétereként is használható a további találatok lekéréséhez.

## <a name="how-to-delete-a-queue"></a>kézikönyv: Üzenetsor törlése
Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **deleteQueue** metódust a várólista-objektumon.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Ha törölni szeretné a várólista összes üzenetét anélkül, hogy törölné, használja a **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Útmutató: Közös hozzáférésű jogosultságkódok használata
A közös hozzáférésű aláírások (SAS) biztonságos módszert biztosítanak a várólistákhoz való részletes hozzáférés biztosításához a Storage-fiók nevének vagy kulcsainak megadása nélkül. Az SAS-t gyakran a várólisták korlátozott hozzáférésének biztosítására használják, például lehetővé teszik, hogy a Mobile apps üzeneteket küldjön.

Egy megbízható alkalmazás, például egy felhőalapú szolgáltatás létrehoz egy SAS-t a QueueService **generateSharedAccessSignature** , ésbiztosítja azt egy nem megbízható vagy félig megbízható alkalmazás számára. Például egy Mobile-alkalmazás. A rendszer egy szabályzat segítségével hozza létre az SAS-eket, amely meghatározza az SAS érvényességének kezdő és befejező dátumát, valamint az SAS tulajdonosának biztosított hozzáférési szintet.

Az alábbi példa egy új megosztott elérési házirendet hoz létre, amely lehetővé teszi, hogy az SAS birtokosa üzeneteket adjon a várólistához, és 100 perccel a létrehozásuk időpontja után lejár.

```javascript
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

Vegye figyelembe, hogy a gazdagép adatait is meg kell adni, mivel ez akkor szükséges, ha az SAS-tulajdonos megpróbál hozzáférni a várólistához.

Az ügyfélalkalmazás ezután az SAS és a **QueueServiceWithSAS** használatával hajt végre műveleteket a várólistán. A következő példa csatlakozik a várólistához, és létrehoz egy üzenetet.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Mivel a SAS a hozzáférés hozzáadása lehetőséggel lett létrehozva, ha kísérlet történt az üzenetek olvasására, frissítésére vagy törlésére, hibaüzenetet ad vissza.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Hozzáférés-vezérlési listát (Access Control List, ACL) is használhat az SAS hozzáférési szabályzatának beállítására. Ez akkor lehet hasznos, ha több ügyfél számára is engedélyezni szeretné a várólista elérését, de különböző hozzáférési házirendeket biztosít az egyes ügyfelek számára.

A rendszer ACL-eket implementál egy hozzáférésiszabályzat-tömbbel, és minden szabályzathoz azonosító van társítva. Az alábbi példa két szabályzatot határoz meg; egy a "Felhasználó1" és egy "Felhasználó2" esetében:

```javascript
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

Az alábbi példa lekéri a **myqueue**aktuális ACL-listáját, majd hozzáadja az új házirendeket a **setQueueAcl**használatával. Ez a megközelítés lehetővé teszi, hogy:

```javascript
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

Miután beállította az ACL-t, létrehozhat egy SAS-t a szabályzat azonosítója alapján. Az alábbi példa egy új SAS-t hoz létre „user2” számára:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a várólista-tárolás alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

* Látogasson el [az Azure Storage csapat blogjára][Azure Storage Team Blog].
* Látogasson el a GitHubon található Node adattárhoz készült [Azure Storage SDK][Azure Storage SDK for Node] -ba.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Node. js-alapú Webalkalmazás létrehozása Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Node.js alkalmazás létrehozása és üzembe helyezése Azure Cloud Service-ben](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
