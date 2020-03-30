---
title: Eseményvezérelt architektúrák a peremhálózaton – Azure Event Grid az IoT Edge-en
description: Az Azure Event Grid et az IoT Edge-en modulként használhatja a modulok, a peremhálózati eszközök és a felhő közötti továbbítási eseményekhez.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844656"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Mi az Azure Event Grid az Azure IoT Edge-en?
Event Grid az IoT Edge-en hozza a hatalom és a rugalmasság az Azure Event Grid a szélén. Témakörök létrehozása, események közzététele és több célelőfizetés, függetlenül attól, hogy azok modulok ugyanazon az eszközön, más peremhálózati eszközökön vagy szolgáltatások a felhőben.

Csakúgy, mint a felhőben, az IoT Edge-modul Eseményrács kezeli az útválasztást, a szűrést és az események megbízható nagy mértékű kézbesítését. Szűrje az eseményeket annak érdekében, hogy a rendszer speciális karakterlánc-, numerikus és logikai szűrőkkel csak a releváns eseményeket küldje el a különböző eseménykezelőknek. Az újrapróbálkozási logika gondoskodik arról, hogy az esemény akkor is elérje a célcélt, ha az nem érhető el a közzététel időpontjában. Lehetővé teszi, hogy az IoT Edge-en az Event Gridet hatékony tároló és előrekapcsoló mechanizmusként használja.

Az IoT Edge Event Grid támogatja a CloudEvents 1.0-s verzióját és az egyéni eseménysémákat is. Azt is támogatja ugyanazt a pub/sub szemantika, mint Event Grid a felhőben a könnyű együttműködés érdekében.

Ez a cikk áttekintést nyújt az Azure Event Grid ioT Edge-en. A modul peremhálózati használatához részletes útmutatást a [Közzététel, feliratkozás helyi eseményekre.](pub-sub-events-webhook-local.md) 

![A források és kezelők IoT Edge-modelljének eseményrácsa](../media/edge-overview/functional-model.png)

Ez a kép az Event Grid IoT Edge-en való használatának néhány módját mutatja be, és nem a támogatott funkciók átfogó listáját jeleníti meg.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Mikor kell használni az Event Gridet az IoT Edge-en?

Event Grid az IoT Edge-en egy könnyen használható, megbízható eseménykörnyezetés modell a peremhálózat és a felhő között.

Az IoT Edge-en futó Event Grid egy szimmetrikus futásidejű felületi területtel rendelkezik az Azure felhőszolgáltatásszámára, így bárhol használhatja ugyanazokat az eseményeket és API-hívásokat. Akár pub/sub a felhőben, a szélén, vagy a kettő között, Event Grid az IoT Edge most már az egyetlen go-to megoldás.

Az IoT Edge Eseményrács használatával egyszerű munkafolyamatokat indíthat el a modulok között. Például hozzon létre egy témakört, és tegye közzé a "storage blob created" eseményeket a tárolómodulból a témakörben. Most már feliratkozhat egy vagy több függvényt vagy egyéni modult az adott témakörökre.

Bővítse a funkciókat a peremhálózati eszközök között. Ha blobmodul-eseményeket tesz közzé, és több peremhálózati eszköz számítási erejét szeretné használni, hozzon létre eszközök közötti előfizetéseket.

Végül csatlakozzon a felhőhöz. Ha a blobmodul-eseményeket rendszeres időközönként szinkronizálni kell a felhővel, használja a felhőben elérhető nagyobb számítási kapacitást, vagy küldje el a feldolgozott adatokat, hozzon létre további felhőszolgáltatási előfizetéseket.

Az IoT Edge Eseményrács rugalmas és megbízható, függetlenített eseményszervezési architektúrát biztosít.

## <a name="event-sources"></a>Eseményforrások

Hasonlóan a felhőhöz, az IoT Edge Event Grid lehetővé teszi a modulok közötti közvetlen integrációt az eseményvezérelt architektúrák létrehozásához. Jelenleg az események az IoT Edge-en lévő Event Gridbe küldhetők a következő innen:

* Azure Blob Storage az IoT Edge-ben
* CloudEvents források
* Egyéni modulok & a http post-on keresztüli tárolók

## <a name="event-handlers"></a>Eseménykezelők

Az IoT Edge Eseményrács úgy van kialakítva, hogy az eseményeket bárhová elküldhesse. Jelenleg a következő úti célok támogatottak:

* Egyéb modulok, beleértve az IoT Hubot, a funkciókat és az egyéni modulokat
* Egyéb peremhálózati eszközök
* Webhookok
* Azure Event Grid felhőszolgáltatás
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák
* Tárolási üzenetsorok

## <a name="supported-environments"></a>Támogatott környezetek
Jelenleg a Windows 64 bites, a Linux 64 bites és az ARM 32 bites környezetek támogatottak.

## <a name="concepts"></a>Alapelvek

Az Azure Event Grid öt fogalma tengedelheti az első lépésekhez:

* **Események** — Mi történt.
* **Eseményforrások** – Ahol az esemény történt.
* **Témakörök** – Az a végpont, ahol a közzétevők eseményeket küldenek.
* **Esemény-előfizetések** – A végpont vagy a beépített mechanizmus események, néha több kezelő. Az előfizetéseket a kezelők is használják a bejövő események intelligens szűrésére.
* **Eseménykezelők** – Az eseményre reagáló alkalmazás vagy szolgáltatás.

## <a name="cost"></a>Költségek

Az IoT Edge Eseményrács ingyenes a nyilvános előzetes verzióban.

## <a name="issues"></a>Hibák
Jelentse az Event Grid ioT Edge-en való használatával kapcsolatos problémákat a rendszerben. [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)

## <a name="next-steps"></a>További lépések

* [Közzététel, feliratkozás helyi eseményekre](pub-sub-events-webhook-local.md)
* [Közzététel, feliratkozás eseményekre a felhőben](pub-sub-events-webhook-cloud.md)
* [Események továbbítása az Event Grid felhőbe](forward-events-event-grid-cloud.md)
* [Események továbbítása az IoTHubra](forward-events-iothub.md)
* [Reagálás Blob Storage-eseményekre helyben](react-blob-storage-events-locally.md)