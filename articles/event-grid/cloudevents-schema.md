---
title: Azure Event rács használata CloudEvents séma események
description: Ismerteti, hogyan lehet beállítani a CloudEvents séma események Azure esemény rácsban.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: babanisa
ms.openlocfilehash: 23187fbc230e384984085d330bfbfbc90cc9f945
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Esemény rács CloudEvents séma használata

Kívül a [alapértelmezett esemény séma](event-schema.md), Azure esemény rács natív módon támogatja az események a [CloudEvents JSON-séma](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) van egy [nyissa meg a standard specification](https://github.com/cloudevents/spec/blob/master/spec.md) eseményadatok leíró közös módon.

CloudEvents együttműködési leegyszerűsíti az esemény közös séma biztosít a közzététel, és fel a felhő alapú események. Ebben a sémában egységes tooling, normál módon az Útválasztás és események kezelésére és a külső esemény séma deszerializálása univerzális módokat teszi lehetővé. A közös séma könnyebben integrálhatja a munkahelyi különböző platformokon.

CloudEvents folyamatban van a blokkolt build [közreműködők](https://github.com/cloudevents/spec/blob/master/community/contributors.md), beleértve a Microsoft, a [felhő natív számítási Foundation](https://www.cncf.io/). Érhető el jelenleg 0,1 verzióként.

A cikkből megtudhatja, hogyan használható a CloudEvents séma esemény rács.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent séma

Íme egy példa egy Azure Blob Storage esemény CloudEvents formátumban:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 tulajdonságai a következők érhető el:

| CloudEvents        | Típus     | Példa JSON-érték             | Leírás                                                        | Esemény rács leképezése
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | Karakterlánc   | "com.example.someevent"          | Típusa történt előfordulás                                   | eventType
| eventTypeVersion   | Karakterlánc   | "1.0"                            | Az esemény típusa (nem kötelező) verziója                            | dataVersion
| cloudEventsVersion | Karakterlánc   | "0,1"                            | A CloudEvents megadását az esemény által használt verziója        | *továbbítva*
| source             | URI      | "/ mycontext"                     | Az esemény gyártó ismerteti                                       | a témakör #subject
| Eseményazonosító            | Karakterlánc   | "1234-1234-1234"                 | Az esemény azonosítója                                                    | id
| eventTime          | Időbélyeg| "2018-04-05T17:31:00Z"           | Ha az esemény történt (nem kötelező) időbélyegzője                    | eventTime
| schemaURL          | URI      | „https://myschema.com”           | A séma, az adatok attribútum megfelelő (nem kötelező) mutató hivatkozás | *nem használható.*
| contentType        | Karakterlánc   | az "application/json"               | Ismerteti az adatok kódolási formátum (nem kötelező)                       | *nem használható.*
| Bővítmények         | Térkép      | {"extA": "vA", "extB", "vB"}  | Minden további metaadatot (nem kötelező)                                 | *nem használható.*
| adat               | Objektum   | {"objA": "vA", "objB", "vB"}  | Az eseménytartalom (nem kötelező)                                       | adat

További információkért lásd: a [CloudEvents spec](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

## <a name="configure-event-grid-for-cloudevents"></a>Esemény rács CloudEvents konfigurálása

Jelenleg Azure esemény rács rendelkezik preview CloudEvents JSON formátumú bemeneti támogatása, és a kimenetet **nyugati középső Régiójában**, **USA középső RÉGIÓJA**, és **Észak-Európa**.

A bemeneti és kimeneti CloudEvents séma események esemény rács is használhatja. A rendszer események, például a Blob Storage-események és az IoT-központ eseményeket, és az egyéni események CloudEvents is használhatja. Azt is alakíthatja ezeket az eseményeket a keresztülhaladnak a hálózaton oda-vissza.


| A bemeneti séma       | Kimeneti séma
|--------------------|---------------------
| CloudEvents formátum | CloudEvents formátum
| Esemény rács formátumban  | CloudEvents formátum
| CloudEvents formátum | Esemény rács formátumban
| Esemény rács formátumban  | Esemény rács formátumban

A minden esemény lehetséges esemény rács érvényesítést kér, az esemény a rács témakörhöz közzétételekor, és az esemény-előfizetés létrehozása. További információkért lásd: [esemény rács biztonsági és hitelesítési](security-authentication.md).

### <a name="input-schema"></a>A bemeneti séma

A bemeneti sémát beállítása egy egyéni témakörre CloudEvents, használja a következő paramétert az Azure parancssori felület a téma létrehozásakor `--input-schema cloudeventv01schema`. Az egyéni témakör bejövő események most vár CloudEvents v0.1 formátumban.

Egy esemény rács témakör létrehozásához használja:

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

CloudEvents jelenlegi verziója nem támogatja a kötegelés események. CloudEvent sémával rendelkező események közzétételét egy témakörbe, egyenként tegye közzé minden esemény.

### <a name="output-schema"></a>Kimeneti séma

Egy esemény előfizetés CloudEvents a kimeneti sémával beállításához használja a következő paramétert az Azure CLI-t az esemény előfizetés létrehozásakor `--event-delivery-schema cloudeventv01schema`. Az esemény előfizetés események most érkeznek CloudEvents v0.1 formátumban.

Egy esemény-előfizetés létrehozása, használata:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

A CloudEvents jelenlegi verziója nem támogatja a kötegelés események. Egy esemény-előfizetést, amelyet a CloudEvent séma beállított külön-külön kap minden esemény.

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések figyelésével kapcsolatos további információkért lásd: [figyelő esemény rács üzenetkézbesítést](monitor-event-delivery.md).
* Javasoljuk, hogy teszteléséhez megjegyzést, és [közre](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) CloudEvents számára.
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
