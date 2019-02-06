---
title: Reagálás az Azure Blob storage-események |} A Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 4bc683908646a5c05fee14f721e2c26482518947
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751395"
---
# <a name="reacting-to-blob-storage-events"></a>Reagálás Blob storage-események

Az Azure Storage-események lehetővé teszik az alkalmazások létrehozását és törlését, a korszerű, kiszolgáló nélküli architektúra használatával blobok reagálni. Így összetettebb kódja vagy költséges és hatékony lekérdezési szolgáltatások nélkül hajtja végre.  Ehelyett eseményt leküld [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) például előfizetők [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni http-figyelőt, és csak akkor Mit kell fizetni.

BLOB storage-események megbízhatóan érkeznek a Event grid szolgáltatás, amely gazdag újrapróbálkozási szabályzatok és a kézbesíthetetlen levelek kézbesítési révén az alkalmazások megbízható kézbesítést szolgáltatásokat biztosít.

Blob storage esemény gyakori forgatókönyvek kép vagy videó feldolgozása, keresési indexelő vagy minden fájl alapú munkafolyamat tartalmazza.  Aszinkron fájlfeltöltéseket egy kiválóan alkalmas eseményeket a rendszer.  Módosítások ritkák, de a forgatókönyvhöz szükséges azonnali válaszképességét, eseményalapú architektúrát különösen hatékony is lehet.

Vessen egy pillantást [útvonal Blob storage-események az egyéni webes végpont - CLI](storage-blob-event-quickstart.md) vagy [útvonal Blob storage-események az egyéni webes végpont - PowerShell](storage-blob-event-quickstart-powershell.md) rövid példában. 

![Event Grid-modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob Storage-fiókok
Blobtároló események általános célú v2 tárfiókokban vagy Blob tárfiókokban érhetők el. **Általános célú v2** tárfiókok összes tárolási szolgáltatás, így a Blobok, fájlok, üzenetsorok és táblák összes funkcióját támogatják. A **Blob Storage-fiók** egy speciális tárfiók a strukturálatlan adatok blobként (objektumként) való tárolására az Azure Storage-ban. A Blob Storage-fiókok olyanok, mint a meglévő általános célú tárfiókjai, és a jelenlegi rendszereivel megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtanak, beleértve a 100%-os API-konzisztenciát a blokkblobokhoz és a hozzáfűző blobokhoz. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

## <a name="available-blob-storage-events"></a>Elérhető a Blob storage-események
Event grid használ [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára.  A BLOB storage esemény-előfizetések is tartalmazzák kétféle esemény:  

> |Esemény neve|Leírás|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Akkor, ha egy blob jön létre vagy keresztül lecseréli a `PutBlob`, `PutBlockList`, vagy `CopyBlob` műveletek|
> |`Microsoft.Storage.BlobDeleted`|Blob törlése keresztül aktivált egy `DeleteBlob` művelet|

## <a name="event-schema"></a>Eseményséma
BLOB storage-események összes kell reagálni az igények változásaira az adatokban adatokat tartalmazzák.  A Blob storage esemény is azonosítani, mert az esemény típusa tulajdonság "Microsoft.Storage" karakterlánccal kezdődik. További információ a használati Event Grid-esemény tulajdonságainak leírása itt található [Event Grid-esemény séma](../../event-grid/event-schema.md).  

> |Tulajdonság|Típus|Leírás|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |témakör|sztring|Teljes Azure Resource Manager azonosítója, amely az eseményt bocsát ki a tárfiókot.|
> |tárgy|sztring|Az objektum azonos kiterjesztett Azure Resource Manager formátumban, amelyek bemutatják a storage-fiókok, szolgáltatások és tárolók az Azure RBAC használatával az esemény tárgyát képező relatív erőforrás elérési útja.  Ez a formátum tartalmazza a blob nevét megőrzi.|
> |eventTime|sztring|Az esemény létrejött, az ISO 8601 formátumú dátum/idő|
> |eventType|sztring|"Microsoft.Storage.BlobCreated" or "Microsoft.Storage.BlobDeleted"|
> |Azonosító|sztring|Egyedi azonosító, ha az esemény|
> |dataVersion|sztring|Az adatobjektum sémaverziója.|
> |metadataVersion|sztring|A legfelső szintű tulajdonságok sémaverziója.|
> |adat|objektum|A blob storage-specifikus eseményadatok gyűjtése|
> |data.contentType|sztring|A BLOB, a Content-Type fejlécében a blobból kellene visszaadnia a tartalom típusa|
> |data.contentLength|szám|Egész számot jelölő bájt, számos, a Content-Length fejlécet a blobból kellene visszaadnia hasonlóan a blob mérete.  BlobCreated esemény, de nem BlobDeleted küldi.|
> |Data.URL|sztring|Az URL-cím az objektum, amely az esemény tárgya|
> |data.eTag|sztring|Az etag címkéje az objektumot, ha ez az esemény történt.  Nem érhető el a BlobDeleted eseményhez.|
> |Data.API|sztring|Az api-művelet ezt az eseményt kiváltó neve. BlobCreated események az értéke "PutBlob", "PutBlockList" vagy "CopyBlob". BlobDeleted események az értéke "DeleteBlob". Ezeket az értékeket az azonos api nevei, amelyek szerepelnek az Azure Storage-diagnosztikai naplók. Lásd: [naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|sztring|Egy nem átlátszó karakterláncértéket, amely a logikai eseménysorozatát bármely adott blob neve.  Felhasználók szabványos karakterláncok összehasonlítása segítségével megismerheti a relatív eseménysorozat két azonos blob neve.|
> |data.requestId|sztring|Szolgáltatás által létrehozott kérelemazonosító a storage API-művelet. Az Azure Storage-diagnosztikai naplók "fejléc-kérelem-azonosító" mező a naplók használatával, és adja vissza a megoldásig API-hívás az "x-ms-request-id" fejléc korrelációját használható. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|sztring|A storage API-művelet ügyfél által biztosított kérés azonosítója. Használható az Azure Storage diagnosztikai naplókat a naplók "client-request-id" mezője összekapcsolását, és az ügyfélkérések használja az "x-ms-client-request-id" fejléc adható meg. Lásd: [naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
> |data.storageDiagnostics|objektum|Diagnosztikai adatok az Azure Storage szolgáltatás időnként tartalmazza. Ha igen, figyelmen kívül hagyja az eseményfelhasználók által.|
|data.blobType|sztring|A blob típusa. Érvényes értékek: "BlockBlob" vagy "PageBlob".| 

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

További információkért lásd: [Blob storage-események sémája](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Események szűrése
Esemény-előfizetések a BLOB az esemény típusa, valamint a tároló neve és a létrehozott vagy törölt objektum blob neve alapján szűrhetők.  Szűrők alkalmazható esemény-előfizetések vagy során a [létrehozási](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) az esemény-előfizetés vagy [egy későbbi időpontban](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Az Event Grid során alapján tárgy szűrőket "kezdődik" és "vége" egyezést, így az előfizető kézbesíti az eseményeket egy megfelelő témát. 

A Blob storage-események tárgya a formátumot használja:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

A storage-fiókhoz tartozó összes esemény megfelel, a tárgy szűrőket üresen hagyhatja.

Események megosztása előtag tárolók készletét létrehozott blobok megfelelően, használja a `subjectBeginsWith` szűrheti, például:

```
/blobServices/default/containers/containerprefix
```

Egyezik meg a létrehozott adott tárolóhoz blobot eseményeit, használja a `subjectBeginsWith` szűrheti, például:

```
/blobServices/default/containers/containername/
```

Létrehozott egy blobnév előtagja megosztása adott tárolóhoz blobot események megfeleltetéséhez használja egy `subjectBeginsWith` szűrheti, például:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Egyezik meg a létrehozott blob utótag megosztása adott tárolóhoz blobot eseményeit, használja a `subjectEndsWith` például ".log" vagy "jpg" szűrőt. További információkért lásd: [Event Griddel kapcsolatos fogalmak](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Eljárások az események felhasználásához
Az alkalmazásokat, amelyek a Blob storage-események kezeléséhez kövesse kell néhány ajánlott eljárást:
> [!div class="checklist"]
> * Több előfizetés is beállíthatók úgy, hogy az azonos eseménykezelő események átirányítása, fontos, nem egy adott forrásból származó események feltételezik, de ellenőrzéséhez győződjön meg arról, hogy a tárfiók várt származnak üzenet a témakörben.
> * Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy folyamat kész, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
> * Üzenetek érkezésekor is sorrendben, és némi várakozás után, ha még mindig naprakész állapotban-e az adatok és objektumok megértése az etag-mezők használatával.  A sequencer mezőket is, használja a tudni, hogy egy adott objektumra események sorrendje.
> * A blobType mező segítségével megismerheti, hogy milyen típusú műveletek engedélyezettek a blob, és melyik ügyféloldali kódtár típusait, a blob eléréséhez használ. Érvényes értékek a következők egyikét `BlockBlob` vagy `PageBlob`. 
> * Az URL-cím mezőt használja a `CloudBlockBlob` és `CloudAppendBlob` konstruktorok hozzáférjen a blobhoz.
> * Hagyja figyelmen kívül nem ismeri a mezőket. Ez az eljárás fog megakadályozhatja, hogy rugalmas, előfordulhat, hogy a jövőben hozzáadott új funkciókhoz.


## <a name="next-steps"></a>További lépések

További információ az Event Grid és a Blob storage-események próbálja:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [A Blob storage-események átirányítása egyéni webes végpontra](storage-blob-event-quickstart.md)
