---
title: Az Azure Service Fabric éles készültségi ellenőrzőlista |} A Microsoft Docs
description: Felkészülés a Service Fabric-alkalmazás és a fürt éles környezetben a következő bevált gyakorlatát.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: subramar
ms.openlocfilehash: 6ebe83794f23ed3cf637f668d2810b9ba7521084
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295870"
---
# <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája

Az alkalmazás és a fürt készen áll az éles forgalmat is? Futtathatja és tesztelheti az alkalmazások és a fürt nem feltétlenül jelenti azt, készen áll, éles környezetben. Tartsa meg az alkalmazás és a fürt zökkenőmentes végig a következő címen. Erősen ajánlott ezeket kell ellenőrizni összes elemet. Természetesen lehet váltani, használja az alternatív megoldások egy adott vonal elem (például a saját diagnosztikai keretrendszer).


## <a name="pre-requisites-for-production"></a>Éles előfeltételei

1. 20-nál több maggal vagy a 10 csomópont-fürtöket hozzon létre egy dedikált elsődleges csomóponttípus-szolgáltatások. Adjon hozzá [elhelyezési korlátozások](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) lefoglalni az elsődleges csomóponttípushoz,-szolgáltatások. 
2. Egy D2v2 vagy magasabb szintű Termékváltozatot használja az elsődleges csomóponttípushoz. Javasoljuk, hogy válasszon ki egy Termékváltozat legalább 50 GB merevlemez-kapacitással rendelkező.
2. Éles fürtök kell [biztonságos](service-fabric-cluster-security.md). Például egy biztonságos fürt beállításának, ez látható [fürtöket](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Tanúsítványok köznapi nevek használata, és elkerülheti a saját önaláírt tanúsítványokkal.
4. Adjon hozzá [a tárolók és szolgáltatások erőforrás-korlátozások](service-fabric-resource-governance.md), így több mint 75 %-a-csomópont erőforrások nem használnak. 
5. Megismerheti és beállíthatja a [tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Állapotalapú alkalmazások és szolgáltatások futtatása csomóponttípusok Silver és magasabb szintű tartóssági szint használata ajánlott. Az elsődleges csomóponttípushoz kell rendelkeznie a tartóssági szint beállítása a Silver vagy újabb.
6. Megismerheti, és válassza ki a [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) a csomópont típusa. Silver és magasabb szintű megbízhatóság használata javasolt.
7. Terhelés és a méretezési csoport teszteléséhez a számítási feladatok azonosításához [kapacitásigények](service-fabric-cluster-capacity.md) a fürt számára. 
8. A szolgáltatások és alkalmazások állnak figyelés és az alkalmazásnaplókat a rendszer éppen létrehozott és tárolt, a riasztás. Lásd a [naplózás hozzáadása a Service Fabric-alkalmazás](service-fabric-how-to-diagnostics-log.md) és [a Log Analytics-tárolók monitorozása](service-fabric-diagnostics-oms-containers.md).
9. A fürt a riasztási rendszer figyeli (például [OMS](service-fabric-diagnostics-event-analysis-oms.md)). 
10. Az alapul szolgáló virtual machine scale set infrastruktúra-riasztások figyel (például [Log Analytics](service-fabric-diagnostics-oms-agent.md).
11. A fürt rendelkezik [elsődleges és másodlagos tanúsítványát](service-fabric-cluster-security-update-certs-azure.md) mindig (tehát Ön nem kizárva).
12. A fejlesztési, átmeneti és éles környezetek különálló fürtöket karbantartása. 
13. [Alkalmazásfrissítések](service-fabric-application-upgrade.md) és [verziófrissítések fürt](service-fabric-tutorial-upgrade-cluster.md) fejlesztés tesztelését és előkészítési fürtök. 
14. Kapcsolja ki az automatikus frissítéseket éles fürtökben, és kapcsolja be a fejlesztési és átmeneti fürtök (visszaállítás igény szerint). 
15. A helyreállítási időkorlátot (RPO) a szolgáltatás létrehozásához, és állítsa be a [vész-helyreállítási folyamat](service-fabric-disaster-recovery.md) és próbálhatja ki.
16. Tervezze meg [skálázás](service-fabric-cluster-scaling.md) a fürt manuálisan vagy programon keresztül.
17. Tervezze meg [javítás](service-fabric-patch-orchestration-application.md) a fürtcsomópontokat. 
18. A CI/CD-folyamat kialakítani, hogy a legutóbbi módosításoknak folyamatosan tesztelt. Például [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) vagy [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
19. A fejlesztés és a terhelés alatt fürtök átmeneti tesztelése a [Fault Analysis Service](service-fabric-testability-overview.md) és idéz elő az ellenőrzött [káosz](service-fabric-controlled-chaos.md). 
20. Tervezze meg [skálázás](service-fabric-concepts-scalability.md) alkalmazásait. 


Ha a Service Fabric Reliable Services és Reliable Actors programozási modellt használ, a következő elemeket kell ellenőrizni kell,:
21. Ellenőrizze, hogy a szolgáltatás kód van érvényesítenie a megszakítás lexikális elem szerepel a helyi fejlesztés során az alkalmazások frissítése a `RunAsync` metódust, és bezárja az egyéni kommunikációs figyelőket.
22. Kerülje [közös alkalmazásmegoldásokra](service-fabric-work-with-reliable-collections.md) a Reliable Collections használata esetén.
23. Számlálók futtatásakor terheléses teszteket, és ellenőrizze a szemétgyűjtés vagy sorozatos halommemória növekedési nagy mértékű .NET CLR memória teljesítményének figyeléséhez.
24. Offline biztonsági másolat karbantartása [Reliable Services és Reliable Actors](service-fabric-reliable-services-backup-restore.md) és a visszaállítási folyamat tesztelése. 


## <a name="optional-best-practices"></a>Választható eljárások

A fenti listák éles környezetben nyissa meg az előfeltételeket, amelyek a következő elemek figyelembe kell venni:
25. Csatlakoztassa a [Service Fabric állapotmodell](service-fabric-health-introduction.md) kiterjesztheti a beépített állapot-értékelést, és jelentéskészítés.
26. Egy egyéni figyelő által figyelt, az alkalmazás és a jelentések telepítése [betöltése](service-fabric-cluster-resource-manager-metrics.md) a [erőforrás terheléselosztási](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>További lépések
* [Service Fabric Windows-fürt üzembe helyezése](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Linux-alapú Service Fabric-fürt üzembe helyezése](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
