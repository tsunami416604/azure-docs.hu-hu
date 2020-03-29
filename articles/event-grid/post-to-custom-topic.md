---
title: Esemény bejegyzése az Azure Event Grid egyéni témakörében
description: Ez a cikk azt ismerteti, hogyan lehet eseményt közzétenni egy egyéni témakörben. Ez azt mutatja, a formátum a posta és az esemény adatait.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721557"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Közzététel az Azure Event Grid egyéni témakörében

Ez a cikk azt ismerteti, hogyan lehet eseményt közzétenni egy egyéni témakörben. Ez azt mutatja, a formátum a posta és az esemény adatait. A [szolgáltatásiszint-szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) csak a várt formátumnak megfelelő bejegyzésekre vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Végpont

Amikor a HTTP POST-ot egyéni témakörbe `https://<topic-endpoint>?api-version=2018-01-01`küldi, használja az URI formátumot: .

Egy érvényes URI például `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`a következő: .

Ha be szeretné szerezni egy egyéni témakör végpontja az Azure CLI használatával, használja:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Ha be szeretné szerezni egy egyéni témakör végpontja az Azure PowerShell használatával, használja:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Fejléc

A kérelemben adjon meg `aeg-sas-key` egy nevű fejlécértéket, amely hitelesítési kulcsot tartalmaz.

Például egy érvényes fejlécérték . `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`

Az Azure CLI-vel egy egyéni témakör kulcsának beszerezéséhez használja a következőket:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Ha be szeretné szerezni egy egyéni témakör kulcsát a PowerShell használatával, használja a következőket:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Eseményadatok

Egyéni témakörök esetén a legfelső szintű adatok ugyanazokat a mezőket tartalmazzák, mint a szabványos erőforrás-definiált események. Az egyik ilyen tulajdonság egy adattulajdonság, amely az egyéni témakör egyedi tulajdonságait tartalmazza. Eseményközzétevőként meghatározhatja az adatobjektum tulajdonságait. Használja a következő sémát:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Ezeknek a tulajdonságoknak a leírását lásd: [Azure Event Grid eseményséma](event-schema.md). Események eseményrács-témakörbe történő feladásakor a tömb teljes mérete legfeljebb 1 MB lehet. A tömb minden eseménye 64 KB (általános rendelkezésre állás) vagy 1 MB (előzetes verzió) lehetséges.

> [!NOTE]
> A legfeljebb 64 KB méretű eseményekre az általános rendelkezésre állási (GA) szolgáltatásiszint-szerződés (SLA) vonatkozik. Az 1 MB-os méretű események támogatása jelenleg előzetes verzióban érhető el. A 64 KB feletti eseményeket 64 KB-os lépésekben számítjuk fel. 

Egy érvényes eseményadat-séma például a következő:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Válasz

A témakör végpontjára való közzététel után választ kap. A válasz egy szabványos HTTP-válaszkód. Néhány gyakori válasz:

|Eredmény  |Válasz  |
|---------|---------|
|Sikeres  | 200 OK  |
|Az eseményadatok formátuma nem megfelelő | 400 rossz kérés |
|Érvénytelen hozzáférési kulcs | 401 Jogosulatlan |
|Helytelen végpont | 404 Nem található |
|A tömb vagy esemény túllépi a méretkorlátokat | 413 Hasznos teher túl nagy |

Hibák esetén az üzenet törzsének formátuma a következő:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésfigyelésről az [Eseményrács üzenetkézbesítésének figyelése](monitor-event-delivery.md)című témakörben talál további információt.
* A hitelesítési kulcsról további információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
