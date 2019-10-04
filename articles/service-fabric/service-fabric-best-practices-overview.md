---
title: Gyakorlati tanácsok az Azure Service Fabric-alkalmazás és fürt |} A Microsoft Docs
description: Service Fabric-fürtök és alkalmazások kezelésének ajánlott eljárásai.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206804"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Az Azure Service Fabric-alkalmazás és fürt kapcsolatos ajánlott eljárások

Ez a cikk az Azure Service Fabric-alkalmazások és -fürtök kezelésére vonatkozó ajánlott eljárások mutató hivatkozásokat tartalmaz. Kifejezetten ajánljuk, hogy alkalmazza ezeket a módszereket, a megbízhatóság az éles környezet optimalizálása érdekében. Használja az egyik a [Service Fabric-fürt sablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates) megkezdéséhez, a gyártási megoldás tervezése, vagy frissítse a meglévő sablont, és építse be ezeket az eljárásokat.

## <a name="security"></a>Biztonság

* [Biztonsági védelmének bevált gyakorlata](service-fabric-best-practices-security.md)

## <a name="networking"></a>Hálózat

* [Ajánlott eljárások a hálózatkezelés](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Számítási teljesítmény tervezése és méretezése

* [Ajánlott eljárások a számítás méretezése](service-fabric-best-practices-capacity-scaling.md)
* [Számítási kapacitásának megtervezése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

* [Infrastruktúra mint kód megvalósításához ajánlott eljárások](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

* [Gyakorlati tanácsok a fürt monitorozás és diagnosztika](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Az alkalmazás kialakítása

* [Ajánlott eljárások az alkalmazás-tervezés](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Ellenőrzőlista

Után alkalmazza a javasolt a korábbi szakaszokban ismertetett eljárásokat, győződjön meg arról, hogy Ön már integrált az éles készültségi feladatlista ajánlott eljárásokat:
* [Az Azure Service Fabric éles készültségi ellenőrzőlista](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A Service Fabric hibaelhárítása: [Hibaelhárítási útmutatók](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)