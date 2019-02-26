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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a0174a5442de7a10e45e9dc2a2f43f5f401d1a69
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805070"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Az Azure Service Fabric-alkalmazás és fürt kapcsolatos ajánlott eljárások

Az Azure Service Fabric-alkalmazások és -fürtök felügyelete a sikeres, nincsenek műveleteket, erősen ajánlott hajt végre, az éles környezetben; megbízhatóságának optimalizálása Ebben a dokumentumban meghatározott műveletek elvégzéséhez, és válassza ki az egyik a [minták Service Fabric-fürt az Azure-sablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates) megkezdése a gyártási megoldás tervezése, vagy módosíthatja a meglévő sablont, és építse be ezeket az eljárásokat.

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

## <a name="checklist"></a>Ellenőrzőlista

Miután végzett az összes fenti szakaszt, győződjön meg arról, hogy integrálta az ajánlott eljárások az üzemi készültségi feladatlista mindegyikét:
* [Az Azure Service Fabric éles készültségi ellenőrzőlista](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Hibaelhárítás: [A Service Fabric – hibaelhárítási útmutató](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)