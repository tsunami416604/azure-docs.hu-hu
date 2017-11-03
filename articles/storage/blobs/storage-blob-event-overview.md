---
title: "Azure Blob Storage-események (előzetes verzió) reagálnak |} Microsoft Docs"
description: "Esemény rács Azure Blob Storage-események használni."
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/25/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: f7a43d0a7255b326cd550fbcbb92bba93905d293
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="reacting-to-blob-storage-events-preview"></a>Reagál a Blob storage-események (előzetes verzió)

Az Azure Blob storage-események lehetővé teszik az alkalmazások létrehozását és törlését a blobok modern kiszolgáló nélküli architektúrák használatával, és nincs szükség bonyolult kód vagy drága, és nem elég hatékony lekérdezési szolgáltatások reagálni.  Ehelyett az események leküldött vannak [Azure esemény rács](https://azure.microsoft.com/services/event-grid/) többek között az előfizetőknek [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni HTTP-figyelő, és csak akkor a valóban használt funkciókért fizetési.

Blob Storage-esemény szabhatják kép- vagy videóátvitel feldolgozás, a keresések indexelése vagy minden fájl alapú munkafolyamat tartalmazza.  Aszinkron fájlfeltöltéseket egy nagyszerű beválik, ha az események.  Ha módosítások alkalomszerű, de adott esetben szükség van a közvetlen reakcióidőt, esemény-alapú architektúra különösen hatékony lehet.

Esemény rács jelenleg előzetes verzióban érhetők, és a fiókokhoz érhető el a ***nyugati középső Régiójában*** vagy ***2 USA nyugati régiója*** helyét.  Vessen egy pillantást [útvonal Blob storage-események az egyéni webkiszolgáló-végpont](storage-blob-event-quickstart.md) gyors például.

![Esemény rács modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob Storage-fiókok
A BLOB storage-események találhatók [Blob storage-fiókok](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) (és nem az általános célú tárfiókok esetében).  A Blob Storage-fiók egy speciális tárfiók a strukturálatlan adatok blobként (objektumként) való tárolására az Azure Storage-ban. BLOB storage-fiókok hasonló általános célú tárfiókok, és minden nagy tartósságot, rendelkezésre állási, méretezhetőség és teljesítmény szolgáltatás használata ma beleértve a 100 %-os API-konzisztenciát a blokkblobokhoz, és a hozzáfűző blobokhoz. A csak blokkok és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát.

## <a name="available-blob-storage-events"></a>Elérhető a Blob storage-események
Esemény rács által használt [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) esemény üzenetek előfizetőknek.  A BLOB storage esemény előfizetések tartalmazhatnak kétféle esemény:  

> |Esemény neve|Leírás|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Blob létrehozásakor vagy cserélni keresztül következik a `PutBlob`, `PutBlockList`, vagy `CopyBlob` műveletek|
> |`Microsoft.Storage.BlobDeleted`|Egy blob keresztül törlésekor következik a `DeleteBlob` művelet|

## <a name="event-schema"></a>Esemény séma
BLOB storage-események tartalmaznia kell az adatok változásait összes információt.  A Blob storage-események azonosíthatja, mert a eventType tulajdonság kezdődik "Microsoft.Storage."  
További információ a használati esemény rács esemény tulajdonságainak részletes ismertetését lásd: [esemény rács esemény séma](../../event-grid/event-schema.md).  

> |Tulajdonság|Típus|Leírás|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |A témakör|Karakterlánc|A storage-fiók, amely bocsát ki az esemény teljes Azure Resource Manager azonosítója.|
> |Tulajdonos|Karakterlánc|Az objektum, amely ugyanazt a kiterjesztett Azure Resource Manager formátumban, amelyek bemutatják a storage-fiókok, a szolgáltatások és a tárolók az Azure RBAC használjuk az esemény tárgya relatív erőforrás elérési útja.  Ez a formátum nagybetűket blob nevét tartalmazza.|
> |eventTime|Karakterlánc|Dátum és idő, hogy az eseményt a rendszer létrehozta, ISO 8601 formátumban|
> |Esemény típusa|Karakterlánc|"Microsoft.Storage.BlobCreated" vagy "Microsoft.Storage.BlobDeleted"|
> |Azonosító|Karakterlánc|Egyedi azonosító, ha ez az esemény|
> |Adatok|Objektum|A blob storage-specifikus eseményadatok gyűjteménye|
> |data.contentType|Karakterlánc|A BLOB, mint a blobból a Content-Type fejléc vissza a tartalom típusa|
> |data.contentLength|Szám|Ahogy közötti egész számot bájt, számos, a blobból az a Content-Length fejlécet vissza a blob mérete.  Küldött BlobCreated esemény, de nem BlobDeleted.|
> |Data.URL|Karakterlánc|Az objektum, amely az esemény tárgya URL-címe|
> |data.eTag|Karakterlánc|Az etag, az objektum, ha ez az esemény következik be.  Nem érhető el a BlobDeleted esemény.|
> |Data.API|Karakterlánc|Ezt az eseményt kiváltó api-művelet neve.  BlobCreated események az értéke "PutBlob", "PutBlockList" vagy "CopyBlob".  BlobDeleted események az értéke "DeleteBlob".  Ezeket az értékeket a azonos api-nevek, amelyek szerepelnek az Azure Storage diagnosztikai naplók.  Lásd: [naplózott műveletek és állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |Data.sequencer|Karakterlánc|Egy nem átlátszó karakterláncértéket, amely a logikai eseménysorozat bármely adott blob neve.  Felhasználók szabványos karakterlánc-összehasonlítási használatával megérthetik, hogy a relatív eseménysorozat két a blob neve.|
> |data.requestId|Karakterlánc|A tárolási API művelet kérelem szolgáltatás által létrehozott azonosítója.  Azure Storage diagnosztikai a "kérelem-azonosító-fejlécet" mező a naplófájlokban naplózza, és küld vissza kezdeményezése API-hívás az "x-ms-request-id" fejléc összefüggéseket használható. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|Karakterlánc|Ügyfél által megadott kérelemazonosító a tárolási API-művelet.  Az Azure Storage diagnosztikai naplókat a naplók "client-request-id" mezője összefüggéseket használható, és biztosítható, hogy az ügyfél kérelmekben a "x-ms-client-request-id" fejlécet. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|Objektum|Diagnosztikai adatok az Azure Storage szolgáltatás alkalmanként tartalmazza.  Ha létezik, figyelmen kívül hagyja eseményfelhasználók által.|

Íme egy példa egy BlobCreated esemény:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  }
}]

```

További információkért lásd: [Blob storage-események séma](../../event-grid/event-schema.md#azure-blob-storage).

## <a name="filtering-events"></a>Események szűrése
A BLOB esemény-előfizetések az esemény típusát, valamint a tároló és a létrehozott vagy törölt objektum blob neve alapján szűrhetők.  Tulajdonos szűrők alapján esemény rács munkahelyi "jellel kezdődik" és "végződik" megfelel, úgy, hogy az előfizető kézbesíti az eseményeket egy egyező témát.
A Blob storage-események tárgya formátumot használja:
```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```
A storage-fiókhoz tartozó összes esemény megfeleltetéséhez üresen a tulajdonos szűrők.

A blobok tárolókban előtag megosztása készlete létrehozott események kereséséhez használja a `subjectBeginsWith` például szűrése:
```json
/blobServices/default/containers/containerprefix
```
Az adott tárolóban létrehozott BLOB események kereséséhez használja a `subjectBeginsWith` például szűrése:
```json
/blobServices/default/containers/containername/
```
A blob nevének előtagját megosztása adott tárolóban létrehozott BLOB származó események kereséséhez használja a `subjectBeginsWith` például szűrése:
```json
/blobServices/default/containers/containername/blobs/blobprefix
```

A BLOB egy blob utótagot megosztása adott tárolóban létrehozott események kereséséhez használja a `subjectEndsWith` például ".log" vagy ".jpg" szűrő

További információkért lásd: [esemény rács fogalmak](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Események felhasználásához tartozó eljárásokat
Alkalmazások, amelyek kezelik a Blob storage-események néhány javasolt eljárást kell követnie:
> [!div class="checklist"]
> * Mivel több előfizetéssel beállítható úgy, hogy az azonos eseménykezelő útvonal események, fontos, nem egy adott forrásból származó események feltételezik, de az üzenet annak érdekében, hogy a tárfiók már vár származik a témakör kereséséhez.
> * Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy felkészültek folyamat, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
> * Üzenetek is nem megfelelő sorrendben érkeznek, és némi várakozás után, az etag mezők segítségével megértse, hogy az objektumok adatait még mindig naprakész.  Emellett a sequencer mezők segítségével azonosíthatja egy adott objektumra események sorrendjének.
> * A blobType mező használatával megérteni, hogy milyen típusú műveletek engedélyezettek a blob, és mely ügyféloldali kódtár meg kell adnia a blob eléréséhez használ.
> * Az URL-cím mezőben használja a `CloudBlockBlob` és `CloudAppendBlob` konstruktorok a blob eléréséhez.
> * Mezők nem világos, figyelmen kívül.  Ez az eljárás segítségével rugalmas nyomon lehet, hogy a jövőben hozzáadott új funkciók.


## <a name="next-steps"></a>Következő lépések

További tudnivalók az esemény rács, és adjon a Blob storage-események egy try:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [A Blob storage eseményeket továbbítani egy egyéni webkiszolgáló-végpont](storage-blob-event-quickstart.md)
