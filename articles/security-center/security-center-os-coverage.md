---
title: "Az Azure Security Center által támogatott platformok |} Microsoft Docs"
description: "Ez a dokumentum az Azure Security Centerben támogatott Windows és Linux-operatings rendszerek listáját jeleníti meg."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: terrylan
ms.openlocfilehash: c80e78b39b3fba4dcd9523e1e679822758822805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="supported-platforms-in-azure-security-center"></a>Az Azure Security Centerben a támogatott platformok
Biztonsági állapotának megfigyelése és javaslatok érhetők el a klasszikus és Resource Manager üzembe helyezési modell használatával létrehozott virtuális gépek (VM).

> [!NOTE]
> További információ a [klasszikus és Resource Manager üzembe helyezési modell](../azure-classic-rm.md) az Azure-erőforrások.
>
>

## <a name="supported-platforms-for-windows-vms"></a>Windows virtuális gépek által támogatott platformok
Támogatott Windows operációs rendszerek:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-vms"></a>Linux virtuális gépek által támogatott platformok
Támogatott Linux operációs rendszerek:

* Ubuntu verziók 12.04, 14.04, 16.04, 16.10
* Debian verziók 7, 8
* A centOS 6 verziók. \*, 7.*
* Red Hat Enterprise Linux (RHEL) verziók 6. \*, 7.*
* SUSE Linux Enterprise Server (SLES) 11 SP4 + verziók, 12.*
* Oracle Linux 6 verziók. \*, 7.*

> [!NOTE]
> Virtuális gép viselkedéselemzés még nem állnak rendelkezésre a Linux operációs rendszereken.
>
>

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Felhőszolgáltatások
Felhőszolgáltatásban futó virtuális gépeket is támogatottak. Csak felhőalapú szolgáltatások üzembe helyezési ponti figyelt éles környezetben futó webes és feldolgozói szerepköröket. A felhőalapú szolgáltatás kapcsolatos további információkért lásd: [felhőalapú szolgáltatások – áttekintés](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Következő lépések

- [Azure Security Center tervezéséhez és az üzemeltetési útmutatóban](security-center-planning-and-operations-guide.md) – megtudhatja, hogyan tervezhető meg és hogy bevezessék az Azure Security Center tervezéséhez kapcsolódó szempontokat ismertetése
- [Biztonsági riasztások az Azure Security Centerben típusonként](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis) – további információk a virtuális gép viselkedéselemzése és összeomlás-memóriakép memória elemzése a Security Center
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi
