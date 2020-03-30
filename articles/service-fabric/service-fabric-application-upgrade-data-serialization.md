---
title: 'Alkalmazásfrissítés: adatszerializálás'
description: Gyakorlati tanácsok az adatok szerializálásához és a működés közbeni alkalmazásfrissítések befolyásolásának módjához.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457497"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Az adatok szerializálása az alkalmazásfrissítésre
A [működés közbeni alkalmazásfrissítés](service-fabric-application-upgrade.md)során a frissítés a csomópontok egy részhalmazára vonatkozik, egyszerre egy frissítési tartományra. A folyamat során egyes frissítési tartományok az alkalmazás újabb verzióját, a frissítési tartományok pedig az alkalmazás régebbi verzióját keresik. A bevezetés során az alkalmazás új verziójának képesnek kell lennie az adatok régi verziójának olvasására, és az alkalmazás régi verziójának képesnek kell lennie az adatok új verziójának olvasására. Ha az adatformátum nem előre- és visszafelé kompatibilis, a frissítés sikertelen lehet, vagy ami még rosszabb, az adatok elveszhetnek vagy megsérülhetnek. Ez a cikk ismerteti, hogy mi minősül az adatformátumnak, és gyakorlati tanácsokat ad annak biztosítására, hogy az adatok előre és hátra kompatibilisek legyenek.

## <a name="what-makes-up-your-data-format"></a>Mi alkotja az adatformátumot?
Az Azure Service Fabric az adatok, amelyek megmaradnak, és replikált származik a C# osztályok. A Megbízható [gyűjtemények](service-fabric-reliable-services-reliable-collections.md)alkalmazást használó alkalmazások esetében az adatok a megbízható szótárak ban és várólistákban lévő objektumok. A Reliable [Actors](service-fabric-reliable-actors-introduction.md)alkalmazást használó alkalmazások esetében ez az aktor háttérállapota. Ezeknek a C# osztályoknak szerializálhatónak kell lenniük ahhoz, hogy megmaradjanak és replikálódjanak. Ezért az adatformátumot a szerializált mezők és tulajdonságok, valamint a szerializált mezők és tulajdonságok határozzák meg. Az adatokban `IReliableDictionary<int, MyClass>` például egy szerializált `int` `MyClass`és egy szerializált .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Az adatformátum változását eredményező kódváltozások
Mivel az adatformátumot C# osztályok határozzák meg, az osztályok módosítása adatformátum-változást okozhat. Ügyelni kell arra, hogy a működés közbeni frissítés kezelni tudja az adatformátum-változást. Példák, amelyek az adatformátum megváltozását okozhatják:

* Mezők vagy tulajdonságok hozzáadása és eltávolítása
* Mezők vagy tulajdonságok átnevezése
* Mezők vagy tulajdonságok típusának módosítása
* Az osztály nevének vagy névterének módosítása

### <a name="data-contract-as-the-default-serializer"></a>Adategyezmény alapértelmezett szerializálóként
A szerializáló általában felelős az adatok olvasásáért és deszerializálásáért az aktuális verzióba, még akkor is, ha az adatok régebbi vagy *újabb* verzióban vannak. Az alapértelmezett szerializáló az [adatszerződés szerializáló](https://msdn.microsoft.com/library/ms733127.aspx), amely jól definiált verziószámozási szabályokkal rendelkezik. Megbízható gyűjtemények lehetővé teszik a szerializáló felülbírálható, de megbízható szereplők jelenleg nem. Az adatszerializáló fontos szerepet játszik a működés közbeni frissítések engedélyezésében. Az adategyezmény szerializáló a szerializáló, amely et javasoljuk a Service Fabric-alkalmazások.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Az adatformátum hatása a működés közbeni frissítésre?
A működés közbeni frissítés során két fő forgatókönyv van, ahol a szerializáló az adatok egy régebbi vagy *újabb* verziójával találkozhat:

1. A csomópont frissítése és a biztonsági másolatot elindít, az új szerializáló betölti a régi verzió által lemezre megőrzött adatokat.
2. A működés közbeni frissítés során a fürt a kód régi és új verzióinak keverékét fogja tartalmazni. Mivel a replikák különböző frissítési tartományokba helyezhetők, és a replikák adatokat küldenek egymásnak, az adatok új és/vagy régi verzióját a szerializáló új és/vagy régi verziója találkozhat.

> [!NOTE]
> Az "új verzió" és a "régi verzió" itt a kód futó verziójára vonatkozik. Az "új szerializáló" az alkalmazás új verziójában végrehajtó szerializáló kódra utal. Az "új adatok" az alkalmazás új verziójából származó szerializált C# osztályra utalnak.
> 
> 

A kód és az adatformátum két verziójának előre- és hátrafelé is kompatibilisnek kell lennie. Ha nem kompatibilisek, a működés közbeni frissítés sikertelen lehet, vagy adatok veszhetnek el. A működés közbeni frissítés sikertelen lehet, mert a kód vagy szerializáló előfordulhat, hogy kivételeket vagy hibát, amikor találkozik a másik verzió. Adatok elveszhetnek, ha például egy új tulajdonságot adtak hozzá, de a régi szerializáló elveti azt a deszerializálás során.

Az adategyezmény az ajánlott megoldás annak biztosítására, hogy az adatok kompatibilisek legyenek. Jól definiált verziószámozási szabályokkal rendelkezik mezők hozzáadásához, eltávolításához és módosításához. Azt is támogatja foglalkozó ismeretlen területeken, hooking a szerializálási és deszerializálási folyamat, és foglalkozik osztály öröklés. További információ: [Using Data Contract](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>További lépések
[Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti az alkalmazás frissítésén a Visual Studió használatával.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával történő alkalmazásfrissítésen.

A frissítési paraméterek használatával szabályozhatja, hogy az alkalmazás hogyan [frissítsen.](service-fabric-application-upgrade-parameters.md)

A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.

Az alkalmazásfrissítések gyakori problémáinak megoldása az [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva.

