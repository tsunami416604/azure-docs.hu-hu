---
title: "A Service Fabric az alkalmazásfrissítés |} Microsoft Docs"
description: "Ez a cikk bemutatja azokat a Service Fabric-alkalmazás, beleértve a választhatja frissítési módok és teljesítő állapot-ellenőrzést frissítése."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 5fed3b5b127a2b398b99ab2b46c762920e9dc249
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric-alkalmazás frissítése
Az Azure Service Fabric-alkalmazás szolgáltatások gyűjteménye. A frissítés során a Service Fabric összehasonlítja az új [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) korábbi verziójával, és meghatározza, hogy az alkalmazás igényelnek frissítést szolgáltatások. A Service Fabric a verziója, a szolgáltatás szereplő számok akkor jelentkezik, az előző verziójában a verziószámok hasonlítja össze. Ha a szolgáltatás nem változott, hogy a szolgáltatás nincs frissítve.

## <a name="rolling-upgrades-overview"></a>Működés közbeni frissítés áttekintése
Az alkalmazás működés közbeni frissítés megtörténik a frissítés fázisból áll. Minden szakaszhoz a frissítés a a fürt, egy frissítési tartományt a csomópontok egy alkészlete alkalmazzák. Emiatt az alkalmazás továbbra is elérhető a frissítés során. A frissítés során a fürt a régi és új verzióit vegyesen is tartalmazhat.

Emiatt a két verzióját kell előre és hátra kompatibilis. Ha azok nem kompatibilisek, az alkalmazás-rendszergazda feladata átmeneti egy több-fázis frissítés rendelkezésre álljon. A több fázisú frissítés az első lépés egy köztes az alkalmazás, amely kompatibilis a régebbi verziójára frissíti. A második lépése, hogy frissítse a végleges verzióra, amely megsérti a frissítés előtti verziójával való kompatibilitás szempontjából, de a köztes verziójával kompatibilis.

Amikor konfigurálja a fürt frissítési tartományok megadott a fürtjegyzékben. Frissítési tartományok nem kaphat frissítéseket meghatározott sorrendben. Egy frissítési tartomány nem az alkalmazás központi telepítésének logikai tárolóegységet jelöl. Frissítési tartományok magas rendelkezésre állás, a frissítés során továbbra is a szolgáltatások engedélyezése.

Nem működés közbeni frissítés is előfordulhatnak, ha a frissítés vonatkozik, a helyzet, amikor az alkalmazás csak egy frissítési tartomány van, amely a fürt összes csomópontján. Ez a módszer nem ajánlott, mivel a szolgáltatás leáll, és nem érhető el a frissítés során. Emellett Azure nem biztosít semmilyen garanciák egy fürt csak egy frissítési tartomány be van állítva.

A frissítés befejezése után a szolgáltatások és replicas(instances) volna maradnak ugyanazon verzióját – azaz, ha a frissítés sikeres, az összes azok frissíti az új verzió; Ha a frissítés sikertelen, és visszaállításra, azok akkor állítható vissza a korábbi verziója.

## <a name="health-checks-during-upgrades"></a>Állapot-ellenőrzési eredményeire frissítéskor
A frissítéshez állapotházirendeket kell állítani (vagy lehet, hogy az alapértelmezett értékeket használni). Frissítés sikeres nevezik, frissítési tartományok belül az adott időtúllépéseket frissítésekor, ha minden frissítési tartományok kifogástalan minősülnek.  A megfelelő frissítési tartomány azt jelenti, hogy a frissítési tartomány átadott állapotházirend megadott állapotellenőrzést. Például egy olyan házirendet is engedélyezhetik, hogy kell-e egy alkalmazás példányán belül minden szolgáltatás *kifogástalan*, a health Service Fabric által meghatározott.

Házirendek és a Service Fabric által a frissítés során ellenőrzi olyan szolgáltatások és alkalmazások független. Ez azt jelenti, hogy nem szolgáltatásspecifikus tesztek történik.  Például előfordulhat, hogy a szolgáltatás egy átviteli követelményt, de a Service Fabric információkkal rendelkezik, ellenőrizze az átviteli sebesség. Tekintse meg a [egészségügyi cikkek](service-fabric-health-introduction.md) vonatkozó ellenőrzéseket hajtja végre. A ellenőrzi, hogy az e alkalmazáscsomag megfelelően, másolja egy frissítési include tesztek során fordulhat elő, hogy a példány indult el, és így tovább.

Az alkalmazás állapotának az alkalmazás gyermek entitások összesítést. A Service Fabric rövid, az alkalmazás állapotát, amely az alkalmazás jelentenek a állapotának értékeli ki. Azt is kiértékeli az alkalmazás összes szolgáltatás állapotának ily módon. A Service Fabric további alkalmazásszolgáltatások állapotának értékelje a gyermekek, például a szolgáltatás replika állapotának összesítése. Után az alkalmazás állapotházirendje teljesül, a frissítés végrehajtható. Ha állapotházirend sérül, az alkalmazás frissítése sikertelen.

## <a name="upgrade-modes"></a>Frissítési módok
Az alkalmazásfrissítés általunk javasolt módja a figyelt módban, amely a gyakran használt mód. Figyelt módban végzi, a frissítés egy frissítési tartományt, és ha ellenőrzi az összes száma (a megadott házirend), pass helyezi át a következő frissítési tartomány automatikusan.  Ha az állapot ellenőrzése sikertelen, és/vagy időtúllépéseket elérésekor, a frissítés vagy vissza lesz állítva a frissítési tartomány, vagy a mód a nem figyelt manuálisra változott. Beállíthatja, hogy a frissítés sikertelen frissítés e két mód kiválasztása. 

Nem figyelt manuális mód kézi beavatkozás kell minden egyes frissítési tartományokon, hogy a frissítés a következő frissítési tartományra indítsa a frissítés után. Nem a Service Fabric állapotellenőrzést végez. A rendszergazda a rendszerállapot vagy állapotának ellenőrzése a következő frissítési tartomány a frissítés megkezdése előtt.

## <a name="upgrade-default-services"></a>Alapértelmezett szolgáltatások frissítésére
Service Fabric-alkalmazás alapértelmezett szolgáltatás a frissítés során egy alkalmazás frissítése. Alapértelmezett szolgáltatások vannak definiálva a [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md). Az alapértelmezett szolgáltatások frissítése szabványos szabályok a következők:

1. Az új szolgáltatások alapértelmezett [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) , amelyek nem szerepelnek a fürt jönnek létre.
> [!TIP]
> [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) engedélyezéséhez a következő szabályok true értékre kell beállítani. Ez a funkció a v5.5 támogatott.

2. Mindkét előző meglévő szolgáltatási alapértelmezett [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) és új verziójára frissülnek. Az új verzióban szolgáltatások ismertetése felülírná azokat már a fürtben. Az alkalmazásfrissítés alapértelmezett szolgáltatás hibája frissítése után automatikusan visszaállítási lenne.
3. Alapértelmezett szolgáltatásait előző [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) , de az új verzió nem törli. **Vegye figyelembe, hogy a törlési alapértelmezett szolgáltatások nem beállításai állíthatók vissza.**

Abban az esetben frissítés vissza lesz állítva, az alapértelmezett szolgáltatások állnak vissza állapotát frissítésének megkezdése előtt. De törölt szolgáltatások soha nem hozható létre.

## <a name="application-upgrade-flowchart"></a>Alkalmazás frissítési folyamatábra
Az alábbi folyamatábra azt segítenek megérteni a frissítési folyamat Service Fabric-alkalmazás. A folyamatot ismerteti, hogyan az időtúllépéseket, beleértve a *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, és *UpgradeHealthCheckInterval*, Súgó Ha a frissítés egy frissítési tartomány tekinthető sikeres vagy hibát vezérlő.

![A Service Fabric-alkalmazás frissítési folyamata][image]

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [Adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [Speciális témakörök](service-fabric-application-upgrade-advanced.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
