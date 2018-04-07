---
title: Egyéni Azure esemény rács a témakör a feladás egy vagy több esemény
description: Ismerteti, hogyan lehet egy adott esemény egy egyéni témakör utáni Azure esemény rács
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 1c23aef0773ffddbc26e4090ecf137b632394ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Küldje el a témakör egyéni Azure esemény rács

Ez a cikk ismerteti, hogyan elküldeni egy eseményt, egy egyéni témakörbe. Azt illusztrálja, post és esemény adatok formátuma. A [szolgáltatási szint szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) bejegyzéseket, amelyek megfelelnek a várt formátum csak vonatkozik.

## <a name="endpoint"></a>Végpont

A HTTP POST egy egyéni témához küldésekor használja az URI-formátum: `https://<topic-endpoint>?api-version=2018-01-01`.

Például egy érvényes URI-cím lesz: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

A végpont Azure parancssori felület segítségével egyéni témakör megtekintéséhez használja:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

A végpont egy egyéni témakör az Azure PowerShell használatához:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Fejléc

A kérelem tartalmazza a nevű fejléc értéke `aeg-sas-key` , amely tartalmazza a hitelesítési kulcs.

Például egy érvényes fejléc értéke `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Azure parancssori felület segítségével egyéni témakör kulcs használatához:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

A PowerShell segítségével egyéni témakör kulcs használatához:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Eseményadatok

Egyéni témaköröket a legfelső szintű adatok tartalmazzák a szabványos erőforrás által definiált eseményként is ugyanazokat a mezőket. Ezek a tulajdonságok egyike, amelyben az egyéni témakörbe egyedi tulajdonságok adatok tulajdonság. Esemény-közzétevő, mint az adatok objektum tulajdonságok meghatározásához. A következő séma használata:

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

Ezeket a tulajdonságokat, olvassa el [Azure esemény rács esemény séma](event-schema.md).

Például egy érvényes esemény adatkulcsokat van:

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

A témakör a végponthoz könyvelés után kapott választ. A rendszer egy szabványos HTTP-válaszkód a választ. Néhány gyakori válaszok a következők:

|Eredmény  |Válasz  |
|---------|---------|
|Sikeres  | 200 OK  |
|Helytelen végpont | 404 – Nem található |
|Érvénytelen a hozzáférési kulcsot | 401 nem engedélyezett |
|Eseményadatok formátuma nem megfelelő | 400 Hibás kérés |

A hibákat az üzenet törzse formátuma a következő:

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

* Esemény kézbesítések figyelésével kapcsolatos további információkért lásd: [figyelő esemény rács üzenetkézbesítést](monitor-event-delivery.md).
* A hitelesítési kulcs kapcsolatos további információkért lásd: [esemény rács biztonsági és hitelesítési](security-authentication.md).
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
