---
title: Event hub Azure Event Grid események eseménykezelője
description: Ismerteti, hogyan használható az Event hub Azure Event Grid eseményekhez eseménykezelőként.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598366"
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
Ha az **Event hub** -t a Event Gridból származó események eseménykezelője használja, állítsa be a következő üzeneteket: 

| Tulajdonság neve | Leírás |
| ------------- | ----------- | 
| AEG-előfizetés – név | Az esemény-előfizetés neve. |
| AEG – kézbesítés – darabszám | <p>Az eseményre tett kísérletek száma.</p> <p>Példa: "1"</p> |
| AEG – eseménytípus | <p>Az esemény típusa.</p><p> Például: "Microsoft. Storage. blobCreated"</p> | 
| AEG – metaadatok – verzió | <p>Az esemény metaadat-verziója.</p> <p>Példa: "1".</p><p> **Event Grid Event Schema**esetében ez a tulajdonság a metaadat-verziót és a **Felhőbeli esemény sémáját**jelöli, amely a **spec verziót**jelöli. </p>|
| AEG – adatverzió | <p>Az esemény adatverziója.</p><p>Példa: "1".</p><p>**Event Grid Event Schema**esetében ez a tulajdonság az adatverziót és a **Felhőbeli esemény sémáját**jelöli, nem érvényes.</p> |
| AEG-output-Event-ID | A Event Grid esemény azonosítója. |


## <a name="next-steps"></a>További lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
