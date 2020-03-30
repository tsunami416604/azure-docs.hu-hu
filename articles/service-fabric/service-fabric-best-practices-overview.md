---
title: Az Azure Service Fabric-alkalmazás és a fürt ajánlott eljárásai
description: Ajánlott eljárások és tervezési szempontok a fürtök, alkalmazások és szolgáltatások Azure Service Fabric használatával történő kezeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551777"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Az Azure Service Fabric-alkalmazás és a fürt ajánlott eljárásai

Ez a cikk az Azure Service Fabric-alkalmazások és fürtök kezelésével kapcsolatos gyakorlati tanácsokra mutató hivatkozásokat tartalmaz. Javasoljuk, hogy az éles környezet megbízhatóságának optimalizálása érdekében valósítsa meg ezeket a gyakorlatokat. Használja a [Service Fabric fürtsablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates) egyikével az éles megoldás tervezésének megkezdéséhez, vagy frissítse a meglévő sablont, hogy beépítse ezeket a gyakorlatokat.

## <a name="security"></a>Biztonság

* [Gyakorlati tanácsok a biztonsággal kapcsolatosakban](service-fabric-best-practices-security.md)

## <a name="networking"></a>Hálózat

* [Gyakorlati tanácsok a hálózatépítéshez](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Számítási teljesítmény tervezése és méretezése

* [A számítási méretezés ajánlott eljárásoka](service-fabric-best-practices-capacity-scaling.md)
* [Számítási kapacitástervezés](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

* [Az infrastruktúra kódként történő megvalósításának bevált gyakorlatai](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

* [Gyakorlati tanácsok a fürtfigyeléshez és -diagnosztikához](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Az alkalmazás kialakítása

* [Gyakorlati tanácsok az alkalmazástervezéshez](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Ellenőrzőlista

Az előző szakaszokban javasolt eljárások végrehajtása után győződjön meg arról, hogy az összes ajánlott gyakorlatot beépítette a termelési készenléti ellenőrzőlistába:
* [Az Azure Service Fabric éles készültségi ellenőrzőlistája](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>További lépések

* Fürt létrehozása virtuális gépeken vagy Windows Server rendszert futtató számítógépeken: [Service Fabric-fürt létrehozása Windows Server rendszerhez](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxot futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Szolgáltatásháló – [hibaelhárítási útmutatók](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)