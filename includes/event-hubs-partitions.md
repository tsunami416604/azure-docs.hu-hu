---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481469"
---
Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül, amelyben mindegyik felhasználó az üzenetstream csak egy adott részét, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el.

A partíció események egy rendezett sorozata az eseményközpontban. Ha új esemény érkezik, az a sorozat végére kerül. A partíció elképzelhető egy „véglegesítési naplóként”.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Az Event Hubs megőrzi az adatokat egy konfigurált megőrzési időre, amely az eseményközpont összes partíciójára vonatkozik. Az események időalapon évülnek el – nem törölhetők külön. Mivel a partíciók függetlenek egymástól, és saját adatsorozataikat tartalmazzák, gyakran különböző ütemben nőnek.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

A partíciók száma az eseményközpont létrehozásakor határozható meg, és 2 és 32 közé eshet. A partíciószám nem módosítható, a megadásakor tehát hosszú távú szempontokat érdemes mérlegelni. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. A partíciószám 32 fölé növeléséhez vegye fel a kapcsolatot az Event Hubs-csapattal.

Előfordulhat, hogy a létrehozás időpontjában a lehető legmagasabb értéket, azaz 32 értéket szeretné beállítani. Ne feledje, hogy egynél több partícióval eredményez több partícióra küldött eseményeket anélkül, hogy megtartaná a sorrendet, kivéve, ha úgy konfigurálja a küldőket, hogy csak a 32-ből csak egy partícióra küldjenek, így a fennmaradó 31 partíció felesleges. Az előbbi esetben mind a 32 partíción végig kell olvasnia az eseményeket. Az utóbbi esetben nincs nyilvánvaló többletköltség eltekintve az extra konfigurációt kell tennie az Event Processor Host.

Míg a partíciók azonosíthatók, és közvetlenül elküldhetők, a partícióra való közvetlen küldés nem ajánlott. Ehelyett használhatja az Esemény közzétevői szakaszban bevezetett magasabb szintű [konstrukciókat.](../articles/event-hubs/event-hubs-features.md#event-publishers) 

A partíciók eseményadatok sorozatával vannak kitöltve, amely tartalmazza az esemény törzsét, a felhasználó által definiált tulajdonságtáskát és metaadatokat, például a partícióban való eltolását és az adatfolyam-sorozatban lévő számát.

Azt javasoljuk, hogy az optimális skálázás elérése érdekében 1:1 átviteli egységek és partíciók egyensúlyba. Egyetlen partíció garantált be- és kimenő forgalom egy adott átviteli egység garantált anamnézisével rendelkezik. Bár előfordulhat, hogy nagyobb átviteli teljesítményt érhet el egy partíción, a teljesítmény nem garantált. Ezért javasoljuk, hogy az eseményközpontban lévő partíciók száma nagyobb vagy egyenlő legyen az átviteli egységek számával.

Tekintettel a teljes átviteli igény, tudja, hogy hány átviteli egységek van szüksége, és a partíciók minimális száma, de hány partíciót kell rendelkeznie? Válassza ki a partíciók számát az elérni kívánt alsóbb rétegbeli párhuzamosság, valamint a jövőbeli átviteli igények alapján. Az Event Hubon belül lévő partíciók száma díjmentes.

További információt a partíciókról és a rendelkezésre állás és a megbízhatóság közötti kellő egyensúly kialakításáról az [Event Hubs programozási útmutatójában](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) és az [Event Hubs rendelkezésre állásával és következetességével](../articles/event-hubs/event-hubs-availability-and-consistency.md) foglalkozó cikkben talál.
