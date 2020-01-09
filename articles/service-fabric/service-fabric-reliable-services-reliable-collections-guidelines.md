---
title: Irányelvek a megbízható gyűjteményekhez
description: Irányelvek és javaslatok Service Fabric megbízható gyűjtemények Azure Service Fabric alkalmazásban való használatához.
ms.topic: conceptual
ms.date: 12/10/2017
ms.openlocfilehash: 37c734205877f9e0cb98ef2834462691e8e483d9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645480"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Irányelvek és javaslatok az Azure-Service Fabric megbízható gyűjteményekhez
Ez a szakasz a megbízható állapot-kezelő és a megbízható gyűjtemények használatához nyújt útmutatást. A cél az, hogy segítse a felhasználókat a gyakori buktatók elkerülésében.

Az irányelvek a feltételek *elvégzése*, a *megfontolás*, a *elkerülés* és a *nem*megfelelő egyszerű javaslatok szerint vannak rendszerezve.

* Ne módosítsa az olvasási műveletek által visszaadott egyéni típusú objektumokat (például `TryPeekAsync` vagy `TryGetValueAsync`). A megbízható gyűjtemények, akárcsak a párhuzamos gyűjtemények, az objektumokra mutató hivatkozást adnak vissza.
* A módosítás előtt készítsen részletesen egy egyéni típusú visszaadott objektumot. Mivel a struktúrák és a beépített típusok értéke a pass-by-Value, nem kell részletes másolatot készítenie rajtuk, hacsak nem tartalmaznak hivatkozással megadott mezőket vagy tulajdonságokat, amelyeket módosítani kíván.
* Ne használja a `TimeSpan.MaxValue` az időtúllépésekhez. A holtpontok észleléséhez időtúllépést kell használni.
* Ne használjon tranzakciót a véglegesítés, a megszakított vagy a használat után.
* Ne használjon enumerálást a-ben létrehozott tranzakciós hatókörön kívül.
* Ne hozzon létre tranzakciót egy másik tranzakció `using` utasításán belül, mert a holtpontot eredményezhet.
* Ne hozzon létre megbízható állapotot `IReliableStateManager.GetOrAddAsync` és használja a megbízható állapotot ugyanabban a tranzakcióban. Ez egy InvalidOperationException eredményez.
* Győződjön meg arról, hogy a `IComparable<TKey>` implementációja helyes. A rendszer az ellenőrzőpontok és a sorok egyesítéséhez `IComparable<TKey>` függőséget vesz igénybe.
* A frissítési zárolást akkor használja, ha olyan elemeket olvas be, amelyekkel frissíteni kívánja azt, hogy megakadályozza a holtpontok bizonyos osztályait.
* Vegye figyelembe, hogy a megbízható gyűjtemények száma nem haladhatja meg a 1000-ot. A megbízható gyűjtemények több elemmel is előnyben részesülnek, kevesebb elemmel.
* Ügyeljen arra, hogy az elemek (például a TKey + TValue megbízható szótárhoz) az 80 kilobájt alatt legyenek: kisebb a jobb. Ez csökkenti a nagyméretű objektum-halom használatának mennyiségét, valamint a lemez és a hálózat i/o-követelményeit. Gyakran csökkenti a duplikált adat replikálását, ha az érték csak egy kis részét frissíti. Ennek a megbízható szótárban való elérésének gyakori módja, ha a sorokat több sorba szeretné bontani.
* Érdemes lehet biztonsági mentési és visszaállítási funkciókat használni a vész-helyreállításhoz.
* A különböző elkülönítési szintek miatt ne keverje az egyentitásos műveleteket és a több entitásos műveleteket (például `GetCountAsync`, `CreateEnumerableAsync`) ugyanabban a tranzakcióban.
* Végezze el a InvalidOperationException kezelését. A rendszer többféle okból is megszakíthatja a felhasználói tranzakciókat. Ha például a megbízható állapot-kezelő módosítja a szerepkört az elsődlegesen, vagy ha egy hosszan futó tranzakció blokkolja a tranzakciós napló csonkítása. Ilyen esetekben előfordulhat, hogy a felhasználó olyan InvalidOperationException kap, amely azt jelzi, hogy a tranzakciójuk már meg van szakítva. Feltételezve, hogy a felhasználó nem kérte a tranzakció megszüntetését, a kivétel kezelésére a legjobb módszer, ha a tranzakciót el szeretné érni, ellenőrizze, hogy a lemondási token meg lett-e jelezve (vagy a replika szerepköre megváltozott-e), és ha nem hoz létre új tranzakció, majd próbálkozzon újra.  

A következő szempontokat érdemes figyelembe venni:

* Az alapértelmezett időtúllépés négy másodperc az összes megbízható gyűjtemény API-hoz. A legtöbb felhasználónak az alapértelmezett időtúllépést kell használnia.
* Az alapértelmezett lemondási token `CancellationToken.None` minden megbízható gyűjtemény API-ban.
* A megbízható szótár kulcs Type paraméterének (*TKey*) megfelelően kell megvalósítani `GetHashCode()` és `Equals()`. A kulcsok nem változtathatók meg.
* Ha magas rendelkezésre állást szeretne biztosítani a megbízható gyűjtemények számára, minden szolgáltatásnak rendelkeznie kell legalább egy célként megadott és minimális replikakészlet 3-as mérettel.
* A másodlagos olvasási műveletek a kvórumnak nem véglegesített verziókat is tartalmazhatnak.
  Ez azt jelenti, hogy az egyetlen másodlagosból beolvasott adatok egyik verziója hamis lehet.
  Az Elsődlegesből való beolvasás mindig stabil: soha nem lehet hamisan haladni.
* A megbízható gyűjteményben az alkalmazás által megőrzött adatok biztonsága/adatvédelme a saját döntése, valamint a tárolási felügyelet által biztosított védelem tárgya. I.E. Az operációs rendszer lemezének titkosítása felhasználható az inaktív adatok védelme érdekében.  

### <a name="next-steps"></a>Következő lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Az adatkezelés
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* Egyebek
  * [Reliable Services – első lépések](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referenciák megbízható gyűjteményekhez](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
