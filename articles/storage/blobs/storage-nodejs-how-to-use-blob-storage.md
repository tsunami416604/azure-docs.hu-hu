---
title: "Blob storage-ának Node.js használatával |} Microsoft Docs"
description: "Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: e52f38d5fb3c100e4275032f9a2a1234961c672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan hajthat végre a Blob storage használatával gyakori helyzetek. A minták kerülnek, a Node.js API-n keresztül. A tárgyalt forgatókönyvekben szerepel feltöltése, listázása, letöltése és blobok törlése.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
A Node.js-alkalmazás létrehozása, lásd: [hozzon létre egy Node.js-webalkalmazás az Azure App Service], [létrehozása és központi telepítése egy Azure Cloud Service a Node.js-alkalmazás](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) – Windows PowerShell használatával vagy [hozza létre, és a Node.js webalkalmazás telepítése az Azure-ban a Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Állítsa be az alkalmazását tároló elérésére
Az Azure storage használatához szüksége az Azure Storage szolgáltatás SDK a Node.js, amely tartalmaz egy kényelmi szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Csomópont Package Manager (NPM) használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix), a lépjen abba a mappába, amelyben létrehozta a mintaalkalmazáshoz.
2. Típus **npm telepítése azure-tároló** a parancsablakban. A parancs az alábbi példakód hasonlít.

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
3. Manuálisan futtatható a **ls** parancs futtatásával ellenőrizze, hogy egy **csomópont\_modulok** mappa hozták létre. A mappában található a **azure-tároló** csomag, amely tartalmazza a szalagtárak kell érniük a tárhelyet.

### <a name="import-the-package"></a>A csomag importálása
A Jegyzettömbben vagy más szövegszerkesztőben, adja hozzá a következő elejéhez a **server.js** fájl tartalmát, ha szeretne használni a tárolási:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Egy Azure Storage-kapcsolat beállítása
Az Azure-moduljának a környezeti változókat olvassák `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_ACCESS_KEY`, vagy `AZURE_STORAGE_CONNECTION_STRING`, az Azure storage-fiókhoz való kapcsolódáshoz szükséges információkat. Ha ezek a környezeti változók nem, meg kell adnia a fiókadatokat, meghívásakor **createBlobService**.

## <a name="create-a-container"></a>Tároló létrehozása
A **BlobService** objektum lehetővé teszi, hogy a tárolók és blobok. Az alábbi kód létrehoz egy **BlobService** objektum. Adja hozzá a következő tetejénél található **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Akkor érhető el blob névtelenül **createBlobServiceAnonymous** és a gazdagép címe. Tegyük fel például, `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Új tároló létrehozásához használja **createContainerIfNotExists**. Az alábbi példakód létrehoz egy "mycontainer" nevű új tárolót:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

A tároló újonnan jön létre, ha `result.created` értéke true. Ha a tároló már létezik, `result.created` értéke "false". `response`a művelet, beleértve a tároló ETag információkat adatait tartalmazza.

### <a name="container-security"></a>Tároló biztonsági
Alapértelmezés szerint az új tárolók személyesek, és nem érhető el névtelen hozzáféréssel. Ahhoz, hogy a tároló nyilvános, úgy, hogy hozzá tud férni névtelenül, beállíthatja a tároló hozzáférési szint beállítása azokhoz a **blob** vagy **tároló**.

* **a BLOB** -lehetővé teszi a névtelen olvasási hozzáférés blob tartalom és metaadatok található, de nem tároló metaadatait, például az összes BLOB a tárolóban lévő listázása
* **tároló** -névtelen olvasási hozzáférést biztosít a blob tartalma és a metaadatok, valamint a csomagtároló metaadatai

Az alábbi példakód mutatja be a hozzáférési szint beállítása azokhoz a beállítás **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

Azt is megteheti, módosíthatja a hozzáférési szint, a tároló használatával **setContainerAcl** a hozzáférési szint megadását. Az alábbi példakód tároló módosítja, a hozzáférési szintet:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

Az eredmény a működéséről, beleértve az aktuális információkat tartalmaz **ETag** ahhoz a tárolóhoz.

### <a name="filters"></a>Szűrők
Választható szűrési műveletek használatával végrehajtott műveleteket is alkalmazhat **BlobService**. Műveletek szűrésének lehetnek naplózási, automatikus újrapróbálkozása, stb. A metódus aláírása megvalósító objektumok szűrők a következők:

```nodejs
function handle (requestOptions, next)
```

Ezután a előfeldolgozása kérelmet a beállítások, a metódus meg kell hívni a "Tovább" gombra, átadja a következő aláírással rendelkező visszahívás:

```nodejs
function (returnObject, finalCallback, next)
```

A visszahívási, és a returnObject (válasza a kérés a kiszolgáló) feldolgozása után a visszahívás kell mellett meghívni, ha más szűrők feldolgozásának folytatásához létezik, vagy egyszerűen meghívása finalCallback a szolgáltatás hívás befejezéséhez.

Két szűrőket, amelyek megvalósítják az újrapróbálkozási logika érhetők el az Azure SDK for Node.js, a **ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**. A következő létrehoz egy **BlobService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Nincsenek a blobok három különböző típusa: blokkblobokat, lapblobokat és hozzáfűző blobokat. Blokkblobok lehetővé teszik, hogy több hatékonyan nagy adatokat feltölteni. Hozzáfűző blobok vannak optimalizálva, műveletek hozzáfűzésére. Optimalizált blobok, amelyek az olvasási/írási műveletek. További információkért lásd: [ismertetése Blokkblobokat, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Blokkblobok
A blokkblob feltölteni az adatokat, használja a következő:

* **createBlockBlobFromLocalFile** - hoz létre egy új blokkblob és feltölt egy fájlt
* **createBlockBlobFromStream** - hoz létre egy új blokkblob, és feltölti a tartalmát egy stream
* **createBlockBlobFromText** - hoz létre egy új blokkblob, és feltölti a tartalmát egy karakterlánc
* **createWriteStreamToBlockBlob** -egy elérésű blokkblob írási adatfolyamot tartalmaz

Az alábbi példakód feltölti a tartalmát a **teszt.txt** fájlt **myblob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

A `result` ezen módszerek által visszaadott ismerteti, a művelet, például a **ETag** a BLOB.

### <a name="append-blobs"></a>Hozzáfűző blobokat
Új hozzáfűző blob feltölteni az adatokat, használja a következő:

* **createAppendBlobFromLocalFile** - hoz létre egy új hozzáfűző blob és feltölt egy fájlt
* **createAppendBlobFromStream** - hoz létre egy új hozzáfűző blob, és feltölti a tartalmát egy stream
* **createAppendBlobFromText** - hoz létre egy új hozzáfűző blob, és feltölti a tartalmát egy karakterlánc
* **createWriteStreamToNewAppendBlob** - hoz létre egy új hozzáfűző blob, majd azt írni adatfolyam

Az alábbi példakód feltölti a tartalmát a **teszt.txt** fájlt **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

A blokk hozzáfűzése a meglévő hozzáfűző blob, az alábbi parancsokat használja:

* **appendFromLocalFile** -fájl tartalmának hozzáfűzése a meglévő hozzáfűző blob
* **appendFromStream** -adatfolyam tartalmát hozzáfűzése a meglévő hozzáfűző blob
* **appendFromText** -karakterlánc tartalmát hozzáfűzése a meglévő hozzáfűző blob
* **appendBlockFromStream** -adatfolyam tartalmát hozzáfűzése a meglévő hozzáfűző blob
* **appendBlockFromText** -karakterlánc tartalmát hozzáfűzése a meglévő hozzáfűző blob

> [!NOTE]
> API-k appendFromXXX fog tenni bizonyos ügyféloldali érvényesítése sikertelen gyors szükségtelen server hívások elkerülése érdekében. appendBlockFromXXX nem.
>
>

Az alábbi példakód feltölti a tartalmát a **teszt.txt** fájlt **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Lapblobok
Az oldalakra vonatkozó blob feltölteni az adatokat, az alábbi parancsokat használja:

* **createPageBlob** -hoz létre egy új oldalakra vonatkozó blob egy meghatározott hosszúságú
* **createPageBlobFromLocalFile** - hoz létre egy új oldalakra vonatkozó blob és feltölt egy fájlt
* **createPageBlobFromStream** - hoz létre egy új oldalakra vonatkozó blob, és feltölti a tartalmát egy stream
* **createWriteStreamToExistingPageBlob** -egy meglévő oldalakra vonatkozó blob írható adatfolyamot tartalmaz
* **createWriteStreamToNewPageBlob** - hoz létre egy új oldalakra vonatkozó blob, majd azt írni adatfolyam

Az alábbi példakód feltölti a tartalmát a **teszt.txt** fájlt **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Lapblobokat 512 bájtos "lap" alkotják. Egy hibaüzenetet fog kapni, amely nincs 512 többszöröse méretű adatok feltöltésekor.
>
>

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához, használja a **listBlobsSegmented** metódust. Ha szeretné térjen vissza a blobok adott előtaggal kezdődik, **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

A `result` tartalmaz egy `entries` gyűjteményt, amely minden egyes blob leíró objektumok tömbje. Összes BLOB nem adható vissza, ha a `result` is biztosít a `continuationToken`, amely előfordulhat, hogy a második paraméterként további bejegyzéseinek beolvasása.

## <a name="download-blobs"></a>Blobok letöltése
Adatok letöltése a blob, az alábbi parancsokat használja:

* **getBlobToLocalFile** -ír a blob tartalmát a következő fájlba:
* **getBlobToStream** -ír a blob tartalmát egy stream
* **getBlobToText** -ír a blob tartalmát egy karakterlánc
* **createReadStream** -biztosít a blob olvasható adatfolyam

Az alábbi példakód mutatja be, használatával **getBlobToStream** tartalmának letöltése a **myblob** blob-, és tárolja a a **kimenet.txt** fájl adatfolyam használatával:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

A `result` a blob adatait tartalmazza többek között **ETag** információkat.

## <a name="delete-a-blob"></a>Blob törlése
Végezetül blob törléséhez hívja meg a **deleteBlob**. Az alábbi példakóddal törölhető nevű blob **myblob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Egyidejű hozzáférés
Támogatja a egyidejű hozzáférés blob több ügyfél vagy a folyamat több példányt, segítségével **ETag-EK** vagy **bérleteket**.

* **ETag** -észleli, hogy a blob vagy a tároló egy másik folyamat módosult módszert kínál a
* **Címbérlet** -biztosítja az beszerzése kizárólagos, megújítható, írási vagy törlése blob a hozzáférést egy adott időn belül

### <a name="etag"></a>ETag
Ha több ügyfelek vagy példányok írni a blokk Blob vagy a lap engedélyeznie kell a használat ETag-EK egyidejűleg Blob. Az ETag lehetővé teszi annak meghatározását, ha a tárolót vagy blobot óta módosították kezdetben olvasására vagy létrehozta, amely lehetővé teszi egy másik ügyfél vagy a folyamat által előjegyzett módosítások felülírni.

Nem kötelező ETag feltételeket is megadhat `options.accessConditions` paraméter. A következő példa csak feltöltések kódot a **teszt.txt** fájlt, ha a blob már létezik, és az ETag érték által tárolt `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

Amikor ETag-EK használja, az általános minta a következő:

1. Az ETag beszerzése a create, a lista és a get művelet eredményeként.
2. Egy műveletet, ellenőrzése, hogy az ETag érték nem lett módosítva.

Ha az érték módosítva lett, ez azt jelzi, hogy egy másik ügyfél vagy a példányt a blobot, vagy a tároló óta módosított ETag érték kapott.

### <a name="lease"></a>Címbérlet
Új bérletet segítségével lekérheti a **acquireLease** módszer, adja meg a címbérletet kívánt blob vagy tároló. Például az alábbi kód címbérletet a **myblob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

A későbbi műveletek **myblob** kell megadnia a `options.leaseId` paraméter. A bérlet Azonosítóját adja vissza a rendszer `result.id` a **acquireLease**.

> [!NOTE]
> Alapértelmezés szerint a címbérlet érték a végtelen. Megadhat egy nem végtelen időtartama (között 15 és 60 másodperc) biztosításával a `options.leaseDuration` paraméter.
>
>

A címbérlet eltávolításához használja **releaseLease**. Törés a címbérlet, de akadályozni, hogy mások számára egy új bérleti mindaddig, amíg az eredeti időtartam lejárt, használja a **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Megosztott hozzáférési aláírásokkal működik
Közös hozzáférésű jogosultságkód (SAS) tárolók és blobok részletes hozzáférést biztosítson anélkül, hogy a tárfiók neve vagy a kulcsokat biztonságosan. Közös hozzáférésű jogosultságkód gyakran használják korlátozott hozzáférést biztosít az adatok, például, amely lehetővé teszi a mobilalkalmazások blobokhoz való hozzáférést.

> [!NOTE]
> Is engedélyezheti a blobok névtelen hozzáférés, miközben a közös hozzáférésű jogosultságkód lehetővé teszik több ellenőrzött hozzáférést nyújtani, mint a SAS létre kell hoznia.
>
>

A megbízható alkalmazások, például egy felhőalapú szolgáltatás közös hozzáférésű jogosultságkód használatával hoz létre a **generateSharedAccessSignature** , a **BlobService**, és átadja egy nem megbízható vagy félig megbízható alkalmazás, például egy mobilalkalmazást. Megosztott hozzáférés aláírások akkor jönnek létre, használja a szabályzatot, amely ismerteti a kezdő és befejezési dátuma, amely alatt a közös hozzáférésű jogosultságkód érvényesek, valamint a hozzáférési szintet nyújtani, a megosztott hozzáférési aláírásokkal jogosult.

Az alábbi példakód létrehoz egy új megosztott elérési házirendet, amely lehetővé teszi a megosztott hozzáférési aláírásokkal tulajdonos olvasási műveletek elvégzésére a **myblob** blob-, és 100 perc létrehozása után lejár.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

Vegye figyelembe, hogy az állomás információit meg kell adni is, akkor szükség, ha a megosztott hozzáférési aláírásokkal jogosult megpróbál hozzáférni a tárolóhoz.

Az ügyfélalkalmazás majd használja a közös hozzáférésű jogosultságkód **BlobServiceWithSAS** szemben a blob műveletek végrehajtásához. A következő információ lekérése **myblob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

A közös hozzáférésű jogosultságkód csak olvasási hozzáféréssel rendelkező jött létre, ha a blob módosítására tett kísérlet, mert egy hibaüzenetet küld.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Hozzáférés-vezérlési listaként (ACL) segítségével állítsa be a hozzáférési házirendben a SAS. Ez akkor hasznos, ha több ügyfelek érhető el a tároló, de különböző hozzáférési házirendek biztosít az egyes ügyfelek számára engedélyezni szeretné.

Hozzáférés-vezérlési Listában hozzáférési házirendeket, tömbje segítségével minden házirendhez társított azonosítójú van megvalósítva. Az alábbi példakód két házirend, egy "felhasználó1", és egy "felhasználó2" határozza meg:

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Az alábbi példakód lekérdezi az aktuális hozzáférés-Vezérlési **mycontainer**, és hozzáadja az új házirendek **setBlobAcl**. Ez a megközelítés lehetővé teszi, hogy:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Miután a hozzáférés-vezérlési lista van beállítva, majd a házirend-azonosító alapján közös hozzáférésű jogosultságkód hozhatók létre. Az alábbi példakód létrehozza a "felhasználó2" az új közös hozzáférésű jogosultságkód:

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket.

* [Az azure Storage SDK API-referencia csomópont] [Az azure Storage SDK API-referencia csomópont]  
* [Az azure Storage csapat blogja] [Az azure Storage csapat blogja]  
* [Az Azure Storage szolgáltatás SDK csomópont] [ Azure Storage SDK for Node] GitHub tárházából  
* [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/)  
* [Adatátvitel az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Hozza létre és Azure Cloud Service a Node.js-alkalmazás központi telepítése](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Az azure Storage csapat blogja]: http://blogs.msdn.com/b/windowsazurestorage/  
[Az azure Storage SDK API-referencia csomópont]: http://dl.windowsazure.com/nodestoragedocs/index.html  
