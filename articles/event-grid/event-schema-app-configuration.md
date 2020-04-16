---
title: Az Azure App konfigurációja eseményrács-forrásként
description: Ez a cikk ismerteti, hogyan használhatja az Azure App Konfiguráció eseményrács eseményforrásként. Ez biztosítja a séma és hivatkozásokat bemutató és útmutató cikkeket.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393431"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Az Azure App konfigurációja eseményrács-forrásként
Ez a cikk az Azure App Configuration események tulajdonságait és sémáját tartalmazza. Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md) Emellett az Azure App Configuration eseményforrásként való használatához az Azure App Configuration használatával kapcsolatos gyorsindítások és oktatóanyagok listáját is tartalmazza.

## <a name="event-grid-event-schema"></a>Eseményrács eseménysémája

### <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure App Konfigurációja a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Kulcsérték létrehozásakor vagy cseréjekor keletkezik. |
| Microsoft.AppConfiguration.KeyValueTörölve | Kulcsérték törlésekor előad. |

### <a name="example-event"></a>Példa esemény

A következő példa egy kulcsérték-módosított esemény sémáját mutatja be: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

A törölt kulcsérték-esemény sémája hasonló: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| ID (Azonosító) | sztring | Az esemény egyedi azonosítója |
| data | objektum | Alkalmazáskonfigurációs eseményadatok. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| kulcs | sztring | A módosított vagy törölt kulcsérték kulcsa. |
| label | sztring | A módosított vagy törölt kulcsérték címkéje(ha van). |
| Etag | sztring | Az `KeyValueModified` új kulcs-érték e-címkéje esetén. A `KeyValueDeleted` törölt kulcsérték e-címkéje esetén. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók

|Cím | Leírás |
|---------|---------|
| [Reagálás az Azure App konfigurációs eseményeire az Event Grid használatával](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure App Configuration és az Event Grid integrálásának áttekintése. |
| [Rövid útmutató: az Azure App Configuration események irányítása egy egyéni webes végpontra az Azure CLI-vel](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan azure CLI-vel küldhet Azure App Configuration események egy WebHook. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
* Az Azure App Configuration eseményekkel való együttműködés bemutatása az [Azure App Konfigurációs események – Azure CLI útvonala](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)című témakörben található. 