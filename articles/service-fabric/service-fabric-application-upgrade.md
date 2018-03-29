---
title: A Service Fabric az alkalmazásfrissítés |} Microsoft Docs
description: Ez a cikk bemutatja azokat a Service Fabric-alkalmazás, beleértve a választhatja frissítési módok és teljesítő állapot-ellenőrzést frissítése.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 60bbd75496b6e835a76edb4251aac6ea249187b3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
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
Egyes alapértelmezett szolgáltatás definiált paraméterek a [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) alkalmazás frissítés részeként is frissíthető. Csak a paramétereket, amelyek támogatják a keresztül az módosítás alatt [frissítés-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) módosítható a frissítés részeként. Az alapértelmezett szolgáltatások módosítása az alkalmazásfrissítés során a rendszer nem az alábbiak szerint:

1. Alapértelmezett szolgáltatásokat az új alkalmazásjegyzékben, amely nem szerepel a fürt jönnek létre.
2. Mindkét a korábbi és új alkalmazás jegyzékfájlokban szereplő alapértelmezett szolgáltatások frissülnek. A paraméterek az alapértelmezett szolgáltatás az új alkalmazásjegyzékben felülírja a meglévő szolgáltatás paraméterei. Az alkalmazásfrissítés automatikusan visszaállítási Ha alapértelmezett szolgáltatás frissítése sikertelen.
3. Alapértelmezett szolgáltatások, amelyek nem szerepelnek az új alkalmazás jegyzékfájlja törlődnek, ha vannak ilyenek, a fürtben. **Vegye figyelembe, hogy egy alapértelmezett szolgáltatás törlése eredményezi, hogy a szolgáltatás törlésével állapotát és nem vonható vissza.**

Az alkalmazásfrissítés vissza lesz állítva, alapértelmezett paramétereket az előtt a frissítés elindult, de törölt szolgáltatások nem hozható létre ismét a régi állapot állnak vissza a régi értékekre.

> [!TIP]
> A [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) kell lennie a fürt konfigurációs beállítás *igaz* szabályok 2 engedélyezése) és 3) (alapértelmezett frissítési és törlési) felett. Ez a funkció a Service Fabric 5.5-ös verzió kezdve támogatott.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>A HTTPS-végpontnak több alkalmazás frissítése
Ügyeljen arra, hogy használni szeretné a **ugyanazt a portot** HTTP használata esetén egy alkalmazás különböző példányai**S**. A hiba oka, hogy a Service Fabric nem lehet frissíteni a tanúsítvány az alkalmazás példányai közül. Ha például 1 vagy alkalmazás 2 mindkét alkalmazás szeretné, hogy az 1. tanúsítvány frissítése a cert 2. Ha a frissítés történik, a Service Fabric előfordulhat, hogy rendelkezik kiüríti az 1. tanúsítvány regisztrálása a következőben: http.sys annak ellenére, hogy a többi alkalmazás továbbra is használja. Ennek megelőzése érdekében a Service Fabric észleli, hogy már egy másik alkalmazáspéldány regisztrálva a porton (mert a http.sys) a tanúsítványhoz, és a művelet sikertelen lesz.

Ezért a Service Fabric nem támogatja a frissítést két különböző szolgáltatásokat **ugyanazt a portot** másik alkalmazási esetekben. Ez azt jelenti ugyanazt a tanúsítványt nem használhat a különböző szolgáltatások ugyanazt a portot. Ha egy megosztott tanúsítvány rendelkezik ugyanazon a porton van szüksége, győződjön meg arról, hogy a szolgáltatások egy elhelyezési korlátozás rendelkező különböző gépeken kerülnek szeretné. Vagy fontolja meg a Service Fabric dinamikus portok lehetőség szerint minden egyes szolgáltatás minden egyes alkalmazás-példányban. 

Ha megjelenik egy frissítés sikertelen lesz, a https, hiba, figyelmeztetés, amely szerint "A Windows HTTP-kiszolgáló API nem támogatja több tanúsítvány egy portot használó alkalmazásokhoz."

## <a name="application-upgrade-flowchart"></a>Alkalmazás frissítési folyamatábra
Az alábbi folyamatábra azt segítenek megérteni a frissítési folyamat Service Fabric-alkalmazás. A folyamatot ismerteti, hogyan az időtúllépéseket, beleértve a *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, és *UpgradeHealthCheckInterval*, Súgó Ha a frissítés egy frissítési tartomány tekinthető sikeres vagy hibát vezérlő.

![A Service Fabric-alkalmazás frissítési folyamata][image]

## <a name="next-steps"></a>További lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [Adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [Speciális témakörök](service-fabric-application-upgrade-advanced.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
