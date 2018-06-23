---
title: Az Azure Security Center által támogatott platformok |} Microsoft Docs
description: Ez a dokumentum az Azure Security Centerben támogatott Windows és Linux-operatings rendszerek listáját jeleníti meg.
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
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 1bb0b61fe8a74edd35bb096fd295aedc67f6efb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335890"
---
# <a name="supported-platforms-in-azure-security-center"></a>Az Azure Security Centerben a támogatott platformok
Virtuális gépek (VM), a klasszikus és Resource Manager üzembe helyezési modellel, és számítógépek használatával létrehozott biztonsági állapotának megfigyelése és javaslatok használhatók.

> [!NOTE]
> További információ a [klasszikus és Resource Manager üzembe helyezési modell](../azure-classic-rm.md) az Azure-erőforrások.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows rendszerű számítógépek és a virtuális gépek által támogatott platformok
Támogatott Windows operációs rendszerek:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux rendszerű számítógépek és a virtuális gépek által támogatott platformok
Támogatott Linux operációs rendszerek:

* Ubuntu verziók 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian verziók 6, 7, 8, 9
* CentOS verziók 5, 6, 7
* Red Hat Enterprise Linux (RHEL) verziók 5, 6, 7
* SUSE Linux Enterprise Server (SLES) verziók 11, 12
* Oracle Linux verziók 5, 6, 7
* Amazon Linux 2012.09 2017 keresztül
* Openssl 1.1.0-ás csak akkor támogatott a x86_64 platformokon (64 bites)

> [!NOTE]
> Virtuális gép viselkedéselemzés még nem állnak rendelkezésre a Linux operációs rendszereken.
>
>

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Felhőszolgáltatások
Felhőszolgáltatásban futó virtuális gépeket is támogatottak. Csak felhőalapú szolgáltatások üzembe helyezési ponti figyelt éles környezetben futó webes és feldolgozói szerepköröket. A felhőalapú szolgáltatás kapcsolatos további információkért lásd: [felhőalapú szolgáltatások – áttekintés](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>További lépések

- [Azure Security Center tervezéséhez és az üzemeltetési útmutatóban](security-center-planning-and-operations-guide.md) – megtudhatja, hogyan tervezhető meg és hogy bevezessék az Azure Security Center tervezéséhez kapcsolódó szempontokat ismertetése
- [Biztonsági riasztások az Azure Security Centerben típusonként](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – további információk a virtuális gép viselkedéselemzése és összeomlás-memóriakép memória elemzése a Security Center
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi
