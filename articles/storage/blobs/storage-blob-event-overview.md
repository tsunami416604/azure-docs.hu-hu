---
title: "Azure Blob Storage-események reagálnak |} Microsoft Docs"
description: "Esemény rács Azure Blob Storage-események használni."
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: ea2ec712c8d8b5f85f020535ab0544986f0da53a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="reacting-to-blob-storage-events"></a>Reagál a Blob Storage-események

Az Azure Storage-események lehetővé teszik az alkalmazások létrehozását és törlését a blobok használatával a modern kiszolgáló nélküli architektúrák reagálni. Igen, nincs szükség bonyolult kód vagy drága, és nem elég hatékony lekérdezési szolgáltatások.  Ehelyett az események leküldött vannak [Azure esemény rács](https://azure.microsoft.com/services/event-grid/) többek között az előfizetőknek [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni HTTP-figyelő, és csak akkor a valóban használt funkciókért fizetési. 

Blob Storage-esemény szabhatják kép- vagy videóátvitel feldolgozás, a keresések indexelése vagy minden fájl alapú munkafolyamat tartalmazza.  Aszinkron fájlfeltöltéseket egy nagyszerű beválik, ha az események.  Ha módosítások alkalomszerű, de adott esetben szükség van a közvetlen reakcióidőt, esemény-alapú architektúra különösen hatékony lehet.

Rendelkezésre állás a Storage-események kötődik esemény rács [rendelkezésre állási](../../event-grid/overview.md) és más régiókban is elérhető lesz a esemény rács hasonlóan. Vessen egy pillantást [útvonal Blob storage-események az egy egyéni webalkalmazás-végpont - CLI](storage-blob-event-quickstart.md) vagy [útvonal Blob storage-események az egy egyéni webalkalmazás-végpont - PowerShell](storage-blob-event-quickstart-powershell.md) gyors például. 

![Esemény rács modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob Storage-fiókok
A BLOB storage-események találhatók [Blob storage-fiókok](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) és a [általános célú v2 tárfiókok](../common/storage-account-options.md#general-purpose-v2). **Általános célú v2 (GPv2)** minden funkció támogatása az összes tárolószolgáltatásokra, köztük a blobokat, fájlok, üzenetsorok és táblák storage-fiókok vannak. A **Blob storage-fiók** egy speciális tárfiók a strukturálatlan adatok blobként (objektumokként) az Azure Storage való tárolására. BLOB storage-fiókok hasonló általános célú tárfiókok, és minden nagy tartósságot, rendelkezésre állási, méretezhetőség és teljesítmény szolgáltatás használata ma beleértve a 100 %-os API-konzisztenciát a blokkblobokhoz, és a hozzáfűző blobokhoz. A csak blokkok és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát. 

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
> |A témakör|karakterlánc|A storage-fiók, amely bocsát ki az esemény teljes Azure Resource Manager azonosítója.|
> |Tulajdonos|karakterlánc|Az objektum, amely ugyanazt a kiterjesztett Azure Resource Manager formátumban, amelyek bemutatják a storage-fiókok, a szolgáltatások és a tárolók az Azure RBAC használjuk az esemény tárgya relatív erőforrás elérési útja.  Ez a formátum nagybetűket blob nevét tartalmazza.|
> |eventTime|karakterlánc|Dátum és idő, hogy az eseményt a rendszer létrehozta, ISO 8601 formátumban|
> |eventType|karakterlánc|“Microsoft.Storage.BlobCreated” or “Microsoft.Storage.BlobDeleted”|
> |Azonosító|karakterlánc|Egyedi azonosító, ha ez az esemény|
> |dataVersion|karakterlánc|Az objektum séma verziója.|
> |metadataVersion|karakterlánc|A legfelső szintű tulajdonságok séma verziója.|
> |adat|objektum|A blob storage-specifikus eseményadatok gyűjteménye|
> |data.contentType|karakterlánc|A BLOB, mint a blobból a Content-Type fejléc vissza a tartalom típusa|
> |data.contentLength|szám|Ahogy közötti egész számot bájt, számos, a blobból az a Content-Length fejlécet vissza a blob mérete.  Küldött BlobCreated esemény, de nem BlobDeleted.|
> |Data.URL|karakterlánc|Az objektum, amely az esemény tárgya URL-címe|
> |data.eTag|karakterlánc|Az etag, az objektum, ha ez az esemény következik be.  Nem érhető el a BlobDeleted esemény.|
> |data.api|karakterlánc|Ezt az eseményt kiváltó api-művelet neve.  BlobCreated események az értéke "PutBlob", "PutBlockList" vagy "CopyBlob".  BlobDeleted események az értéke "DeleteBlob".  Ezeket az értékeket a azonos api-nevek, amelyek szerepelnek az Azure Storage diagnosztikai naplók.  Lásd: [naplózott műveletek és állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|karakterlánc|Egy nem átlátszó karakterláncértéket, amely a logikai eseménysorozat bármely adott blob neve.  Felhasználók szabványos karakterlánc-összehasonlítási használatával megérthetik, hogy a relatív eseménysorozat két a blob neve.|
> |data.requestId|karakterlánc|A tárolási API művelet kérelem szolgáltatás által létrehozott azonosítója.  Azure Storage diagnosztikai a "kérelem-azonosító-fejlécet" mező a naplófájlokban naplózza, és küld vissza kezdeményezése API-hívás az "x-ms-request-id" fejléc összefüggéseket használható. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|karakterlánc|Ügyfél által megadott kérelemazonosító a tárolási API-művelet.  Az Azure Storage diagnosztikai naplókat a naplók "client-request-id" mezője összefüggéseket használható, és biztosítható, hogy az ügyfél kérelmekben a "x-ms-client-request-id" fejlécet. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|objektum|Diagnosztikai adatok az Azure Storage szolgáltatás alkalmanként tartalmazza.  Ha létezik, figyelmen kívül hagyja eseményfelhasználók által.|
|data.blobType|karakterlánc|A blob típusú. Érvényes értékek: "BlockBlob" vagy "PageBlob".| 

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
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

További információkért lásd: [Blob storage-események séma](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Események szűrése
A BLOB esemény-előfizetések az esemény típusát, valamint a tároló és a létrehozott vagy törölt objektum blob neve alapján szűrhetők.  Szűrők alkalmazható esemény előfizetésekhez vagy során a [létrehozása](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) az esemény-előfizetés vagy [későbbi](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Tulajdonos szűrők alapján esemény rács munkahelyi "jellel kezdődik" és "végződik" megfelel, úgy, hogy az előfizető kézbesíti az eseményeket egy egyező témát. 

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
> * A blobType mező használatával megérteni, hogy milyen típusú műveletek engedélyezettek a blob, és mely ügyféloldali kódtár meg kell adnia a blob eléréséhez használ. Érvényes értékek a következők vagy `BlockBlob` vagy `PageBlob`. 
> * Az URL-cím mezőben használja a `CloudBlockBlob` és `CloudAppendBlob` konstruktorok a blob eléréséhez.
> * Mezők nem világos, figyelmen kívül.  Ez az eljárás segítségével rugalmas nyomon lehet, hogy a jövőben hozzáadott új funkciók.


## <a name="next-steps"></a>További lépések

További tudnivalók az esemény rács, és adjon a Blob storage-események egy try:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [A Blob storage eseményeket továbbítani egy egyéni webkiszolgáló-végpont](storage-blob-event-quickstart.md)
