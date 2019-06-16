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
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231382"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Az Azure Service Fabric-alkalmazás és fürt kapcsolatos ajánlott eljárások

Az Azure Service Fabric-alkalmazások és -fürtök felügyelete a sikeres, erősen ajánlott az éles környezetben; megbízhatóságának optimalizálása hajt végre műveleteket vannak Ebben a dokumentumban meghatározott műveletek elvégzéséhez, és válassza ki az egyik a [minták Service Fabric-fürt az Azure-sablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates) megkezdése a gyártási megoldás tervezése, vagy módosíthatja a meglévő sablont, és építse be ezeket az eljárásokat.

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

Miután végzett az összes fenti szakaszt, győződjön meg arról, hogy integrálta az ajánlott eljárások az üzemi készültségi feladatlista mindegyikét:
* [Az Azure Service Fabric éles készültségi ellenőrzőlista](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Hibaelhárítás: [A Service Fabric – hibaelhárítási útmutató](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)