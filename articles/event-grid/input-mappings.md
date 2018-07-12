---
title: Egyéni mező leképezése az Azure Event Grid-séma
description: Az egyéni séma átalakítása az Azure Event Grid-séma ismerteti.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578917"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Egyéni mezők leképezése sémára Event Grid

Ha az eseményadatok nem egyezik a várt [Event Grid séma](event-schema.md), Event Grid útvonal esemény-előfizetők továbbra is használhatja. Ez a cikk ismerteti, hogyan képezhet le a séma az Event Grid-sémát.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Eredeti eseménysémája

Tegyük fel, hogy egy alkalmazást, amely elküldi az eseményeket a következő formátumban:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bár a formátuma nem felel meg a szükséges séma, Event Grid lehetővé teszi, hogy a mezők leképezése a sémát. Vagy az eredeti sémában is kap, az értékeket.

## <a name="create-custom-topic-with-mapped-fields"></a>Leképezett mezők az egyéni témakör létrehozása

Egy egyéni témakör létrehozásakor adja meg, hogyan képezhet le a mezők az eredeti esemény az event grid-séma. Nincsenek segítségével testre szabhatja a leképezés három tulajdonságot:

* A `--input-schema` paraméter meghatározza a sémát. Az elérhető lehetőségek *cloudeventv01schema*, *customeventschema*, és *eventgridschema*. Az alapértelmezett érték: eventgridschema. Egyéni leképezés a séma- és az event grid-séma közötti létrehozásakor customeventschema használja. Amikor események válnak a CloudEvents-séma, cloudeventv01schema használja.

* A `--input-mapping-default-values` paraméter adja meg a mezők alapértelmezett értékeit, az Event Grid-sémában. Az alapértelmezett értékeket megadhatja a *tulajdonos*, *eventtype*, és *dataversion*. Jellemzően ezt a paramétert használni, amikor az egyéni séma nem tartalmaz egy olyan mező, ezek három mezőt valamelyikének felel meg. Például megadhatja, hogy dataversion értéke mindig **1.0**.

* A `--input-mapping-fields` paraméter leképezi a séma az event grid séma mezőket. Szóközzel elválasztott kulcs/érték párok értékeket határozhat meg. A kulcs nevét az event grid mező nevét használja. Az érték használja a mező nevét. Használhatja a kulcsnevek *azonosító*, *témakör*, *eventtime*, *tulajdonos*, *eventtype*, és *dataversion*.

Az alábbi példa létrehoz egy egyéni témakör egyes leképezve, és alapértelmezett mezők:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Fizessen elő az event grid-témakör

Amikor feliratkozik az egyéni témakörre, adja meg a séma, az események fogadására használni szeretné. Használja a `--event-delivery-schema` paraméter, illetve beállíthatja *cloudeventv01schema*, *eventgridschema*, vagy *inputeventschema*. Az alapértelmezett érték: eventgridschema.

Ebben a szakaszban szereplő példák a Queue storage használata az eseménykezelő. További információkért lásd: [egyéni események átirányítása az Azure Queue storage](custom-event-to-queue-storage.md).

Az alábbi példa feliratkozik egy event grid-témakört, és használja az alapértelmezett event grid séma:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

A következő példában a bemeneti sémát az esemény:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>A témakör esemény közzététele

Most már készen áll, küldünk egy eseményt az egyéni témakör és az eredmény a leképezés megjelenítéséhez. A következő szkriptet az esemény közzététele a [példa séma](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Most tekintse meg a Queue storage. A két előfizetés különböző sémákkal leszállítani eseményeket.

Az első előfizetés event grid séma használja. A szállított események formátuma:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Ezek a mezők a leképezéseket, az egyéni témakör tartalmaz. **myEventTypeField** le van képezve **EventType**. Az alapértelmezett értékei **DataVersion** és **tulajdonos** szolgálnak. A **adatok** objektum az eredeti esemény séma mezőket tartalmazza.

A második előfizetés használni a bemeneti eseményt séma. A szállított események formátuma:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Figyelje meg, hogy az eredeti mezők kézbesítése.

## <a name="next-steps"></a>További lépések

* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
