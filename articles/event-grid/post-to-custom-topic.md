---
title: "Egyéni Azure esemény rács a témakör a feladás egy vagy több esemény"
description: "Ismerteti, hogyan lehet egy adott esemény egy egyéni témakör utáni Azure esemény rács"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
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

## <a name="next-steps"></a>További lépések

* Egyéni események útválasztási bemutatása, lásd: [az Azure CLI és az esemény rács létrehozása és útvonal egyéni események](custom-event-quickstart.md) vagy [Azure PowerShell és az esemény rács létrehozása és útvonal egyéni események](custom-event-quickstart-powershell.md).
* A hitelesítési kulcs kapcsolatos további információkért lásd: [esemény rács biztonsági és hitelesítési](security-authentication.md).
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
