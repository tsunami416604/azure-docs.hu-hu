---
title: – Irányelvek és javaslatok az Azure-ban a Reliable Collections a Service Fabric |} A Microsoft Docs
description: Service Fabric megbízható gyűjtemények használatával vonatkozó irányelvek és javaslatok
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: twhitney
ms.openlocfilehash: d50fee06a291e11898de19fd49bd657d2e1a6d00
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184936"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Irányelvek és javaslatok az Azure Service Fabric megbízható gyűjteményeihez
Ez a szakasz útmutatást nyújt a a Reliable State Manager és a Reliable Collections használata. A célja segítség a felhasználóknak a közös alkalmazásmegoldásokra elkerülése érdekében.

Az irányelvek előtaggal van ellátva a használati egyszerű ajánlásként vannak rendszerezve *tegye*, *fontolja meg*, *kerülje* és *nem*.

* Ne módosítsa az olvasási műveletek által visszaadott egyéni típusú objektumot (például `TryPeekAsync` vagy `TryGetValueAsync`). A Reliable Collections egyidejű gyűjteményeket, mint egy hivatkozást az objektumok és a egy másolja vissza.
* Részletes másolási tegye meg a visszaadott objektum egy egyéni típusú módosítás előtt. Mivel az struktúrák és beépített pass-a-érték, nem kell tennie őket részletes másolatot, ha a referencia-kitöltött mező vagy a módosítani kívánt tulajdonságokat tartalmaznak.
* Ne használjon `TimeSpan.MaxValue` időtúllépési. Időtúllépések használandó holtpontok észleléséhez.
* Ne használjon egy tranzakciót, miután ez befejeződött, megszakított, vagy eldobva.
* Ne használjon egy enumerálás kívül lett létrehozva a tranzakció-hatókörben.
* Ne hozzon létre egy tranzakció egy másik tranzakción belül `using` utasítás mert holtpontok okozhat.
* Győződjön meg arról, hogy a `IComparable<TKey>` megvalósítási helyességéről. A rendszer felveszi a függőségi `IComparable<TKey>` a sorok és az ellenőrzőpontok egyesítését.
* Frissítési zárolás funkció használata egy elemet a szándéka olvasásakor frissítse úgy, hogy egy adott osztály holtpontok megakadályozása.
* Érdemes lehet megtartja az 1000-nél kisebb lehet partíciónként megbízható gyűjtemények száma. A további elemek a Reliable Collections előnyben részesítése kevesebb elemet tartalmazó további megbízható gyűjtemények.
* Vegye figyelembe a tartja az elemek (például TKey + megbízható szótár TValue) alatt 80 kilobájt: kisebb, annál jobb. Ez csökkenti a nagy objektumok halommemóriájának kihasználtsága, valamint a lemezek és a hálózati IO-követelményeket. Gyakran előfordul csökkenti a duplikált adatok replikálása, ha az érték csak egy kis részét frissítése folyamatban van. Ennek érdekében a megbízható szótárban gyakori módja, ha több sort a sorok feltörhessék.
* Fontolja meg a biztonsági mentéssel, és hogy a vész-helyreállítási funkciójának helyreállításához.
* Elkerülése érdekében egyetlen entitás műveletek és többentitásos műveletek (például: `GetCountAsync`, `CreateEnumerableAsync`) miatt a különböző elkülönítési szinten ugyanabban a tranzakcióban.
* InvalidOperationException kezelni. A rendszer számos okból a felhasználói tranzakciókat is megszakítja. Például ha a Reliable State Manager módosul a szerepkör elsődleges kívül, vagy ha egy hosszú ideig futó tranzakció nem blokkolja a tranzakciós napló csonkolása. Ezekben az esetekben előfordulhat, hogy a felhasználó InvalidOperationException, amely azt jelzi, hogy a tranzakció már meg lett szakítva, kap. Feltételezve, a tranzakció megszüntetése nem volt szükség a felhasználó által kezelni ezt a kivételt a legjobb módja, ha tud megszabadulni a tranzakciót, ellenőrizze, hogy a megszakítás token mígnem lett (vagy a a replika szerepkörét az megváltozott), és hozzon létre egy új Ha tranzakció, majd próbálkozzon újra.  

Az alábbiakban néhány dolgot figyelembe kell venni:

* Az alapértelmezett időtúllépési érték minden megbízható gyűjtemény API négy másodperc. A felhasználók többsége az alapértelmezett időtúllépési kell használnia.
* Az alapértelmezett megszakítás jogkivonat `CancellationToken.None` az összes megbízható gyűjtemények API-t.
* A kulcstípus paramétert (*TKey*) számára egy megbízható szótárban tároló megfelelően meg kell valósítania `GetHashCode()` és `Equals()`. Lehet, hogy a kulcsok nem módosítható.
* A megbízható gyűjtemények magas rendelkezésre állás eléréséhez, minden szolgáltatás legalább egy cél és a replikakészlet minimális mérete 3 kell rendelkezniük.
* A másodlagos olvasási művelet lehet, hogy olvassa el, amelyek nem kvórum véglegesített verziók.
  Ez azt jelenti, hogy egy egyetlen másodlagos beolvasott verziója előfordulhat, hogy lehet hamis haladt előre.
  Elsődleges olvasási mindig stabil: is soha nem lehet hamis haladt előre.
* A megőrzött biztonsági és adatvédelmi az adatok az alkalmazás megbízható gyűjteményekben a döntést és a storage management által biztosított védelmet a tulajdonos AZAZ Operációs rendszer lemeztitkosítás használható az adatok inaktív védelme érdekében.  

### <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Adatok kezelése
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Szerializálási és frissítése](service-fabric-application-upgrade-data-serialization.md)
  * [A Reliable State Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [A Reliable Services – gyorsútmutató](service-fabric-reliable-services-quick-start.md)
  * [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
