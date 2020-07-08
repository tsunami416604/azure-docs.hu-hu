---
title: Hibrid kapcsolat továbbítása Azure Event Grid események eseménykezelőként
description: Ismerteti, hogyan használhatók Azure Relay hibrid kapcsolatok Azure Event Grid események eseménykezelői.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800889"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Hibrid kapcsolat továbbítása Azure Event Grid események eseménykezelőként
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és **Azure Relay** az egyikük. 

Az Azure **Relay hibrid kapcsolatok** használatával eseményeket küldhet a vállalati hálózaton belüli alkalmazásoknak, és nem rendelkezik nyilvánosan elérhető végponttal.

## <a name="tutorials"></a>Oktatóanyagok
Tekintse meg a következő oktatóanyagot, amely egy Azure Relay hibrid kapcsolat eseménykezelőként való használatát szemlélteti. 

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: események küldése hibrid kapcsolódásra](custom-event-to-hybrid-connection.md) | Egyéni eseményt küld egy meglévő hibrid kapcsolatra egy figyelő alkalmazás általi feldolgozáshoz. |

## <a name="rest-example-for-put"></a>REST-példa (PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>További lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 