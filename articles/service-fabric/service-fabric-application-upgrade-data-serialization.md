---
title: "Az alkalmazásfrissítés: adatszerializálás |} Microsoft Docs"
description: "Ajánlott eljárások az adatok szerializálása és közbeni alkalmazás hatása."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 6aa3ac7842df4657fca7f6b4264e1c6fe52dc0c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hogyan befolyásolja a adatszerializálás az alkalmazás frissítés
Az egy [működés közbeni frissítés alkalmazás](service-fabric-application-upgrade.md), a frissítés a csomópontok egy alkészlete, egyszerre több frissítési tartományt vonatkozik. A folyamat során néhány frissítési tartományok az alkalmazás újabb verziója, és néhány frissítési tartományok az alkalmazás régebbi verzióját. A bevezetés alatt az alkalmazás új verzióját kell fogja tudni elolvasni az adatok a korábbi verziója, és az alkalmazás régebbi verziójához tudja olvasni az adatokat az új verziót kell lennie. Ha az adatformátum nem előre és hátra kompatibilis, a frissítés sikertelen lehet, vagy rosszabb, adatok esetleg elveszett vagy sérült. Ez a cikk ismerteti, mi a adatformátum számít, és annak biztosítása, hogy az adatok előre és hátra ajánlott eljárásai kínál kompatibilis.

## <a name="what-makes-up-your-data-format"></a>Mi az adatformátum alkotó?
Az Azure Service Fabric a megőrzött és a replikált adatokat a C# osztályok származik. Használó alkalmazások esetén [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md), hogy adatokat-e a megbízható szótárakat és a várólisták objektumok. Használó alkalmazások esetén [Reliable Actors](service-fabric-reliable-actors-introduction.md), vagyis szereplő biztonsági állapotát. Ezeket az osztályokat C# tárolása és replikálása szerializálhatónak kell lennie. Ezért az adatok formátumát a mezők és tulajdonságok, amelyek szerializálva vannak, valamint hogy szerializálva vannak. Például egy `IReliableDictionary<int, MyClass>` az adatokat egy szerializált `int` és egy szerializált `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kód változik, hogy akár egy adatok formátumának módosítása
Az adatformátum C# osztályok határozza meg, mert az osztályok módosításai okozhat egy adatok formátumának módosítása. Ügyelni kell arra, hogy a működés közbeni frissítés kezelni tud-e az adatok formátumának módosítása. Példák, amelyek okozhatnak adatok formázása módosításokat:

* Hozzáadásával vagy eltávolításával a mezők és tulajdonságok
* Mezők és tulajdonságok átnevezése
* A típusú mezők és tulajdonságok módosítása
* Az osztályhoz nevet vagy névteret módosítása

### <a name="data-contract-as-the-default-serializer"></a>Adategyezmény, mint az alapértelmezett szerializáló
A szerializáló felelős általában az adatok olvasása és deszerializálása során az aktuális verziójában, még akkor is, ha az adatok korábbi vagy *újabb* verziója. Az alapértelmezett szerializáló van a [adategyezmény-szerializáló](https://msdn.microsoft.com/library/ms733127.aspx), amely jól meghatározott versioning szabályokkal rendelkeznek. Gyűjtemények engedélyezése a szerializáló felülbírálását, de a Reliable Actors jelenleg nem megbízható. Az adatok szerializáló a működés közbeni frissítés fontos szerepet játszik. Az adategyezmény-szerializáló a Service Fabric-alkalmazások általunk javasolt szerializáló.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hogyan befolyásolja az adatformátum a működés közbeni frissítés
A működés közbeni frissítés során nincsenek kétféle módon történhet, ahol a szerializáló régebbi észlelhetnek vagy *újabb* az adatok verziója:

1. Miután egy csomópont frissítve van, és biztonsági mentése elindul, az új szerializáló tölti be lett megőrzött adatok lemezre régi verziója.
2. A működés közbeni frissítés során a fürt a régi és új verziókat a kód vegyesen fogja tartalmazni. Replikák különböző frissítési tartományok kerülnek, és replikák adatokat küldeni a egymástól, az adatok új és/vagy a régi verzióját a szerializáló az új és/vagy a régi verziója fordulhatnak elő.

> [!NOTE]
> Az "új verzió" és "régi verzió" Itt tekintse meg a futó kód verziója. Az "új szerializáló" hivatkozik a szerializáló kódot, amely végrehajtja az az alkalmazás új verziója. Az "új adatok" hivatkozik a szerializált C# az alkalmazás új verziója.
> 
> 

Kódja és adatai formátum két verziója kell lennie, továbbítás és a visszamenőlegesen kompatibilis. Ha azok nem kompatibilisek, a működés közbeni frissítés sikertelen lehet, vagy adatvesztés történhet. A működés közbeni frissítés meghiúsulhat, mert a kódban, illetve a szerializáló generálhat kivételek vagy hiba esetén a másik verziója. Adatok elveszhetnek, ha például egy új tulajdonság hozzá lett adva, de a régi szerializáló elveti deszerializálása során.

Adategyezmény-e annak biztosítása, hogy az adatok kompatibilis az ajánlott megoldás. Rendelkezik, jól meghatározott versioning szabályok hozzáadása, eltávolítása és mezők módosítása Azt is támogatja az ismeretlen mezők foglalkoznak, a szerializálás és a deszerializálás folyamatba csatlakoztatás és osztályöröklődést foglalkoznak. További információkért lásd: [adategyezmény használatával](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [Speciális témakörök](service-fabric-application-upgrade-advanced.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási ](service-fabric-application-upgrade-troubleshooting.md).

