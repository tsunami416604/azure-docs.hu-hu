---
title: A megbízható gyűjtemények irányelvei
description: A Service Fabric megbízható gyűjtemények azure Service Fabric-alkalmazásokban való használatának irányelvei és javaslatai.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: db37067069b2a9eb08009eb6bb373f6fce1cafa9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398525"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Az Azure Service Fabric megbízható gyűjteményeivel kapcsolatos irányelvek és javaslatok
Ez a szakasz a Megbízható állapotkezelő és a megbízható gyűjtemények használatával kapcsolatos irányelveket tartalmaz. A cél az, hogy segítsen a felhasználóknak elkerülni a gyakori buktatókat.

Az irányelvek egyszerű ajánlásokként vannak rendszerezve, amelyek a *Do*, Consider , *Avoid* és *A Ne*kifejezésekkel vannak *ellátva.*

* Ne módosítsa az olvasási műveletek (például vagy `TryPeekAsync` `TryGetValueAsync`) által visszaadott egyéni típusú objektumot. Megbízható gyűjtemények, csakúgy, mint az egyidejű gyűjtemények, visszaad egy hivatkozást az objektumokra, és nem egy példányt.
* Módosítás előtt másolja le az egyéni típusú visszaadott objektumot. Mivel a structs és a beépített típusok pass-by-value, nem kell csinálni egy mély másolatot rájuk, kivéve, ha azok referencia-típusú mezőket vagy tulajdonságokat, amelyeket módosítani kíván.
* Ne használja `TimeSpan.MaxValue` időtúlokra. Időmegtigók at kell használni a holtpontok észlelésére.
* Ne használjon tranzakciót a véglegesítés, a megszakított vagy az ártalmatlanítás után.
* Ne használjon felsorolást azon a tranzakcióhatókörön kívül, amelyben létrejött.
* Ne hozzon létre tranzakciót `using` egy másik tranzakció utasításán belül, mert az holtpontokat okozhat.
* Ne hozzon létre `IReliableStateManager.GetOrAddAsync` megbízható állapotot, és használja a megbízható állapotot ugyanabban a tranzakcióban. Ez egy InvalidOperationException-t eredményez.
* Győződjön meg `IComparable<TKey>` arról, hogy a megvalósítás helyes. A rendszer az `IComparable<TKey>` ellenőrzőpontok és sorok egyesítése függ.
* Ne használja Update lock olvasásakor egy elemet azzal a szándékkal, hogy frissítse azt, hogy megakadályozzák egy bizonyos osztály a holtpontok.
* Fontolja meg a partíciónkénti megbízható gyűjtemények száma 1000-nél kisebb tartása. A megbízható gyűjteményeket részesíti előnyben több elemekkel, mint a kevesebb elemet.
* Fontolja meg, hogy az elemek (például TKey + TValue for Reliable Dictionary) 80 kbyte alatt legyenek: kisebb, annál jobb. Ez csökkenti a nagy objektumhalom-használat, valamint a lemez- és hálózati I/O-követelmények mennyiségét. Gyakran csökkenti az ismétlődő adatok replikálását, ha az értéknek csak egy kis része frissül. Ezt a Reliable Dictionary-ben gyakori módja, ha a sorokat több sorra bontja.
* Fontolja meg a biztonsági mentési és visszaállítási funkció használatát a vészhelyreállításhoz.
* Kerülje az egyegyengazdálkodó műveletek és a többegységes műveletek (pl. `GetCountAsync`) `CreateEnumerableAsync`keverését ugyanabban a tranzakcióban a különböző elkülönítési szintek miatt.
* Ne lekell tenni az InvalidOperationException leírót. A felhasználói tranzakciókat a rendszer több okból is megszakíthatja. Például ha a megbízható állapotkezelő módosítja a szerepét az elsődleges, vagy ha egy hosszú ideig futó tranzakció blokkolja a tranzakciós napló csonkítása. Ilyen esetekben a felhasználó kaphat InvalidOperationException jelzi, hogy a tranzakció már le van állítva. Feltételezve, hogy a tranzakció megszüntetését nem kérte a felhasználó, a legjobb módja annak, hogy kezelni ezt a kivételt, hogy dobja ki a tranzakciót, ellenőrizze, hogy a törlési jogkivonat ot jelezték (vagy a replika szerepköre megváltozott), és ha nem hoz létre egy új tranzakciót, és próbálja meg újra.  

Íme néhány dolog, amit szem előtt kell tartania:

* Az alapértelmezett idő-megtérés négy másodperc az összes reliable collection API-k. A legtöbb felhasználónak az alapértelmezett idő-meghosszabbítást kell használnia.
* Az alapértelmezett megszakítási jogkivonat az összes Reliable Collections API-ban található. `CancellationToken.None`
* A megbízható szótár kulcstípus-paraméterének (*TKey)* `Equals()`helyesen kell implementációba. `GetHashCode()` A kulcsoknak nem módosíthatóknak kell lenniük.
* A megbízható gyűjtemények magas rendelkezésre állásának elérése érdekében minden szolgáltatásnak rendelkeznie kell legalább egy cél és minimális replikakészlet mérete 3.
* Olvasási műveletek a másodlagos lehet olvasni verziók, amelyek nem kvórum véglegesített.
  Ez azt jelenti, hogy az egyetlen másodlagos adatok egy verziója hamis állapotú lehet.
  Az Elemi ből származó olvasások mindig stabilak: soha nem lehetnek hamisak.
* Az alkalmazás által megbízható gyűjtésben tárolt adatok biztonsága/védelme az Ön döntése, és a tárhelykezelés által biztosított védelem vonatkozik rá; Azaz. Az operációs rendszer lemeztitkosítása használható az inaktív adatok védelmére.  

## <a name="volatile-reliable-collections"></a>Illékony megbízható gyűjtemények
Amikor úgy dönt, hogy volatilis megbízható gyűjtemények, fontolja meg a következőket:

* ```ReliableDictionary```nem rendelkezik illékony támogatást
* ```ReliableQueue```nem rendelkezik illékony támogatást
* ```ReliableConcurrentQueue```nem rendelkezik illékony támogatással
* A megőrzött szolgáltatások nem tehetők volatilissé. A ```HasPersistedState``` jelző ```false``` módosításához a teljes szolgáltatás újraalkotása szükséges a semmiből
* Az illékony szolgáltatások nem tarthatók meg. A ```HasPersistedState``` jelző ```true``` módosításához a teljes szolgáltatás újraalkotása szükséges a semmiből
* ```HasPersistedState```szolgáltatási szint konfigurációja. Ez azt jelenti, hogy **az ÖSSZES** gyűjtemény vagy megmarad, vagy illékony. Illékony és megőrzött gyűjtemények nem keverhetők
* Az illékony partíció kvórumának elvesztése teljes adatvesztést eredményez
* A biztonsági mentés és visszaállítás nem érhető el az illékony szolgáltatásokhoz

## <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Adatok kezelése
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapotkezelő-konfiguráció](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [Megbízható szolgáltatások rövid útmutató](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referencia a megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
