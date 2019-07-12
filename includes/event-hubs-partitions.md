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
ms.openlocfilehash: b23f9532aa1ca6f7bae914ff8cb9d7566a0fec86
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841564"
---
Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül, amelyben mindegyik felhasználó az üzenetstream csak egy adott részét, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el.

A partíció események egy rendezett sorozata az eseményközpontban. Ha új esemény érkezik, az a sorozat végére kerül. A partíció elképzelhető egy „véglegesítési naplóként”.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Eseményközpont összes partíciójára érvényes az eseményközpont konfigurált megőrzési időtartamig őrzi meg az adatokat. Az események időalapon évülnek el – nem törölhetők külön. Mivel a partíciók függetlenek egymástól, és saját adatsorozataikat tartalmazzák, gyakran különböző ütemben nőnek.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

A partíciók száma az eseményközpont létrehozásakor határozható meg, és 2 és 32 közé eshet. A partíciószám nem módosítható, a megadásakor tehát hosszú távú szempontokat érdemes mérlegelni. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. A partíciószám 32 fölé növeléséhez vegye fel a kapcsolatot az Event Hubs-csapattal.

Jóllehet a partíciók azonosíthatók, és közvetlenül lehet küldeni, nem ajánlott közvetlenül egy partíció küld. Ehelyett használhatja a bemutatott magasabb szintű szerkezeteket a [esemény-közzétevők](../articles/event-hubs/event-hubs-features.md#event-publishers) szakaszban. 

A partíciók eseményadatok az esemény, a felhasználó által definiált tulajdonságcsomagot és a metaadatokat, például eltolását a partícióban vagy a száma a streamsorozatban törzse tartalmazó sorozatát ki vannak töltve.

Azt javasoljuk, hogy optimális méretezhetőség 1:1 átviteli egységek és partíciók kiegyenlítése. Ugyanazon a partíción van egy garantált bejövő és kimenő forgalom a legfeljebb egy átviteli egységgel rendelkezhet. Előfordulhat, hogy a partíció nagyobb átviteli sebességet érhet el, amíg a teljesítmény nem garantált. Ezért erősen ajánlott, hogy az eseményközpontokban található partíciók számának nagyobbnak vagy azzal egyenlőnek átviteli egységek számát kell-e.

Adja meg a teljes átviteli sebesség a kellene tervezi, hogy a szükséges átviteli egységek számát és a partíciók minimális száma, de a hány partíciók kell, hogy? Válassza ki az alsóbb rétegbeli párhuzamosság használatával kíván elérni, valamint a jövőbeli átviteli igényei alapján a partíciók számát. Nem jár költségekkel rendelkezik egy eseményközpontban partíciók számát.

További információt a partíciókról és a rendelkezésre állás és a megbízhatóság közötti kellő egyensúly kialakításáról az [Event Hubs programozási útmutatójában](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) és az [Event Hubs rendelkezésre állásával és következetességével](../articles/event-hubs/event-hubs-availability-and-consistency.md) foglalkozó cikkben talál.
