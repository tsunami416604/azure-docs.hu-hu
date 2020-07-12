---
title: Az Azure Service Fabric alkalmazás és a fürt ajánlott eljárásai
description: Ajánlott eljárások és kialakítási szempontok fürtök, alkalmazások és szolgáltatások Azure Service Fabric használatával történő kezeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261088"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Az Azure Service Fabric alkalmazás és a fürt ajánlott eljárásai

Ez a cikk az Azure Service Fabric-alkalmazások és-fürtök kezelésével kapcsolatos ajánlott eljárásokra mutató hivatkozásokat tartalmaz. Javasoljuk, hogy ezeket a gyakorlatokat az éles környezet megbízhatóságának optimalizálása érdekében implementálja. Az [Service Fabric-sablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates) egyikével megkezdheti a termelési megoldás megtervezését, vagy frissítheti a meglévő sablont a gyakorlatok beépítéséhez.

## <a name="security"></a>Biztonság

* [Ajánlott biztonsági eljárások](service-fabric-best-practices-security.md)

## <a name="networking"></a>Hálózat

* [Ajánlott eljárások a hálózatkezeléshez](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Számítási teljesítmény tervezése és méretezése

* [Ajánlott eljárások a számítási skálázáshoz](service-fabric-best-practices-capacity-scaling.md)
* [Számítási kapacitás megtervezése](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

* [Ajánlott eljárások az infrastruktúra kódként való megvalósításához](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

* [Ajánlott eljárások a fürtök monitorozásához és diagnosztizálásához](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Az alkalmazás kialakítása

* [Ajánlott eljárások az alkalmazások kialakításához](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Ellenőrzőlista

Az előző fejezetekben javasolt eljárások megvalósítása után győződjön meg arról, hogy az üzemi készültségi ellenőrzőlista összes ajánlott eljárása be van építve:
* [Azure Service Fabric üzemi készültségi ellenőrzőlista](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>Következő lépések

* Fürt létrehozása a Windows Servert futtató virtuális gépeken vagy számítógépeken: [Service Fabric Windows Server-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Hibaelhárítási Service Fabric: [útmutatók](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
