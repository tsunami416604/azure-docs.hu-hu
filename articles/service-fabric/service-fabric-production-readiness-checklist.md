---
title: Az Azure Service Fabric éles készenléti ellenőrzőlistája
description: Készítse elő a Service Fabric-alkalmazást és a fürt éles környezetét az ajánlott eljárások követésével.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376800"
---
# <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája

Az alkalmazás és a fürt készen áll az éles forgalom ra? Az alkalmazás és a fürt futtatása és tesztelése nem feltétlenül jelenti azt, hogy készen áll az éles környezetbe való beharcra. Az alábbi ellenőrzőlista segítségével zökkenőmentesen futtathatja az alkalmazást és a fürtöt. Javasoljuk, hogy ezeket a tételeket ki pipálják. Természetesen választhat, hogy alternatív megoldásokat használ egy adott sorhoz (például saját diagnosztikai keretrendszerekhez).


## <a name="prerequisites-for-production"></a>A termelés előfeltételei
1. Az Azure Service Fabric gyakorlati tanácsok: [Alkalmazástervezés,](./service-fabric-best-practices-applications.md) [Biztonság,](./service-fabric-best-practices-security.md) [Hálózatkezelés,](./service-fabric-best-practices-networking.md) [Kapacitástervezés és -méretezés](./service-fabric-best-practices-capacity-scaling.md), [Infrastruktúra kódként](./service-fabric-best-practices-infrastructure-as-code.md)és [figyelés és diagnosztika](./service-fabric-best-practices-monitoring.md). 
1. Valósítsa meg a Reliable Actors biztonsági konfigurációt, ha az Actors programozási modellt használja
1. A 20 magnál vagy 10 csomópontnál nagyobb számú fürthöz hozzon létre egy dedikált elsődleges csomóponttípust a rendszerszolgáltatásokhoz. [Elhelyezési megkötések](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) hozzáadásával lefoglalhatja az elsődleges csomóponttípusát a rendszerszolgáltatásokszámára.
1. Használjon D2v2 vagy újabb termékváltozatot az elsődleges csomóponttípusához. Javasoljuk, hogy válasszon egy termékváltozat legalább 50 GB-os merevlemez kapacitás.
1. A termelési fürtöknek [biztonságosnak](service-fabric-cluster-security.md)kell lenniük. A biztonságos fürt beállításának például a [fürtsablonban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)olvashat. Használjon közös neveket a tanúsítványokhoz, és ne használjon önaláírt tanúsítványokat.
1. [Erőforrás-korlátozások hozzáadása a tárolókon és a szolgáltatásokon,](service-fabric-resource-governance.md)hogy ne használják fel a csomópont-erőforrások 75%-ánál többet. 
1. Megérteni és beállítani a [tartóssági szintet](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Az állapotalapú számítási feladatokat futtató csomóponttípusok esetében ezüst vagy magasabb tartóssági szint ajánlott. Az elsődleges csomóponttípus nak ezüst vagy magasabb tartóssági szinttel kell rendelkeznie.
1. A csomóponttípus [megbízhatósági szintjének](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) ismertetése és kivételezése. Ezüst vagy nagyobb megbízhatóság ajánlott.
1. Terhelésés méretezési teszt a számítási feladatok a fürt [kapacitáskövetelményeinek](service-fabric-cluster-capacity.md) azonosításához. 
1. A szolgáltatások és alkalmazások figyelése és alkalmazásnaplók létrehozása és tárolása, riasztással. Például lásd: [Naplózás hozzáadása a Service Fabric-alkalmazáshoz](service-fabric-how-to-diagnostics-log.md) és a Figyelő [tárolók Az Azure Monitor naplók.](service-fabric-diagnostics-oms-containers.md)
1. A fürt figyelése riasztással (például [az Azure Monitor naplók).](service-fabric-diagnostics-event-analysis-oms.md) 
1. Az alapul szolgáló virtuálisgép-méretezési csoport infrastruktúráját riasztással figyeli a rendszer (például az [Azure Monitor naplóival.](service-fabric-diagnostics-oms-agent.md)
1. A fürt mindig [elsődleges és másodlagos tanúsítványokkal](service-fabric-cluster-security-update-certs-azure.md) rendelkezik (így nem lesz zárolva).
1. Külön fürtök karbantartása a fejlesztéshez, az előkészítéshez és az éles környezethez. 
1. [Az alkalmazás-](service-fabric-application-upgrade.md) és [fürtfrissítések tesztelése](service-fabric-tutorial-upgrade-cluster.md) először a fejlesztési és átmeneti fürtökben van. 
1. Kapcsolja ki az automatikus frissítéseket az éles fürtökben, és kapcsolja be a fejlesztési és átmeneti fürtökhöz (szükség szerint visszaállítás). 
1. Hozzon létre egy helyreállítási pont célkitűzést (RPO) a szolgáltatáshoz, és állítson be egy [vész-helyreállítási folyamatot,](service-fabric-disaster-recovery.md) és tesztelje azt.
1. Tervezze meg a fürt manuális vagy programozott [méretezését.](service-fabric-cluster-scaling.md)
1. Tervezze meg a fürtcsomópontok [javítását.](service-fabric-patch-orchestration-application.md) 
1. Hozzon létre egy CI/CD-folyamatot, hogy a legújabb módosításokat folyamatosan teszteljék. Például az [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) vagy [a Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) használatával
1. Tesztelje a fejlesztési & átmeneti fürtök terhelés alatt a [Fault Analysis Service](service-fabric-testability-overview.md) és indukáló ellenőrzött [káosz.](service-fabric-controlled-chaos.md) 
1. Tervezze meg az alkalmazások [méretezését.](service-fabric-concepts-scalability.md) 


Ha a Service Fabric megbízható szolgáltatások vagy a Reliable Actors programozási modell, a következő elemeket kell ellenőrizni ki:
1. Frissítse az alkalmazásokat a helyi fejlesztés során, hogy ellenőrizze, hogy a szolgáltatáskód tiszteletben tartja-e a metódus ban a törlési jogkivonatot, és bezárja az `RunAsync` egyéni kommunikációs figyelők.
1. Kerülje a [gyakori buktatókat](service-fabric-work-with-reliable-collections.md) a Megbízható gyűjtemények használatakor.
1. A .NET CLR memóriateljesítmény-számlálók figyelése a terhelési tesztek futtatásakor, és ellenőrizze a szemétgyűjtés vagy az elszabadult halommemória növekedésének magas arányát.
1. A Reliable [Services és](service-fabric-reliable-services-backup-restore.md) a Reliable Actors offline biztonsági mentésének karbantartása és a helyreállítási folyamat tesztelése.
1. Az elsődleges nodetype virtuálisgép-példányok száma ideális esetben egyenlő nek kell lennie a fürtmegbízhatósági szint minimális; feltételeket, ha szükséges, hogy meghaladja a réteg minimum tartalmazza: ideiglenesen, ha függőlegesen skálázás az elsődleges nodetypes virtuálisgép-méretezéskészlet sku.

## <a name="optional-best-practices"></a>Választható gyakorlati tanácsok

Míg a fenti listák előfeltételei a termelésnek, a következő tételeket is figyelembe kell venni:
1. Csatlakoztassa a [Service Fabric állapotmodell](service-fabric-health-introduction.md) a beépített állapotkiértékelés és jelentéskészítés kiterjesztéséhez.
1. Telepítsen egy egyéni figyelőt, amely figyeli az alkalmazást, és [erőforrás-elosztási](service-fabric-cluster-resource-manager-balancing.md) [terhelést](service-fabric-cluster-resource-manager-metrics.md) jelent. 


## <a name="next-steps"></a>További lépések
* [Service Fabric Windows-fürt telepítése](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux-fürt telepítése](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
