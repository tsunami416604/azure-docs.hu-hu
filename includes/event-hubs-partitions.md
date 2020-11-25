---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 48cc6b84fe88676a03d1bb6e0a8154c16e3ef618
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007434"
---
Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül, amelyben mindegyik felhasználó az üzenetstream csak egy adott részét, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el.

A partíció események egy rendezett sorozata az eseményközpontban. Ha új esemény érkezik, az a sorozat végére kerül. A partíció elképzelhető egy „véglegesítési naplóként”.

![A régebbi és újabb squence megjelenítő diagram.](./media/event-hubs-partitions/partition.png)

Event Hubs megőrzi az adatok egy konfigurált megőrzési időt, amely az Event hub összes partícióján érvényes. Az események időalapon évülnek el – nem törölhetők külön. Mivel a partíciók függetlenek egymástól, és saját adatsorozataikat tartalmazzák, gyakran különböző ütemben nőnek.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

A partíciók száma a létrehozáskor van megadva, és 1 és 32 között kell lennie. A partíciószám nem módosítható, a megadásakor tehát hosszú távú szempontokat érdemes mérlegelni. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. A partíciószám 32 fölé növeléséhez vegye fel a kapcsolatot az Event Hubs-csapattal.

A létrehozás időpontjában érdemes lehet beállítani a lehető legmagasabb értéket, amely a 32. Ne feledje, hogy több partíció használata esetén a rendszer több partícióba küldi az eseményeket anélkül, hogy megtartja a sorrendet, kivéve, ha a küldőket úgy konfigurálja, hogy csak egyetlen, a 32-es partíción kívülről küldje el a fennmaradó 31 partíciót. Az előző esetben az összes 32-partíción el kell olvasnia az eseményeket. Az utóbbi esetben az Event Processor Host-on kívüli további konfigurációtól eltekintve nincs nyilvánvaló további díj.

Amíg a partíciók azonosíthatók, és közvetlenül is küldhetők, a közvetlenül a partícióra történő küldés nem ajánlott. Ehelyett az [esemény-közzétevők](../articles/event-hubs/event-hubs-features.md#event-publishers) szakaszban bemutatott magasabb szintű szerkezeteket is használhat. 

A partíciók az esemény törzsét, a felhasználó által definiált tulajdonságok táskáját és a metaadatokat (például a partícióban lévő eltolást, illetve az adatfolyam-sorozatot tartalmazó adatokat) tartalmazó sorozattal vannak kitöltve.

Javasoljuk, hogy az optimális méretezés érdekében a 1:1 átviteli egységek és partíciók egyenlegét. Egy partíción egy-egy átviteli egység garantált bejövő és kimenő forgalmú. Míg előfordulhat, hogy a partíción magasabb átviteli sebesség érhető el, a teljesítmény nem garantált. Ezért azt javasoljuk, hogy az Event hub-ban lévő partíciók száma ne legyen nagyobb vagy egyenlő az átviteli egységek számával.

Az igénybe venni kívánt teljes átviteli sebesség miatt tudja, hogy hány átviteli egységre van szüksége, és a partíciók minimális száma, de hány partícióra van szükség? Válassza ki a partíciók számát az elérni kívánt alsóbb szintű párhuzamosságok alapján, valamint a jövőbeli átviteli sebességi igényeket. Az Event hub-ban lévő partíciók száma díjmentes.

További információt a partíciókról és a rendelkezésre állás és a megbízhatóság közötti kellő egyensúly kialakításáról az [Event Hubs programozási útmutatójában](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) és az [Event Hubs rendelkezésre állásával és következetességével](../articles/event-hubs/event-hubs-availability-and-consistency.md) foglalkozó cikkben talál.
