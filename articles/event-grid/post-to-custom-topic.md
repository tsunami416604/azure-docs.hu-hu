---
title: Esemény közzététele egyéni Azure Event Grid témakörben
description: Ez a cikk azt ismerteti, hogyan lehet elküldeni egy eseményt egy egyéni témakörbe. Megjeleníti a bejegyzés és az esemény formátumát.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721557"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Közzététel a Azure Event Grid egyéni témakörében

Ez a cikk azt ismerteti, hogyan lehet elküldeni egy eseményt egy egyéni témakörbe. Megjeleníti a bejegyzés és az esemény formátumát. A [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) csak a várt formátumnak megfelelő bejegyzésekre vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Végpont

Ha a HTTP-BEJEGYZÉST egyéni témakörbe küldi, használja a következő URI-formátumot: `https://<topic-endpoint>?api-version=2018-01-01`.

Érvényes URI például a következő: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Az Azure CLI-vel rendelkező egyéni témakör végpontjának beszerzéséhez használja a következőt:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Ahhoz, hogy a végpontot a Azure PowerShell használó egyéni témakörhöz lehessen beolvasni, használja a következőt:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Fejléc

A kérelemben adjon meg egy `aeg-sas-key` nevű fejléc-értéket, amely a hitelesítés kulcsát tartalmazza.

Egy érvényes fejléc-érték például `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Az Azure CLI-vel való egyéni témakör kulcsának beszerzéséhez használja a következőt:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

A PowerShell-lel rendelkező egyéni témakör kulcsának beszerzéséhez használja a következőt:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Eseményadatok

Egyéni témakörök esetén a legfelső szintű adat ugyanazokat a mezőket tartalmazza, mint a szabványos erőforrás-definíciós események. Ezen tulajdonságok egyike egy olyan adattulajdonság, amely az egyéni témakörben egyedi tulajdonságokat tartalmaz. Esemény-közzétevőként meg kell határoznia az adatobjektum tulajdonságait. Használja az alábbi sémát:

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

A tulajdonságok leírását itt tekintheti meg: [Azure Event Grid Event Schema](event-schema.md). Amikor eseményeket küld egy Event Grid-témakörbe, a tömb legfeljebb 1 MB méretű lehet. A tömbben lévő összes esemény 64 KB-ra (általános rendelkezésre állás) vagy 1 MB-ra (előzetes verzió) korlátozódik.

> [!NOTE]
> A 64 KB-ig terjedő méretű események általánosan elérhetők (GA) szolgáltatói szerződés (SLA). A legfeljebb 1 MB méretű esemény támogatása jelenleg előzetes verzióban érhető el. Az 64 KB-nál nagyobb számú esemény díja 64 KB. 

Egy érvényes esemény-Adatséma például a következő:

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

A témakör-végpontra való közzététel után választ kap. A válasz egy szabványos HTTP-válasz kódja. Néhány gyakori Válasz:

|Eredmény  |Válasz  |
|---------|---------|
|Sikeres  | 200 OK  |
|Az esemény adatformátuma helytelen formátumú | 400 hibás kérelem |
|Érvénytelen hozzáférési kulcs | 401 jogosulatlan |
|Helytelen végpont | 404 – Nem található |
|Tömb vagy esemény meghaladja a méretkorlátot | 413 túl nagy a hasznos adat |

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

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* További információ a hitelesítési kulcsról: [Event Grid biztonság és hitelesítés](security-authentication.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
