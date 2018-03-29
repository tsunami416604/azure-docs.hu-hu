---
title: Irányelvek és javaslatok az Azure-ban megbízható gyűjtemények szolgáltatás háló |} Microsoft Docs
description: Irányelvek és javaslatok a Service Fabric megbízható gyűjtemények
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: mcoskun
ms.openlocfilehash: b0eb6a5aab5e71c0a8ac8263e177c493a07dafc0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Irányelvek és javaslatok az Azure Service Fabric megbízható gyűjtemények
Ez a szakasz útmutatást nyújt a megbízható állapotkezelője és megbízható gyűjtemények használatával. A cél, hogy a felhasználók közös nehézségek elkerülése érdekében.

Az irányelvek egyszerű ajánlásokat a feltételeket a következő előtaggal vannak sorolva *tegye*, *fontolja meg*, *kerülje* és *nem*.

* Ne módosítsa az olvasási műveletek által visszaadott egyéni típusú objektum (például `TryPeekAsync` vagy `TryGetValueAsync`). Megbízható gyűjtemények, egyidejű gyűjtemények, mint például egy hivatkozást az objektumok és másolatát nem adhat vissza.
* Lemásolni mély visszaadott típusú objektumhoz tartozó olyan egyéni módosítás előtt. Mivel a struktúrák és beépített pass-by-value, nem kell csak referencia típusú mezők vagy a módosítani kívánt tulajdonságokat tartalmaznak, akkor a részletes másolatot rajtuk.
* Ne használjon `TimeSpan.MaxValue` időtúllépési. Időtúllépések holtpont észleléséhez használandó.
* Ne használjon tranzakció, miután ez befejeződött, megszakított, vagy elvetése megtörtént.
* Ne használja az enumerálás a létrehozták a tranzakció hatókörén kívül.
* Ne hozzon létre egy tranzakcióban, másik tranzakcióban `using` utasítás mert holtpont lehet okozni.
* Győződjön meg arról, hogy a `IComparable<TKey>` implementációjának helyességét. A rendszer mennyi függőségi `IComparable<TKey>` az ellenőrzőpontok és sorok egyesítéshez.
* Módosítási zárolást szándéka elemet olvasásakor frissítésére használni megakadályozhatja, hogy egy bizonyos osztály holtpont.
* Vegye figyelembe, ha megtartja az 1000-nél kisebb lehet partíciónként megbízható gyűjtemények száma. Több megbízható gyűjteményeket, amelyek kevesebb elem felett előnyben részesítik a megbízható gyűjtemények további elemeket.
* Vegye figyelembe a megőrzi az elemeket (például TKey + TValue megbízható szótár) 80 KB alatt: kisebb annál pontosabb. Ez csökkenti a nagy objektumok halommemóriájának kihasználtsága, valamint a lemezek és a hálózati IO-követelményeket. Gyakran csökkentik az ismétlődő adatokat replikál, az érték csak egy kis részét frissítésekor. Közös ennek érdekében a megbízható szótárban módja a sorok megszüntetése több sort.
* Érdemes biztonsági mentést, és állítsa vissza a vész-helyreállítási funkció.
* Elkerülése érdekében egyetlen entitás műveletek és többentitásos műveletek (például `GetCountAsync`, `CreateEnumerableAsync`) miatt a különböző elkülönítési szinten ugyanabban a tranzakcióban.
* InvalidOperationException kezelni. A rendszer számos okból felhasználói tranzakció is megszakítja. Például ha a megbízható állapotkezelője-jal kívül elsődleges szerepet a, vagy ha egy hosszú ideig futó tranzakció blokkolja a tranzakciós napló csonkolási. Ilyen esetben felhasználói InvalidOperationException, amely azt jelzi, hogy a tranzakció már meg lett szakítva, jelenhet meg. Ha, a tranzakció futása nem volt szükség a felhasználó által kezelni ezt a kivételt legjobb módja, ha a tranzakció eldobásakor ellenőrizze, hogy a megszakítási token felé a jelzésküldés (vagy a replika szerepkörét az megváltozott), és ha nem hozzon létre egy új tranzakció, és próbálkozzon újra.  

Az alábbiakban néhány dolgot figyelembe venni:

* Az alapértelmezett érték négy másodperc minden megbízható gyűjtemény API. A legtöbb felhasználónak alapértelmezett időkorlátja kell használnia.
* Az alapértelmezett megszakítási lexikális elem `CancellationToken.None` a gyűjtemények API-k az összes megbízható.
* A kulcs típusú paraméter (*TKey*) a megbízható Dictionary megfelelően meg kell valósítania `GetHashCode()` és `Equals()`. Lehet, hogy a kulcsok nem módosítható.
* Magas rendelkezésre állás biztosítása a megbízható gyűjtemények, minden egyes szolgáltatás legalább egy célként és a replikakészlet minimális mérete 3 beállítása kell rendelkeznie.
* A másodlagos olvasási műveletek nem kvórum véglegesített verziók lehet olvasni.
  Ez azt jelenti, hogy egy verziója, amely egyetlen másodlagos olvasható adatok előfordulhat, hogy lehet hamis jutott el.
  Elsődleges olvasások a rendszer mindig stabil: is soha nem lehet false jutott el.

### <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és a zárolásokat](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Adatok kezelése
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Szerializálási és frissítése](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
