---
title: Service Fabric-alkalmazás frissítése |} A Microsoft Docs
description: Ez a cikk mutatja be a Service Fabric-alkalmazások, beleértve a lehetőséget választva frissítési módok és teljesítményű állapot-ellenőrzések frissítése.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e2b407733bcab7bc854e8e3703e53eb474f3425b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663689"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric-alkalmazás frissítése
Az Azure Service Fabric-alkalmazás szolgáltatások gyűjteménye. A frissítés során a Service Fabric hasonlítja össze az új [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) korábbi verziójával, és meghatározza a szolgáltatások az alkalmazás igényelnek frissítést. A Service Fabric a számokat a szolgáltatásban a verziószámokat a korábbi verzióban az alkalmazásjegyzékeket verzióját hasonlítja össze. Ha a szolgáltatás nem változott, a szolgáltatás nincs frissítve.

## <a name="rolling-upgrades-overview"></a>A működés közbeni frissítés áttekintése
Egy alkalmazás frissítése működés közben, az a frissítés végrehajtása szakaszban. Minden egyes fázisában a frissítés alkalmazza a rendszer a fürt frissítési tartomány nevű csomópontok egy része. Ennek eredményeképpen az alkalmazás továbbra is elérhető a frissítés során. A frissítés során a fürt a régi és új verzióit vegyesen is tartalmazhat.

Éppen ezért a két verzió kell lennie, előre és visszafelé kompatibilis. Ha nem kompatibilis, az alkalmazás-rendszergazda feladata átmeneti a több fázisú frissítése rendelkezésre állását. A több fázisú frissítés az első lépés egy köztes verziójára az alkalmazás, amely kompatibilis a korábbi verzióra frissíti. A második lépéseként frissítése a végleges verzió működésképtelenné válik a frissítés előtti verziót való kompatibilitás, de a köztes verziójával kompatibilis.

Frissítési tartományok a fürtjegyzékben vannak megadva, a fürt konfigurálásakor. Frissítési tartományok nem kaphat frissítéseket egy adott sorrendben. Frissítési tartomány egy alkalmazás központi telepítésének egy logikai egységet. Frissítési tartományok a frissítés során továbbra is magas rendelkezésre állású szolgáltatások engedélyezése.

Nem – működés közbeni frissítés is előfordulhatnak, ha a frissítés van érvényben, azonban ez megváltozott, ha az alkalmazás csak egyetlen frissítési tartományt a fürt összes csomópontja számára. Ez a módszer nem ajánlott, mivel a szolgáltatás leáll, és nem érhető el a frissítés idején. Emellett az Azure nem biztosít semmilyen garanciát a fürt úgy van beállítva, az csak egyetlen frissítési tartományt.

A frissítés befejezése után a szolgáltatások és replicas(instances) szeretne maradni ugyanazon verzió – azaz a Ha a frissítés sikeres volt, az összes rendszer frissíti majd őket az új verzióra; Ha a frissítés sikertelen lesz, és van vonva, azok lenne állítható vissza a régi verzióját.

## <a name="health-checks-during-upgrades"></a>Állapot-ellenőrzések frissítések során
A frissítés állapotházirendeket kell állítani (vagy az alapértelmezett értékeket is használni). Frissítés az összes frissítési tartományok megadott időtúllépésre belül frissítésekor, és ha az összes frissítési tartományok kifogástalan beállításkulcsoknak nevezik sikeres.  A megfelelő frissítési tartomány azt jelenti, hogy a frissítési tartomány átadott állapotházirend megadott összes állapot-ellenőrzések. Ha például egy olyan házirendet előfordulhat, hogy megszabják, hogy minden szolgáltatás alkalmazáspéldány belül kell lennie *kifogástalan*, health Service Fabric által meghatározott.

Házirendek és a Service Fabric által a frissítés során ellenőrzi olyan szolgáltatások és alkalmazások független. Ez azt jelenti, hogy nincsenek szolgáltatásspecifikus tesztek kell elvégezni.  Például előfordulhat, hogy a szolgáltatás átviteli követelmény, de a Service Fabric nem rendelkezik az átviteli sebesség ellenőrzéséhez információkat. Tekintse meg a [egészségügyi cikkek](service-fabric-health-introduction.md) számára a ellenőrzi, hogy el kell végezni. A ellenőrzi, hogy számára, hogy az alkalmazáscsomag volt megfelelően másolja ki, egy frissítési Belefoglalás tesztek során fordulhat elő, hogy a példány lett elindítva, és így tovább.

Az alkalmazás üzemállapota az alkalmazás a gyermekentitások összesítését. Röviden a Service Fabric kiértékeli a health, legyen az alkalmazás a jelentés segítségével az alkalmazás állapotát. Azt is kiértékeli az alkalmazás a szolgáltatások állapotát ezzel a módszerrel. További Service Fabric kiértékeli az alkalmazás szolgáltatások állapotának összesítésével ellenőrizhessék a gyermekek, például a szolgáltatás replika állapotát. Után az alkalmazás állapotszabályzata teljesül, folytassa a frissítést. Állapotházirend sérül, ha az alkalmazás frissítése sikertelen.

## <a name="upgrade-modes"></a>Frissítési mód
Az alkalmazásfrissítés általunk javasolt módja a figyelt módban, amely a gyakran használt mód. Felügyelt mód a frissítést hajt végre egy frissítési tartományt, és ha az összes állapot-ellenőrzések (a házirend szerint megadva), pass áthelyezi a következő frissítési tartomány automatikusan.  Ha az állapot-ellenőrzések sikertelenek, és/vagy gondok elérésekor, a frissítés vagy vissza lesz állítva a frissítési tartomány, vagy a üzemmód nem figyelt manuálisra változik. Beállíthatja, hogy a frissítés sikertelen frissítéseket ezen két mód közül választhat. 

Nem figyelt manuális mód a frissítési tartomány, a frissítést a következő frissítési tartomány a elindítson minden frissítés után manuális beavatkozásra kell. Nem Service Fabric állapotellenőrzést végez. A rendszergazda a következő frissítési tartomány a frissítés megkezdése előtt ellenőrzi egészségügyi és állapotát.

## <a name="upgrade-default-services"></a>Alapértelmezett szolgáltatások frissítése
Egyes alapértelmezett szolgáltatás definiált paramétereket a [alkalmazásjegyzék](service-fabric-application-and-service-manifests.md) alkalmazásfrissítést részeként is frissíthetők. Csak a támogatási keresztül módosításának szolgáltatás paraméterei [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) frissítés részeként módosíthatók. Az alapértelmezett szolgáltatások módosítása az alkalmazásfrissítés során viselkedését a következőképpen történik:

1. Alapértelmezett szolgáltatásokat az új alkalmazásjegyzékben, amely még nem léteznek a fürt jön létre.
2. Alapértelmezett szolgáltatások, amelyek szerepelnek a is a korábbi és új alkalmazásjegyzéket frissülnek. A paraméterek alapértelmezett szolgáltatás az új alkalmazásjegyzékben írja felül a meglévő szolgáltatás a paraméterek. Az alkalmazás frissítése automatikusan fog visszaállítása, ha egy alapértelmezett szolgáltatás frissítése sikertelen.
3. Alapértelmezett szolgáltatások, amelyek nem szerepelnek az új alkalmazás jegyzékfájlja törlődnek, ha azok léteznek, a fürtben. **Vegye figyelembe, hogy egy alapértelmezett szolgáltatás törlése eredményezi, hogy a szolgáltatás törlése állapot és nem vonható vissza.**

Ha egy alkalmazás frissítése vissza lesz állítva, alapértelmezett szolgáltatás paraméterek vissza a régi értékük visszavonásra kerül, mielőtt a frissítés elindítva, de a törölt szolgáltatások nem hozható létre ismét a régi állapotuk.

> [!TIP]
> A [enabledefaultservicesupgrade beállítást](service-fabric-cluster-fabric-settings.md) kell lennie a fürt konfigurációs beállítás *igaz* szabályok 2 engedélyezése) és 3) (alapértelmezett szolgáltatás frissítése és törlése) felett. Ez a szolgáltatás indítása a Service Fabric 5.5-ös verzió támogatott.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>A HTTPS-végpontok több alkalmazás frissítése
Ügyeljen arra, hogy használni szeretné a **ugyanazt a portot** a HTTP használata esetén egyazon alkalmazás különböző példányaiban**S**. A hiba oka, hogy a Service Fabric nem tudja frissíteni a tanúsítvány egy, az alkalmazás példányai. Ha például 1 vagy alkalmazás 2 mindkét alkalmazás kívánt 1. a tanúsítvány frissítése a 2. tanúsítvány. Ha a frissítés történik, a Service Fabric előfordulhat, hogy rendelkezik törölni az 1. tanúsítvány regisztrációját, a http.sys annak ellenére, hogy a többi alkalmazás továbbra is használja. Ennek megelőzése érdekében a Service Fabric észleli, hogy már regisztrálva a porton (a http.sys) miatt a tanúsítvánnyal más alkalmazáspéldány, és a művelet sikertelen lesz.

Ezért a Service Fabric nem támogatja a frissítést két különböző szolgáltatások használatával **ugyanazt a portot** a különböző alkalmazáspéldányok. Más szóval ugyanaz a tanúsítvány nem használható a különböző szolgáltatások ugyanazt a portot. Szüksége lesz egy megosztott tanúsítványfájlokat ugyanazt a portot, ha szüksége győződjön meg arról, hogy a szolgáltatások az elhelyezési korlátozások különböző gépeken vannak elhelyezve. Vagy fontolja meg a Service Fabric dinamikus portok lehetőség szerint az egyes szolgáltatások az egyes alkalmazáspéldányokról. 

Ha egy frissítés sikertelen, a https, figyelmeztetés, amely arról tájékoztat, "A Windows API HTTP-kiszolgáló nem támogatja több tanúsítvány egy portot használó alkalmazások számára." hiba jelenik meg

## <a name="application-upgrade-flowchart"></a>Alkalmazás frissítési folyamatábra
Az alábbi folyamatábra segítségével megismerheti a Service Fabric-alkalmazás a frissítési folyamat. Különösen a folyamatot ismerteti, hogyan az időtúllépések, beleértve a *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, és *UpgradeHealthCheckInterval*, Súgó Szabályozza, amikor a frissítés egyetlen frissítési tartomány számít sikeres vagy sikertelen.

![A frissítési folyamat a Service Fabric-alkalmazás][image]

## <a name="next-steps"></a>További lépések
[Az alkalmazás használatával a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.

[Az alkalmazás használatával PowerShell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.

Vezérelheti, hogyan az alkalmazásfrissítések használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Hogyan használja az alkalmazásfrissítések kompatibilissé [adatok szerializálása](service-fabric-application-upgrade-data-serialization.md).

Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [haladó témakörök](service-fabric-application-upgrade-advanced.md).

Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
