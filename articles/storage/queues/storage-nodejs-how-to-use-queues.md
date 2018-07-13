---
title: A Queue storage, a Node.js használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Queue szolgáltatás létrehozása és törlése, üzenetsorok, és helyezze, beolvasása, és törli az üzenetet. A minta Node.js-ben írt.
services: storage
documentationcenter: nodejs
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: cshoe
ms.openlocfilehash: c19ac5f45504e3c19b0b300dbc3ea157861d05ed
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681218"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a Microsoft Azure Queue szolgáltatást használó általános forgatókönyvhöz. A minták írták, a Node.js API-val. Az ismertetett forgatókönyvek között megtalálható **beszúrása**, **Bepillantás**, **első**, és **törlése** várólista-üzenetek, valamint  **létrehozása és törlése a várólisták**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js-alkalmazás létrehozása
Hozzon létre egy üres Node.js-alkalmazás. Node.js-alkalmazás létrehozása utasításokért lásd: [Node.js-webalkalmazás létrehozása az Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [létrehozása és üzembe helyezése az Azure Cloud Service egy Node.js-alkalmazás](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) vagyWindowsPowerShellhasználatával[ A Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Tárolás-alkalmazás konfigurálása
Az Azure storage használatához szükség van az Azure Storage SDK nodejs-hez, amely kommunikálni a tárolási REST szolgáltatások kényelmi kódtárak tartalmazó.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Használja a Node Package Manager (NPM) a csomag beszerzése
1. Például használja a parancssori felület **PowerShell** (Windows), **terminálon** (Mac), vagy **Bash** (Unix), lépjen abba a mappába, amelyben létrehozta a mintaalkalmazáshoz.
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

3. Manuálisan futtathatja a **ls** paranccsal ellenőrizheti, hogy egy **csomópont\_modulok** mappában jött létre. A mappában található az **azure-storage** csomag, amely a tárhely eléréséhez szükséges kódtárakat tartalmazza.

### <a name="import-the-package"></a>A csomag importálása
A Jegyzettömb vagy egy másik szövegszerkesztőt, adja hozzá a következő felső a **server.js** fájlt az alkalmazás hol szeretne használni a storage:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Az Azure Storage-kapcsolat beállítása
Az azure-modul fogja beolvasni a környezeti változókat az AZURE\_tárolási\_fiók és az AZURE\_tárolási\_hozzáférés\_kulcs, vagy az AZURE\_tárolási\_kapcsolat\_ KARAKTERLÁNC, az Azure storage-fiókjába való kapcsolódáshoz szükséges információkat. Ha nincsenek beállítva ezek a környezeti változók, meg kell adnia a fiókadatok hívásakor **createQueueService**.

## <a name="how-to-create-a-queue"></a>Útmutató: A várólista létrehozása
Az alábbi kód létrehoz egy **QueueService** objektum, amely lehetővé teszi, hogy az üzenetsorok.

```javascript
var queueSvc = azure.createQueueService();
```

Használja a **createQueueIfNotExists** metódussal, amely a megadott várólista adja vissza, ha már létezik, vagy a megadott néven létrehoz egy új üzenetsort, ha azt nem létezik.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Ha az üzenetsor létrehozása `result.created` igaz. Ha a várólista létezik, `result.created` false (hamis).

### <a name="filters"></a>Szűrők
Választható szűrési műveletek használatával végrehajtott műveletek alkalmazhatók **QueueService**. Műveletek szűrésének is tartalmazhat, naplózás, automatikus újrapróbálkozása, stb. A szűrők olyan objektumok, amelyek metódusokat implementálnak az alábbi aláírással:

```javascript
function handle (requestOptions, next)
```

Ezután az előfeldolgozási kérelem a beállítások, a metódus hívása a Next (Tovább) átadása egy visszahívást, a következő aláírással rendelkező van szüksége:

```javascript
function (returnObject, finalCallback, next)
```

A visszahívási, és a returnObject (a válasz a kérelemből a kiszolgálóhoz) feldolgozása után a visszahívás vagy a következő meghívása, ha létezik más szűrők feldolgozása a folytatáshoz, vagy egyszerűen meghívása finalCallback ellenkező esetben a szolgáltatás be kell Meghívási.

Az Azure SDK for Node.js tartalmaz két szűrőt (**ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**), amelyek újrapróbálkozási logikát implementálnak. A következő létrehoz egy **QueueService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása egy üzenetsorba
Üzenet beszúrása egy üzenetsorba, használja a **createMessage** metódust hozzon létre egy új üzenetet, és adja hozzá az üzenetsorhoz.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: A következő üzenet betekintés
Anélkül, hogy eltávolítaná az üzenetsorból meghívásával is bepillanthat egy üzenetsorban található üzenet a **peekMessages** metódust. Alapértelmezés szerint **peekMessages** betekintés egy üzenet.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

A `result` tartalmazza az üzenetet.

> [!NOTE]
> Használatával **peekMessages** Ha nincsenek üzenetek az üzenetsorban nem ad vissza hibát, azonban nincsenek üzenetek adja vissza.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet eltávolítása a sorból
Egy üzenet feldolgozása két szakaszból álló folyamat során a rendszer:

1. Az üzenet eltávolítása a sorból.
2. Az üzenet törlése.

Egy üzenet eltávolítása a sorból, használja a **getMessages**. Ez lehetővé teszi az üzenetek nem látható, az üzenetsorban, így más ügyfelek nem tud feldolgozni azokat. Miután az alkalmazás egy üzenetet dolgozott fel, hívja **deleteMessage** való törléséhez az üzenetsorból. Az alábbi példa beolvas egy üzenetet, majd törli őket:

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
> Alapértelmezés szerint az üzenet csak rejtett 30 másodpercig, amely után is látható, más ügyfelek számára. Egy másik értéket is megadhat a `options.visibilityTimeout` a **getMessages**.
> 
> [!NOTE]
> Használatával **getMessages** Ha nincsenek üzenetek az üzenetsorban nem ad vissza hibát, azonban nincsenek üzenetek adja vissza.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Az üzenetsorban található üzenet tartalmának módosítása
Módosíthatja a tartalmát egy üzenet helyben, az üzenetsor használatával **updateMessage**. Az alábbi példa frissíti az üzenet szövege:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: További beállítások üzenetmozgatót üzenetek
Testre szabhatja az üzenetek lekérését egy üzenetsorból két módja van:

* `options.numOfMessages` -Lekéréséhez üzenetkötegek (legfeljebb 32.)
* `options.visibilityTimeout` – Állítsa be a hosszabb vagy rövidebb láthatatlansági időtúllépés.

Az alábbi példában a **getMessages** metódus lekéréséhez 15 üzeneteket egyetlen hívásával. Ezután feldolgozza, hogy minden üzenet használatával egy for ciklusba. Ez a metódus által visszaadott összes üzenet öt percre az láthatatlansági időkorlátot is beállítja.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
A **getQueueMetadata** az üzenetsorban, beleértve a várakozási sorban üzenetek hozzávetőleges száma metaadatait adja vissza.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Útmutató: Lista üzenetsorok
Üzenetsorok listájának lekéréséhez használja **listQueuesSegmented**. Egy adott előtag alapján szűrt listájának lekéréséhez használja **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Valamennyi üzenetsorok nem téríthetők vissza, ha `result.continuationToken` az első paraméterként használhatók **listQueuesSegmented** vagy a második paraméter **listQueuesSegmentedWithPrefix** további eredmények lekéréséhez.

## <a name="how-to-delete-a-queue"></a>Útmutató: Üzenetsor törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja a **deleteQueue** metódust a várólista-objektum.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Törölje az összes üzenetet egy üzenetsorból annak törlése nélkül, használjon **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>How to: közös hozzáférésű Jogosultságkódok használata
A közös hozzáférésű Jogosultságkódok (SAS) egy biztonságos módon a várólisták részletes hozzáférés biztosítására a storage-fiók neve vagy a kulcsok megadása nélkül. SAS gyakran használják a várólisták, például engedélyezheti a mobilalkalmazás üzenetek elküldéséhez korlátozott hozzáférést biztosít.

A megbízható alkalmazások, például egy felhőalapú szolgáltatás létrehoz egy SAS használatával a **generateSharedAccessSignature** , a **QueueService**, és a egy nem megbízható vagy félig megbízható alkalmazás. Ha például egy mobilalkalmazást. A rendszer egy szabályzat segítségével hozza létre az SAS-eket, amely meghatározza az SAS érvényességének kezdő és befejező dátumát, valamint az SAS tulajdonosának biztosított hozzáférési szintet.

Az alábbi példa létrehoz egy új megosztott elérési házirendet, amely lehetővé teszi a üzeneteket ad hozzá a várólista SAS jogosult, és 100 perc, a létrehozás ideje után lejár.

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

Vegye figyelembe, hogy a gazdagép adatait meg kell adni is, mivel esetén a biztonsági Társítások jogosult próbál meg hozzáférni az üzenetsor.

Az ügyfélalkalmazás ezután használja a SAS with **QueueServiceWithSAS** a várólista végzett műveletek végrehajtásához. Az alábbi példa az üzenetsorhoz kapcsolódik, és üzenetet hoz létre.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

A SAS létre lett hozva a hozzáférés hozzáadása, ha történt kísérlet olvasni, frissíteni vagy törli az üzenetet, mert hiba kellene visszaadnia.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Hozzáférés-vezérlési listát (Access Control List, ACL) is használhat az SAS hozzáférési szabályzatának beállítására. Ez akkor hasznos, ha szeretné lehetővé teszi több ügyfelek számára a várólista eléréséhez, de eltérő hozzáférési szabályzatokat biztosít minden ügyfél számára.

A rendszer ACL-eket implementál egy hozzáférésiszabályzat-tömbbel, és minden szabályzathoz azonosító van társítva. Az alábbi példa meghatározza a két házirend; egy 'felhasználó1', "mind a Felhasználó2" egyet:

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

Az alábbi példa lekéri az aktuális ACL-JÉNEK **myqueue**, majd hozzáadja az új szabályzatok használatával **setQueueAcl**. Ez a megközelítés lehetővé teszi, hogy:

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

Után az ACL-t adja meg, majd létrehozhat egy SAS-szabályzat azonosítója alapján. Az alábbi példa egy új SAS-t hoz létre „user2” számára:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok.

* Látogasson el a [Azure Storage csapat blogja][Azure Storage Team Blog].
* Látogasson el a [Azure Storage SDK a Node] [ Azure Storage SDK for Node] tárházban a Githubon.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Node.js-webalkalmazás létrehozása az Azure App Service-ben](../../app-service/app-service-web-get-started-nodejs.md)

[Node.js alkalmazás létrehozása és üzembe helyezése Azure Cloud Service-ben](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
