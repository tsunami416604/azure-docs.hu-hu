---
title: Az Azure Security Center által támogatott platformok |} A Microsoft Docs
description: Ez a dokumentum az Azure Security Center támogatott Windows és Linux-operatings rendszerek listáját tartalmazza.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: terrylan
ms.openlocfilehash: b82b152ae4f44e475b5c3e9bda9d604f405503e0
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123731"
---
# <a name="supported-platforms-in-azure-security-center"></a>Az Azure Security Centerben a támogatott platformok
Biztonsági állapot monitorozása és javaslatok érhetők el a virtuális gépek (VM), mind a klasszikus és Resource Manager üzembe helyezési modellek és számítógépek használatával létrehozott.

> [!NOTE]
> Tudjon meg többet a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows-számítógépek és virtuális gépek által támogatott platformok
A támogatott Windows operációs rendszerek:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux rendszerű számítógépek és virtuális gépek által támogatott platformok
Támogatott Linux operációs rendszerek:

* Ubuntu verziók 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian verziók 6, 7, 8, 9
* CentOS verziók 5, 6, 7
* Red Hat Enterprise Linux (RHEL) verziók 5, 6, 7
* SUSE Linux Enterprise Server (SLES) verziók 11, 12
* Oracle Linux-verziók 5, 6, 7
* Amazon Linux 2012.09 2017-ig
* Openssl 1.1.0-s csak támogatott x86_64 platformokon (64 bites)

> [!NOTE]
> Virtuális gép működés elemzése még nem érhető el Linux operációs rendszerekhez.
>
>

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Cloud Services
Egy cloud service-ben futó virtuális gépek is támogatottak. Csak a felhőszolgáltatások tárhelyek figyelt éles üzemben futó webes és feldolgozói szerepköröket. Felhőszolgáltatás kapcsolatos további információkért lásd: [Cloud Services – áttekintés](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – ismerje meg, hogyan tervezheti meg és a tervezési szempontokat az Azure Security Center elfogadása
- [Biztonsági riasztások típus szerint az Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – további tudnivalók a virtuális gép működésének elemzése és az összeomlási memóriakép memória elemzése a Security Centerben
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Az Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi
