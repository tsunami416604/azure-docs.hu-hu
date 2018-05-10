---
title: Egyéni mező leképezése Azure esemény rács séma
description: Az egyéni séma átalakítása az Azure Event rács séma ismerteti.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 08d1e1ad93eb69d6749860348d13a64078ed1993
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Egyéni mezők leképezése esemény rács séma

Ha az esemény adatok nem egyezik a várt [esemény rács séma](event-schema.md), továbbra is használhatja az esemény rács útvonal eseményre előfizetőknek. A cikkből megtudhatja, hogyan képezheti a sémát a esemény rács séma.

## <a name="original-event-schema"></a>Eredeti esemény séma

Tegyük fel, hogy egy alkalmazás az események küldése a következő formátumban:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bár az, hogy a formátum nem egyezik meg a szükséges séma, esemény rács lehetővé teszi, hogy a mezők hozzárendelését a séma. Vagy az eredeti sémában is fogadhatja az értékeket.

## <a name="create-custom-topic-with-mapped-fields"></a>Hozzon létre egyéni témakör csatlakoztatott mezők

Egy egyéni téma létrehozásakor adja meg, hogyan képezheti mezők az eredeti eseményből származó esemény rács séma. Három tulajdonságok segítségével testre szabhatja a leképezés van:

* A `--input-schema` paraméter határozza meg a sémában. Az elérhető lehetőségek *cloudeventv01schema*, *customeventschema*, és *eventgridschema*. Az alapértelmezett érték: eventgridschema. A séma- és az esemény rács séma egyéni leképezése létrehozásakor használjon customeventschema. Ha az események a CloudEvents séma vannak, használja a cloudeventv01schema.

* A `--input-mapping-default-values` paraméter határozza meg a mezők alapértelmezett értékét, az esemény rács sémában. Megadhatja az alapértelmezett értékeket *tulajdonos*, *eventtype*, és *dataversion*. Általában a paraméter használata, ha az egyéni séma nem tartalmaz olyan mezője, amely három mezőket valamelyikének felel meg. Például megadhatja, hogy dataversion értéke mindig **1.0**.

* A `--input-mapping-fields` paraméter leképezi a séma mezőit a esemény rács sémában. Szóközökkel elválasztott kulcs/érték párok értékeket ad meg. A kulcs neve az esemény a rács mező nevét használja. Az érték a mező nevét használja. Használhatja a kulcs neve *azonosító*, *témakör*, *eventtime*, *tulajdonos*, *eventtype*, és *dataversion*.

Az alábbi példa létrehoz egy egyéni témakör egy leképezett, és az alapértelmezett mezők:

```azurecli-interactive
az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Fizessen elő a esemény rács témakör

Amikor előfizet az egyéni témakör, adja meg a séma szeretné használni az események fogadását. Használja a `--event-delivery-schema` paramétert, majd állítsa be *cloudeventv01schema*, *eventgridschema*, vagy *inputeventschema*. Az alapértelmezett érték: eventgridschema.

Ebben a szakaszban szereplő példák a Queue storage használata az eseménykezelő. További információkért lásd: [egyéni események irányításához az Azure Queue Storage](custom-event-to-queue-storage.md).

A következő példa egy esemény rács témakör számítógépcsoportra fizetett elő, és az alapértelmezett esemény rács sémát használja:

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

Most már készen áll az esemény küldése a egyéni témakört, és az eredményt a leképezés. A következő parancsfájl egy eseményre visszaküldeni a [példa séma](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Most tekintse meg a várólista-tároló. A két előfizetések események i eltérő sémákban.

Az első előfizetés esemény rács séma használja. A kézbesített esemény formátuma:

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

Ezeket a mezőket tartalmaz, a leképezések egyéni témakörben. **myEventTypeField** van leképezve **EventType**. Az alapértelmezett értékei **DataVersion** és **tulajdonos** szolgálnak. A **adatok** objektum az eredeti esemény séma mezőket tartalmazza.

A második előfizetés a bemeneti esemény séma használja. A kézbesített esemény formátuma:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Figyelje meg, az eredeti mezők kapott.

## <a name="next-steps"></a>További lépések

* Esemény kézbesítési és, az újrapróbálkozásokat információt [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
