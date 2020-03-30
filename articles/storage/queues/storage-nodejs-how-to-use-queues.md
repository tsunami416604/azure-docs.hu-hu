---
title: Azure Queue storage használata a Node.js -Ból – Azure Storage
description: Ismerje meg, hogyan használhatja az Azure Queue szolgáltatást várólisták létrehozására és törlésére, valamint üzenetek beszúrására, leésére és törlésére. A node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7abcad03678131668700f5d2c64b9c971081cb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060938"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Várólisták létrehozása és törlése a Node.js várólistákból az Azure Queue Service használatával
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthatja végre a gyakori forgatókönyveket a Microsoft Azure Queue szolgáltatás használatával. A minták a Node.js API használatával íródott. A tárgyalt forgatókönyvek közé tartozik a várólista-üzenetek **beszúrása,** **bepillantása,** **bepillantása**és **törlése,** valamint **várólisták létrehozása és törlése.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
Hozzon létre egy üres Node.js alkalmazást. A Node.js alkalmazás létrehozásáról a [Node.js webalkalmazás létrehozása az Azure App Service szolgáltatásban](../../app-service/app-service-web-get-started-nodejs.md)című témakörben [talál: Node.js alkalmazás létrehozása és üzembe helyezése egy Azure Cloud Service szolgáltatásban](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) a Windows PowerShell vagy a [Visual Studio-kód](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)használatával.

## <a name="configure-your-application-to-access-storage"></a>Az alkalmazás konfigurálása az Access Storage szolgáltatásba
Az Azure storage használatához szüksége van az Azure Storage SDK node.js, amely tartalmazza a tároló REST-szolgáltatásokkal kommunikáló kényelmi kódtárak készletét.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Csomópontcsomag-kezelőt (NPM).
1. Használjon parancssori felületet, például **A PowerShell** (Windows,) **Terminál** (Mac, ) vagy **Bash** (Unix), keresse meg a mappát, ahol létrehozta a mintaalkalmazás.
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

3. Az **ls** paranccsal manuálisan ellenőrizheti, hogy létrejött-e **\_csomópontmodul-mappa.** A mappában található az **azure-storage** csomag, amely a tárhely eléréséhez szükséges kódtárakat tartalmazza.

### <a name="import-the-package"></a>A csomag importálása
A Jegyzettömb vagy más szövegszerkesztő segítségével adja hozzá a következőket annak az alkalmazásnak a **kiszolgáló.js** fájljának tetejére, amelyen a tárhelyet használni kívánja:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Azure storage-kapcsolat beállítása
Az azure modul leolvassa az\_\_AZURE STORAGE-FIÓK\_és AZ\_\_AZURE\_\_STORAGE\_ACCESS KEY környezeti változókat, vagy az AZURE STORAGE CONNECTION STRING-et az Azure storage-fiókhoz való csatlakozáshoz szükséges információkért. Ha ezek a környezeti változók nincsenek beállítva, meg kell adnia a fiókadatokat a **createQueueService hívásakor.**

## <a name="how-to-create-a-queue"></a>Útmutató: Várólista létrehozása
A következő kód létrehoz egy **QueueService** objektumot, amely lehetővé teszi a várólisták kal való munkát.

```javascript
var queueSvc = azure.createQueueService();
```

Használja a **createQueueIfNotExists** metódust, amely a megadott várólistát adja vissza, ha már létezik, vagy új várólistát hoz létre a megadott névvel, ha még nem létezik.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Ha a várólista jön létre, `result.created` igaz. Ha a várólista létezik, `result.created` hamis.

### <a name="filters"></a>Szűrők
A választható szűrési műveletek alkalmazhatók az QueueService használatával végrehajtott **műveletekre.** A szűrési műveletek közé tartozhat a naplózás, az automatikus újrapróbálkozás stb. A szűrők olyan objektumok, amelyek egy módszert implementáljanak az aláírással:

```javascript
function handle (requestOptions, next)
```

Miután elvégezte az előfeldolgozást a kérelem beállításaiban, a metódusnak meg kell hívnia a "következő" hívást a visszahívásnak a következő aláírással:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban és a returnObject feldolgozása után (a kérés válasza a kiszolgálóra), a visszahívásnak vagy meg kell hívnia a következőt, ha létezik más szűrők feldolgozásának folytatásához, vagy egyszerűen csak meghívja a finalCallback-et, hogy a szolgáltatás végére érjen létre Könyörgése.

Az Azure SDK for Node.js tartalmaz két szűrőt (**ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**), amelyek újrapróbálkozási logikát implementálnak. Az alábbiakban létrehoz egy **QueueService** objektumot, amely az **ExponentialRetryPolicyFilter szűrőt**használja:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása várólistába
Ha üzenetet szeretne beszúrni egy várólistába, a **createMessage** metódussegítségével hozzon létre egy új üzenetet, és adja hozzá a várólistához.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Hogyan: Betekintés a következő üzenetbe
A **peekMessages** metódus meghívásával a várólista elejére való betekintés a várólistából való eltávolítása nélkül is betekinthet. Alapértelmezés szerint a **peekMessages** egyetlen üzenetbe tekint be.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

A `result` tartalmazza az üzenetet.

> [!NOTE]
> A **peekMessages használata,** ha nincsenek üzenetek a várólistában, nem ad vissza hibát, de nem ad vissza üzenetet.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet várólistájának törlése
Az üzenet feldolgozása kétlépcsős folyamat:

1. Az üzenet várólistájának törlése.
2. Törölje az üzenetet.

Üzenet várólistájának törléséhez használja a **getMessages .** Ez láthatatlanná teszi az üzeneteket a várólistában, így más ügyfelek nem tudják feldolgozni őket. Miután az alkalmazás feldolgozott egy üzenetet, hívja **meg a deleteMessage-et,** hogy törölje azt a várólistából. A következő példa üzenetet kap, majd törli azt:

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
> Alapértelmezés szerint az üzenet csak 30 másodpercig van elrejtve, majd más ügyfelek számára is látható. A `options.visibilityTimeout` **getMessages**segítségével másik értéket is megadhat.
> 
> [!NOTE]
> A **getMessages** használata, ha nincsenek üzenetek a várólistában, nem ad vissza hibát, de nem ad vissza üzenetet.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: A várólistán lévő üzenetek tartalmának módosítása
Az **updateMessage**segítségével helyben módosíthatja a várólistában lévő üzenetek tartalmát. A következő példa frissíti az üzenet szövegét:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: Az üzenetek dekonkulálásának további lehetőségei
A várólistából kétféleképpen szabhatja testre az üzenetek beolvasását:

* `options.numOfMessages`- Lekérés egy tétel üzenetek (akár 32.)
* `options.visibilityTimeout`- Állítson be hosszabb vagy rövidebb láthatatlansági időhosszabbítást.

A következő példa a **getMessages** metódust használja 15 üzenet lehívásához egy hívásban. Ezután feldolgozza az egyes üzeneteket egy for ciklus használatával. Emellett a láthatatlansági időtúltávolságot öt percre állítja be az ezzel a módszerrel visszaadott összes üzenetre.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: A várólista hosszának beszerezni
A **getQueueMetadata** metaadatokat ad vissza a várólistáról, beleértve a várólistában várakozó üzenetek hozzávetőleges számát.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Útmutató: Várólisták listázása
A várólisták listájának beolvasásához használja a **listQueuesSegmented (Várólistákszegmentált**) című listát. Egy adott előtag által szűrt lista beolvasásához használja **a listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Ha az összes várólistát `result.continuationToken` nem lehet visszaadni, a **listQueuesSegmented** első paramétereként vagy a **listQueuesSegmentedWithPrefix** második paramétereként használható további eredmények beolvasásához.

## <a name="how-to-delete-a-queue"></a>Útmutató: Várólista törlése
Egy várólista és a benne lévő összes üzenet törléséhez hívja meg a **várólista-objektum deleteQueue** metódusát.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Ha törlés nélkül szeretné törölni az összes üzenetet egy várólistából, használja a **clearMessages (ClearMessages)** segítségével.

## <a name="how-to-work-with-shared-access-signatures"></a>Útmutató: Közös hozzáférésű aláírások kal való kapcsolat
A megosztott hozzáférésű aláírások (SAS) biztonságos módja annak, hogy a tárfiók nevének vagy kulcsainak megadása nélkül biztosítsanak részletes hozzáférést a várólistákhoz. A SAS-t gyakran használják arra, hogy korlátozott hozzáférést biztosítsanak a várólistákhoz, például lehetővé teszik egy mobilalkalmazás számára az üzenetek küldését.

Egy megbízható alkalmazás, például egy felhőalapú szolgáltatás sas-t hoz létre a **QueueService** **generateSharedAccessSignature** használatával, és azt egy nem megbízható vagy félig megbízható alkalmazásnak biztosítja. Például egy mobilalkalmazás. A rendszer egy szabályzat segítségével hozza létre az SAS-eket, amely meghatározza az SAS érvényességének kezdő és befejező dátumát, valamint az SAS tulajdonosának biztosított hozzáférési szintet.

A következő példa egy új megosztott hozzáférési szabályzatot hoz létre, amely lehetővé teszi a SAS-tulajdonos számára, hogy üzeneteket adjon a várólistához, és 100 perccel a létrehozása után lejár.

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

Vegye figyelembe, hogy az állomásadatokat is meg kell adni, mivel ez szükséges, amikor a SAS-tulajdonos megpróbál hozzáférni a várólistához.

Az ügyfélalkalmazás ezután a **SAS-t a QueueServiceWithSAS-szal** használja a várólistán végzett műveletek végrehajtásához. A következő példa csatlakozik a várólistához, és létrehoz egy üzenetet.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Mivel a SAS-t add access-rel hozták létre, ha megkísérelték az üzenetek olvasását, frissítését vagy törlését, a rendszer hibát ad vissza.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Hozzáférés-vezérlési listát (Access Control List, ACL) is használhat az SAS hozzáférési szabályzatának beállítására. Ez akkor hasznos, ha több ügyfél számára szeretné engedélyezni a várólista elérését, de minden ügyfélszámára különböző hozzáférési házirendeket szeretne biztosítani.

A rendszer ACL-eket implementál egy hozzáférésiszabályzat-tömbbel, és minden szabályzathoz azonosító van társítva. A következő példa két házirendet határoz meg; egy a "user1"-hez és egy a "user2"-hez:

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

A következő példa beszerzi a **myqueue**aktuális ACL-ét, majd hozzáadja az új házirendeket a **setQueueAcl**használatával. Ez a megközelítés lehetővé teszi, hogy:

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

Az ACL beállítása után létrehozhat egy SAS-t egy házirend azonosítója alapján. Az alábbi példa egy új SAS-t hoz létre „user2” számára:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta a várólista-tárolás alapjait, kövesse ezeket a hivatkozásokat az összetettebb tárolási feladatok megismeréséhez.

* Látogasson el [az Azure Storage csapat blogjára][Azure Storage Team Blog].
* Keresse fel az [Azure Storage SDK-t a][Azure Storage SDK for Node] GitHub node tárházához.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Node.js-webalkalmazás létrehozása az Azure App Service-ben](../../app-service/app-service-web-get-started-nodejs.md)

[Node.js alkalmazás létrehozása és üzembe helyezése Azure Cloud Service-ben](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
