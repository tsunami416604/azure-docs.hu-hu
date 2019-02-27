---
title: 'Alkalmazásfrissítés: adatok szerializálása |} A Microsoft Docs'
description: Ajánlott eljárások az adatok szerializálása és működés közbeni alkalmazásfrissítések hatása.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 43d19e5c69733689be184f06b853fa4e488dd51e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871723"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hogyan befolyásolja az adatok szerializálása a egy alkalmazás frissítése
Az egy [alkalmazás frissítése működés közbeni](service-fabric-application-upgrade.md), a frissítés alkalmazott csomópontok egy része, egyszerre több frissítési tartományt. A folyamat során néhány frissítési tartományok az alkalmazás újabb verziójában, és az alkalmazás régebbi verzióját a rendszer egyes frissítési tartományok. A bevezetés során az alkalmazás új verziója, olvassa el a régi verziót, az adatok képesnek kell lennie, és az alkalmazás régebbi verzióját kell tudni olvasni az adatokat az új verziót. Ha a adatformátum nem előre és visszafelé kompatibilis, a frissítés sikertelen lehet, vagy rosszabb, adatok esetleg elveszett vagy sérült állapotba kerül. Ez a cikk ismerteti, mi a adatformátum alkotja, és annak biztosítása, hogy az adatok előre és hátra ajánlott eljárásai kínál kompatibilis.

## <a name="what-makes-up-your-data-format"></a>Miből áll a adatformátum?
Az Azure Service Fabric a megőrzött és a replikált adatok származik a C# osztályokat. Az alkalmazásokat, amelyek használják [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md), hogy adatokat-e az objektumok a megbízható szótárakban és üzenetsorok. Használó alkalmazások esetében [Reliable Actors](service-fabric-reliable-actors-introduction.md), vagyis a biztonsági állapotot az aktor. Ezek C# osztályok, tárolása és replikálása szerializálható kell lennie. Ezért az adatok formátumát határozza meg a mezőket és a tulajdonságok, amelyek szerializálva vannak, valamint, hogy hogyan szerializálva vannak. Például egy `IReliableDictionary<int, MyClass>` az adatokat a szerializált `int` és a egy szerializált `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kód módosítja a eredményez olyan adatok formátumának módosítása
Mivel az adatok formátumát határozza meg C# osztályok, az osztályok módosítása hatására előfordulhat, hogy a formátum változnak. Ügyelni kell arra, hogy a működés közbeni frissítésének képes kezelni az adatok formátumának módosítása. Példa, amelyek okozhat a módosítások formázása:

* Hozzáadásával vagy eltávolításával mezők vagy tulajdonságai
* Mezők vagy tulajdonságok átnevezése
* A típusú mezők vagy tulajdonságainak módosítása
* Az osztály neve vagy a névtér módosítása

### <a name="data-contract-as-the-default-serializer"></a>Az alapértelmezett szerializáló szerződéssel adatok
A szerializáló felelős általában az adatok olvasása és deszerializálhatja azt az aktuális verzióját, akkor is, ha az adatok a régebbi vagy *újabb* verzió. Az alapértelmezett szerializáló van a [adategyezményben szerializáló](https://msdn.microsoft.com/library/ms733127.aspx), amely jól definiált verziókezelési szabályokkal rendelkezik. A Reliable Collections engedélyezése a szerializáló Alkalmazásszint felülbírálását, de a Reliable Actors jelenleg nem. A data-szerializáló fontos szerepet engedélyezése a működés közbeni frissítése során. Az adatok szerződés szerializáló a szerializáló, javasoljuk, hogy a Service Fabric-alkalmazásokat.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hogyan érinti az adatformátum a működés közbeni frissítés
A működés közbeni frissítés során két fő forgatókönyv, ahol a szerializáló You may encounter régebbi vannak vagy *újabb* az adatok verziója:

1. Miután egy csomópont frissül, és biztonsági mentése elindul, az új szerializáló betölti az adatokat, amelyek fájladatot lemezre által a régi verzióját.
2. A fürt működés közbeni frissítése során a kód korábbi és új verzióit vegyesen tartalmazza. Replikák különböző frissítési tartományt lehet helyezni, és a replikák adatokat küldeni a egymást, mivel az adatok új és/vagy régi verziója merülhetnek fel a szerializáló új és/vagy régi verziója.

> [!NOTE]
> Az "új" és "régi verziója" Itt tekintse meg a futó kód verziója. A szerializáló kódot, amely az alkalmazás új verziójának a végrehajtása az "új szerializáló" hivatkozik. Az "új adatok" hivatkozik a szerializált C# osztály az alkalmazás új verzióját.
> 
> 

A kód és az adatok formátuma két verzióját lehet előre és visszafelé is kompatibilis. Ha nem kompatibilis, a működés közbeni frissítés sikertelen lehet, vagy adatvesztés történhet. A működés közbeni frissítés sikertelen lehet, mert a kód vagy a szerializáló generálhat kivétel, vagy pedig egy tartalék során tapasztal verziót. Ha például egy új tulajdonság hozzá lett adva, de a régi szerializáló elveti a deszerializálás során adatok elveszhetnek.

Adatok szerződés biztosítva, hogy az adatok kompatibilis a javasolt megoldás. Jól definiált versioning szabályok hozzáadása, eltávolítása és a mezők módosítása rendelkezik. Azt is támogatja az ismeretlen mezőket foglalkoznak, a szerializálást és deszerializálást folyamat történetének és osztályöröklődés foglalkoznak. További információkért lásd: [adategyezményben használatával](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>További lépések
[Az alkalmazás használatával a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.

Vezérelheti, hogyan az alkalmazásfrissítések használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [haladó témakörök](service-fabric-application-upgrade-advanced.md).

Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).

