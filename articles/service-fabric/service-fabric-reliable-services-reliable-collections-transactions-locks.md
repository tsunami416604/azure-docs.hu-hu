---
title: Tranzakciók és a zárolási mód az Azure Service Fabric Reliable Collections |} A Microsoft Docs
description: Az Azure Service Fabric Reliable State Manager és a Reliable Collections tranzakciók és zárolását.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 246606792797afe0e57dbe2e582c4e94df3133d4
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806436"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Tranzakciók és az Azure Service Fabric Reliable Collections zárolási mód

## <a name="transaction"></a>Tranzakció
Egy tranzakció a munka egyetlen logikai egységként végrehajtott műveletek sorozata.
Egy tranzakció a következő ACID tulajdonságokat kell mutatnak. (lásd: https://technet.microsoft.com/library/ms190612)
* **Atomitást**: Egy tranzakció egy atomi munkaegység kell lennie. Más szóval vagy az adatok minden módosítás el kell végezni, vagy egyiket sem történik.
* **Konzisztencia**: Befejezése után az egy tranzakció összes adatot kell konzisztens állapotban hagyja. Az összes belső adatszerkezetek helyes-e a tranzakció végén kell lennie.
* **Elkülönítés**: Egyidejű tranzakciók által végrehajtott módosítások a bármely más egyidejű tranzakciók által végrehajtott módosítások elkülönítve kell lennie. A művelet végrehajtása a IReliableState egy ITransaction belül egy művelethez használt elkülönítési szintjét határozza meg.
* **Tartóssági**: Amikor egy tranzakció véget ért, hatásai vannak véglegesen helyen a rendszer. A módosítások megmarad, még akkor is a rendszer hiba esetén.

### <a name="isolation-levels"></a>Elkülönítési szinten
Elkülönítési szintet határozza meg, amelyhez a tranzakció el kell különíteni a módosítások fényében, amelyek más tranzakciók által adatforrástól.
Nincsenek két elkülönítési szint, amely a Reliable Collections használata támogatott:

* **Megismételhető olvasási**: Megadja, hogy az utasítások módosul, hanem más tranzakciók által még nem véglegesített adatokat nem tudja olvasni és, hogy más tranzakciók módosíthatja, hogy elolvasta az aktuális tranzakció által az aktuális tranzakció befejezéséig adatok. További részletekért lásd: [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snapshot**: Megadja, hogy egy tranzakció utasítás által beolvasott adatok az adatokat, amely létezett a tranzakció kezdetekor tranzakciós szempontból konzisztens verzióját.
  A tranzakció felismerje csak a tranzakció megkezdése előtt is voltak adatok módosítását.
  Adatok módosítások fényében, amelyek más tranzakciók által az aktuális tranzakció megkezdése után az aktuális tranzakció végrehajtása kimutatások nem láthatók el.
  Milyen hatása, mintha az utasítások tranzakcióban, létezett a tranzakció kezdetekor pillanatkép a véglegesített adatokat szerezhet.
  A pillanatképek a Reliable Collections egységesek.
  További részletekért lásd: [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

A Reliable Collections automatikusan válassza ki a megadott olvasási művelet függően a művelet és a replika szerepkör tranzakció létrehozása idején használandó elkülönítési szint.
Alábbiakban található a táblában, és az elkülönítési szint alapértelmezett megbízható szótárban és üzenetsor-műveletek.

| A művelet \ szerepkör | Elsődleges | Másodlagos |
| --- |:--- |:--- |
| Egyetlen entitás olvasása |Megismételhető olvasása |Pillanatkép |
| Enumerálás, száma |Pillanatkép |Pillanatkép |

> [!NOTE]
> Egyetlen entitás műveletek gyakori alkalmazási helyzetek `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

A megbízható szótárt és a megbízható várólista is támogatja a olvassa el a ír.
Más szóval bármely írási tranzakción belül meg fognak jelenni a következő olvasási, amely ugyanabban a tranzakcióban tartozik.

## <a name="locks"></a>Zárolások
A Reliable Collections szigorú tranzakció megvalósítása két fázis zárolás: tranzakció nem szabadítja fel a zárolást szerzett meg amíg a tranzakció véget nem ér egy megszakítás vagy egy véglegesítés az.

Megbízható szótárban a művelet egy entitás zárolása sor szintet használja.
Megbízható várólista rendeltetésű szigorú tranzakciós FIFO tulajdonság egyidejűségi ki.
Megbízható várólista használja a művelet lehetővé teszi az egy-egy tranzakció szintű zárolások `TryPeekAsync` és/vagy `TryDequeueAsync` és a egy-egy tranzakció `EnqueueAsync` egyszerre.
Vegye figyelembe, amely segítségével megőrizheti a FIFO, ha egy `TryPeekAsync` vagy `TryDequeueAsync` minden eddiginél figyelembe veszi, hogy a megbízható várólista üres, akkor is zárolja `EnqueueAsync`.

Az írási műveletek mindig elsőbbséget kizárólagos zárolást.
Az olvasási műveletek esetében a a zárolás szolgáltatás több tényezőtől függ.
Minden olvasási művelet kész pillanatkép-elkülönítés használatával szabad zárolás.
Minden olyan reprodukálható olvasási művelet alapértelmezés szerint a közös zárolás vesz igénybe.
Azonban bármely olvasási művelet, amely megismételhető olvasási támogatja, a felhasználó kérhet-módosítási zárolást a közös zárolás helyett.
Frissítési zárolás egy aszimmetrikus zárolási megakadályozható, hogy egy közös formája, amely akkor fordul elő, amikor több tranzakció zárolhat erőforrásokat egy későbbi időpontban lehetséges frissítések holtpont.

A zárolás kompatibilitási mátrix az alábbi táblázatban található:

| Kérelem \ kapnak | None | Közös | Frissítés | Kizárólagos |
| --- |:--- |:--- |:--- |:--- |
| Közös |Ütközés |Ütközés |Ütközés |Ütközés |
| Frissítés |Ütközés |Ütközés |Ütközés |Ütközés |
| Kizárólagos |Ütközés |Ütközés |Ütközés |Ütközés |

A megbízható gyűjtemények API-k időtúllépési argumentum holtpont észlelési szolgál.
Ha például két tranzakciók (a T1 és T2) próbál olvassa és frissítse a K1.
Lehetőség, hogy kölcsönös kizárás, mivel mindkettő rendelkezik a közös zárolás.
Ebben az esetben az egyik vagy mindkettő a műveletek az időtúllépést okoz.

Ebben a forgatókönyvben holtpont egy remek példa hogyan frissítési zárolás képes megakadályozni a holtpontok.

## <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [A Reliable Services-értesítések](service-fabric-reliable-services-notifications.md)
* [Reliable Services biztonsági mentési és visszaállítási (katasztrófa utáni helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [A Reliable State Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

