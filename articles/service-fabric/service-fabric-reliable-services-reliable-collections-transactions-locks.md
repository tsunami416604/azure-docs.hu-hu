---
title: Tranzakciók és zárolási módok megbízható gyűjtemények esetén
description: Az Azure Service Fabric megbízható állapotkezelő és megbízható beszedési tranzakciók és zárolás.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938911"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Tranzakciók és zárolási módok az Azure Service Fabric megbízható gyűjtemények szolgáltatásában

## <a name="transaction"></a>Tranzakció

A tranzakció egyetlen logikai munkaegységként végrehajtott műveletek sorozata. Ez mutatja a közös [SAV](https://en.wikipedia.org/wiki/ACID) (*atom,* *konzisztencia*, *elszigeteltség*, *tartósság)* tulajdonságai adatbázis-tranzakciók:

* **Atomi :** A tranzakciónak amunka atomegységének kell lennie. Más szóval vagy az összes adatmódosítást végrehajtja a rendszer, vagy egyiket sem hajtja végre.
* **Konzisztencia**: Ha befejeződött, a tranzakciónak az összes adatot konzisztens állapotban kell hagynia. A tranzakció végén minden belső adatstruktúrának helyesnek kell lennie.
* **Elkülönítés**: Az egyidejű tranzakciók által végrehajtott módosításokat el kell különíteni a többi egyidejű gazdasági ügylet módosításaitól. Az [ITransaction-en](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) belüli művelethez használt elkülönítési szintet a műveletet végző [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) határozza meg.
* **Tartósság**: A tranzakció befejezése után a rendszer hatásai tartósan érvényben maradnak. A módosítások rendszerhiba esetén is fennállnak.

### <a name="isolation-levels"></a>Elkülönítési szintek

Az elkülönítési szint azt határozza meg, hogy az ügyletet milyen mértékben kell elkülöníteni a más tranzakciók által végrehajtott módosításoktól.
A Megbízható gyűjtemények két elkülönítési szintet támogatnak:

* **Ismételhető olvasás:** Itt adhatja meg, hogy az utasítások nem tudják olvasni azokat az adatokat, amelyeket más tranzakciók módosítottak, de más tranzakciók még nem véglegesítették, és hogy más tranzakciók nem módosíthatják az aktuális tranzakció által olvasott adatokat az aktuális tranzakció befejezéséig.
* **Pillanatkép**: Megadja, hogy a tranzakció bármely utasítása által olvasott adatok a tranzakció kezdetén létező adatok tranzakciósan konzisztens változata.
  A tranzakció csak a tranzakció kezdete előtt véglegesített adatmódosításokat ismeri fel.
  Az aktuális tranzakció kezdete után más tranzakciók által végrehajtott adatmódosítások nem láthatók az aktuális tranzakcióban végrehajtó utasítások számára.
  A hatás olyan, mintha a tranzakcióban lévő utasítások pillanatképet kapnának a véglegesített adatokról, ahogy az a tranzakció kezdetén létezett.
  A pillanatképek konzisztensek a megbízható gyűjtemények között.

A Megbízható gyűjtemények automatikusan kiválasztják az adott olvasási művelethez használandó elkülönítési szintet a művelettől és a replika szerepkörétől függően a tranzakció létrehozásakor.
A következő táblázat a Megbízható szótár és várólista-műveletek elkülönítési szintalapértelmezett beállításait ábrázolja.

| Művelet \ Szerepkör | Elsődleges | Másodlagos |
| --- |:--- |:--- |
| Egyetlen entitás olvasása |Ismételhető olvasás |Pillanatkép |
| Felsorolás, Darabszám |Pillanatkép |Pillanatkép |

> [!NOTE]
> Az egyentitásos műveletek `IReliableDictionary.TryGetValueAsync`gyakori `IReliableQueue.TryPeekAsync`példái a .
> 

Mind a megbízható szótár, mind a megbízható várólista támogatja *az írások olvasását.*
Más szóval a tranzakción belüli bármely írás látható lesz egy következő olvasási lehetőség számára, amely ugyanahhoz a tranzakcióhoz tartozik.

## <a name="locks"></a>Zárolások

A Megbízható gyűjtemények alkalmazásban minden tranzakció szigorú kétfázisú zárolást valósít meg: a tranzakció nem oldja fel a megszerzett zárolásokat, amíg a tranzakció megszakítással vagy véglegesítéssel le nem áll.

A Reliable Dictionary sorszintű zárolást használ az összes egyentitásos művelethez.
Megbízható várólista kereskedik ki egyidejűség szigorú tranzakciós FIFO tulajdonság.
A Megbízható várólista műveletszintű zárolásokat használ, amelyek egyszerre egy tranzakciót `TryPeekAsync` és/vagy `TryDequeueAsync` egy tranzakcióval tesznek `EnqueueAsync` lehetővé.
Vegye figyelembe, hogy a `TryPeekAsync` FIFO megőrzése érdekében, ha egy `TryDequeueAsync` vagy `EnqueueAsync`valaha is megjegyzi, hogy a megbízható várólista üres, akkor is zárolja .

Az írási műveletek mindig exkluzív zárakat vesznek igénybe.
Az olvasási műveletekesetében a zárolás több tényezőtől függ:

- A Pillanatkép elkülönítésével végzett olvasási műveletek zárolásmentesek.
- Minden ismételhető olvasási művelet alapértelmezés szerint megosztott zárolásokat vesz igénybe.
- Az ismételhető olvashatót támogató olvasási műveletek esetében azonban a felhasználó a megosztott zárolás helyett frissítési zárolást kérhet.
Az update lock egy aszimmetrikus zárolás, amely megakadályozza a holtpont egy gyakori formáját, amely akkor fordul elő, ha több tranzakció zárolja az erőforrásokat a lehetséges frissítésekhez egy későbbi időpontban.

A zárolási kompatibilitási mátrix az alábbi táblázatban található:

| Kérelem \ Megadva | None | Megosztott | Frissítés | Exkluzív |
| --- |:--- |:--- |:--- |:--- |
| Megosztott |Nincs ütközés |Nincs ütközés |Ütközés |Ütközés |
| Frissítés |Nincs ütközés |Nincs ütközés |Ütközés |Ütközés |
| Exkluzív |Nincs ütközés |Ütközés |Ütközés |Ütközés |

A Reliable Collections API-k időkapcsolati argumentuma holtpontészleléshez használatos.
Például két tranzakció (T1 és T2) próbálja olvasni és frissíteni a K1-et.
Lehetséges, hogy holtpontra jutnak, mert mindketten a megosztott zárat tartják.
Ebben az esetben az egyik vagy mindkét művelet idővel megmarad. Én ebben a forgatókönyvben, egy frissítészár megakadályozhatja egy ilyen patthelyzet.

## <a name="next-steps"></a>További lépések

* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Megbízható szolgáltatásokról szóló értesítések](service-fabric-reliable-services-notifications.md)
* [Megbízható szolgáltatások biztonsági mentése és visszaállítása (vészhelyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapotkezelő-konfiguráció](service-fabric-reliable-services-configuration.md)
* [Fejlesztői referencia a megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
