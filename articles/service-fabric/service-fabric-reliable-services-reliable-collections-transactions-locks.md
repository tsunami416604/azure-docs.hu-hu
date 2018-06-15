---
title: Tranzakciók és a zárolási mód az Azure Service Fabric megbízható gyűjtemények |} Microsoft Docs
description: Az Azure Fabric megbízható állapot szolgáltatáskezelő és megbízható gyűjtemények tranzakciók és zárolása.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: 79be861a70abb0331d971b00e753691e77642637
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207366"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Tranzakciók és az Azure Service Fabric megbízható gyűjtemények zárolási mód

## <a name="transaction"></a>Tranzakció
Egy tranzakció a munka egyetlen logikai egységként végrehajtott műveletek sorozata.
Egy tranzakció kell mutatnak a következő ACID tulajdonságokat. (lásd: https://technet.microsoft.com/library/ms190612)
* **Atomicity**: egy tranzakciót egy atomi munkaegység kell lennie. Más szóval vagy az adatok változtatás történik, vagy egyikük sem történik.
* **Konzisztencia**: befejezése után egy tranzakció konzisztens állapotban kell hagynia minden adat. Az összes belső adatszerkezetek helyes-e a tranzakció végén kell lennie.
* **Elkülönítési**: párhuzamos tranzakciók által végrehajtott módosítások el kell különíteni a párhuzamos tranzakciók által végrehajtott módosítások a. A művelet végrehajtása a IReliableState belül egy ITransaction művelet használt elkülönítési szint határozza meg.
* **Tartóssági**: amikor egy tranzakció véget ért, hatásai vannak, ha véglegesen a rendszerben. A módosításokat továbbra is fennáll, még akkor is a rendszer hiba esetén.

### <a name="isolation-levels"></a>Elkülönítési szinten
Elkülönítési szint határozza meg, amelyhez a tranzakció legyen különítve a többi tranzakciók által végrehajtott módosítások a mértékben.
Két, a megbízható gyűjtemények támogatott elkülönítési szinten van:

* **Ismételhető olvasás**: meghatározza, hogy utasítás nem tudja olvasni a módosított, de más tranzakciók által még nem véglegesített adatokat, és, hogy más tranzakciók módosíthatja, hogy az aktuális tranzakció csak az aktuális tranzakció beolvasva adatok befejeződik. További részletekért lásd: [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Pillanatkép**: Megadja, hogy egy tranzakció utasítás által beolvasott adatok az adatokat, hogy a tranzakció kezdetekor tranzakciós úton megegyező verziójának.
  A tranzakció csak módosítása, hogy a tranzakció kezdete előtt véglegesítése volt felismerhető.
  Az aktuális tranzakció elindítása után más tranzakciók által végrehajtott módosítása utasítás végrehajtása az aktuális tranzakcióban nem láthatók.
  A hatás, mintha a tranzakció lévő utasítások adott verzióját a tranzakció kezdetekor kaphat a véglegesített adatokat pillanatképet.
  A pillanatképek egységesek megbízható gyűjtemények.
  További részletekért lásd: [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

Megbízható gyűjtemények automatikusan válasszon egy adott olvasási művelet attól függően, hogy a művelet és a replika szerepkörét az tranzakció létrehozásának időpontjában használandó elkülönítési szintjét.
Az alábbiakban az a táblázat, amely megbízható szótár és várólista műveletek elkülönítési szint alapértelmezései ábrázol.

| A művelet \ szerepkör | Elsődleges | Másodlagos |
| --- |:--- |:--- |
| Egyetlen entitás olvasása |Ismételhető olvasás |Pillanatkép |
| Számbavételi, száma |Pillanatkép |Pillanatkép |

> [!NOTE]
> Gyakori példák egyetlen entitás műveletekhez `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

A megbízható szótár és a megbízható várólistára egyaránt támogatja, olvassa el a ír.
Más szóval bármely írási tranzakción belül lesznek láthatók a következő olvasási, amely ugyanabban a tranzakcióban tartozik.

## <a name="locks"></a>Zárolások
A megbízható gyűjtemények szigorú tranzakciók megvalósítása két fázis zárolás: tranzakció nem zárolások a szerzett a tranzakció vagy megszakítás történt, vagy egy véglegesítési végén.

Megbízható szótár minden egyetlen entitás műveleteihez zárolás sor szintet használja.
Megbízható várólista lefolytathassa szigorú tranzakciós FIFO tulajdonság egyidejű ki.
Megbízható várólista művelet szintű zárolások engedélyezése egy tranzakciót használ `TryPeekAsync` és/vagy `TryDequeueAsync` és egy tranzakciót `EnqueueAsync` egyszerre.
Ne feledje, megőrzéséhez FIFO, ha egy `TryPeekAsync` vagy `TryDequeueAsync` valaha is figyelembe veszi, hogy a megbízható várólista üres, akkor azok is zárolják `EnqueueAsync`.

Az írási műveletek mindig egy kizárólagos zárolást.
Az olvasási műveletek a zárolás függ tényezők néhány.
Bármely olvasási művelet történik pillanatkép-elkülönítés használatával szabad zárolás.
Alapértelmezés szerint minden ismételhető olvasása a közös zárolás vesz igénybe.
Azonban bármely olvasási művelet, amely támogatja a ismételhető olvasható, a felhasználó helyett a közös zárolás módosítási zárolást is kérhet.
Módosítási zárolást az aszimmetrikus zár megakadályozható, hogy egy közös formája, amely akkor fordul elő, amikor több tranzakció egy későbbi időpontban lehetséges frissítések erőforrások zárolása holtpont.

A zárolás kompatibilitási mátrix a következő táblázatban található:

| Kérelem \ kap | None | Közös | Frissítés | Kizárólagos |
| --- |:--- |:--- |:--- |:--- |
| Közös |Ütközés |Ütközés |Ütközés |Ütközés |
| Frissítés |Ütközés |Ütközés |Ütközés |Ütközés |
| Kizárólagos |Ütközés |Ütközés |Ütközés |Ütközés |

A megbízható gyűjtemények API-k időtúllépési argumentumának holtpont észlelési szolgál.
Például két tranzakció (T1 és T2) olvassa és frissítse a K1 próbált.
Akkor lehet, hogy kölcsönös kizárás, mert mindkét végződhetnek a közös zárolás rendelkezik.
Ebben az esetben egyik vagy mindkét a műveletek lesznek túllépi az időkorlátot.

Ebben a forgatókönyvben holtpont példája egy nagyszerű hogyan módosítási zárolást előfordulhat, hogy holtpont.

## <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Megbízható szolgáltatások értesítések](service-fabric-reliable-services-notifications.md)
* [Megbízható szolgáltatások biztonsági mentése és visszaállítása (katasztrófa utáni helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapot Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

