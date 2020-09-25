---
title: Event hub Azure Event Grid események eseménykezelője
description: Ismerteti, hogyan használható az Event hub Azure Event Grid eseményekhez eseménykezelőként.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 5e6a84c1737c6b8a575f47576aeb1d3d9efae6eb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322563"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event hub Azure Event Grid események eseménykezelője
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő végrehajt egy műveletet az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és az **azure Event Hubs** az egyik. 

Akkor használja a **Event Hubst** , ha a megoldás a Event Gridnál gyorsabban beolvassa az eseményeket, mint amennyit fel tud dolgozni. Ha az események egy Event hub-ban találhatók, az alkalmazás a saját időpontjában képes feldolgozni az Event hub eseményeit. Az események feldolgozását méretezheti úgy, hogy kezelni tudja a bejövő eseményeket.

## <a name="tutorials"></a>Oktatóanyagok
Lásd az alábbi példákat: 

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események irányítása az Azure Event Hubs az Azure CLI-vel](custom-event-to-eventhub.md) | Egyéni eseményt küld egy Event hub-nak egy alkalmazás általi feldolgozáshoz. |
| [Resource Manager-sablon: Event Grid egyéni témakör létrehozása és események küldése az Event hubhoz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablon, amely létrehoz egy egyéni témakörhöz tartozó előfizetést. Eseményeket küld egy Azure-Event Hubs. |

## <a name="message-properties"></a>Üzenet tulajdonságai
Ha az **Event hub** -t a Event Grid eseményeihez tartozó esemény-kezelőként használja, akkor az üzenetek fejlécében megjelenő tulajdonságok a következők: 

| Tulajdonság neve | Description |
| ------------- | ----------- | 
| AEG-előfizetés – név | Az esemény-előfizetés neve. |
| AEG – kézbesítés – darabszám | <p>Az eseményre tett kísérletek száma.</p> <p>Példa: "1"</p> |
| AEG – eseménytípus | <p>Az esemény típusa.</p><p> Például: "Microsoft. Storage. blobCreated"</p> | 
| AEG – metaadatok – verzió | <p>Az esemény metaadat-verziója.</p> <p>Példa: "1".</p><p> **Event Grid Event Schema**esetében ez a tulajdonság a metaadat-verziót és a **Felhőbeli esemény sémáját**jelöli, amely a **spec verziót**jelöli. </p>|
| AEG – adatverzió | <p>Az esemény adatverziója.</p><p>Példa: "1".</p><p>**Event Grid Event Schema**esetében ez a tulajdonság az adatverziót és a **Felhőbeli esemény sémáját**jelöli, nem érvényes.</p> |
| AEG-output-Event-ID | A Event Grid esemény azonosítója. |

## <a name="rest-examples-for-put"></a>REST-példák (PUT)


### <a name="event-hub"></a>Eseményközpont

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event hub – kézbesítés felügyelt identitással

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> Az események egy **másik bérlőben** lévő Azure Event hubhoz való továbbítása nem támogatott. 

## <a name="next-steps"></a>Következő lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
