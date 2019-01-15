---
title: Funkciók és az Azure Security Center által támogatott platformok |} A Microsoft Docs
description: Ez a dokumentum az a funkciók és az Azure Security Center által támogatott platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/9/2019
ms.author: rkarlin
ms.openlocfilehash: 98d2d29e7822d9ca97ba488fcf67298a0b40efbf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265464"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformok és az Azure Security Center által támogatott funkciók

Biztonsági állapot monitorozása és javaslatok érhetők el a virtuális gépek (VM), a klasszikus és Resource Manager üzembe helyezési modellel, és a számítógépek által létrehozott.

> [!NOTE]
> Tudjon meg többet a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
>
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Az adatgyűjtési ügynökét támogató platformok 

Ez a szakasz felsorolja a platformokat, amelyeken az Azure Security Center-ügynök futtathatja, és, amelyről adatokat tudjon gyűjteni.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows-számítógépek és virtuális gépek által támogatott platformok
Az alábbi Windows operációs rendszerek támogatottak:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

> [!NOTE]
> A Windows Defender ATP-integráció csak a Windows Server 2012 R2 és Windows Server 2016 támogatja.
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux rendszerű számítógépek és virtuális gépek által támogatott platformok
A következő Linux operációs rendszerek támogatottak:

* Ubuntu 12.04 LTS, 14.04 LTS és 16.04 LTS verzió.
* Debian verziók 6, 7, 8 és 9.
* CentOS 5, 6 és 7.
* Red Hat Enterprise Linux (RHEL) verziójú 5, 6 és 7.
* SUSE Linux Enterprise Server (SLES) verziók 11 – 12.
* Oracle Linux-verziók 5, 6 és 7.
* Amazon Linux 2012.09 2017-ig.
* OpenSSL 1.1.0-s csak támogatott platformokon x86_64, 64 bites.

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Cloud Services
A felhőalapú szolgáltatásként futtató virtuális gépeket is támogatottak. Csak cloud services webes és feldolgozói szerepkörök üzemelési pont felcserélése futtató figyeli a program. A cloud services kapcsolatos további információkért lásd: [áttekintése az Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Támogatott IaaS-szolgáltatások

> [!div class="mx-tableFixed"]
> 

|Kiszolgáló|Windows||Linux||
|----|----|----|----|----|
|Környezet|Azure|Nem Azure|Azure|Nem Azure|
|VMBA fenyegetésészlelési riasztások|✔|✔|✔ (a támogatott verziók)|✔|
|Hálózati fenyegetésészlelés riasztásai|✔|X|✔|X|
|A Windows Defender ATP-integráció|✔ (a támogatott verziók)|✔|X|X|
|Hiányzó javítások|✔|✔|✔|✔|
|Biztonsági konfigurációk|✔|✔|✔|✔|
|Endpoint protection (Végpontok védelme)|✔|✔|X|X|
|Virtuális gépek igény szerinti elérése|✔|X|✔|X|
|Adaptív alkalmazásvezérlők|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|Disk encryption (Lemeztitkosítás)|✔|X|✔|X|
|Külső üzembe helyezés|✔|X|✔|X|
|NSG-k|✔|X|✔|X|
|Fileless fenyegetésészlelés|✔|✔|X|X|
|Hálózati térkép létrehozásához|✔|X|✔|X|
|Az adaptív hálózati vezérlők|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>Támogatott végpontvédelmi megoldások

Az alábbi táblázat foglalja össze:
 - Az Azure Security Center segítségével akár telepítheti Önnek az egyes megoldások.
 - Melyik endpoint protection megoldásokat a Security Center képes felderíteni. Endpoint protection megoldást egyet felismernek, ha a Security Center nem ajánlott telepítését.

| Endpoint Protection (Végpontok védelme)| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Windows Defender (Microsoft-kártevőirtó)| Windows Server 2016| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | A Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – Összes verzió | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| Kaspersky| Windows Server termékcsalád  | Nem | Nem  |
| Sophos| Windows Server termékcsalád  | Nem | Nem  |

> [!NOTE]
> - System Center Endpoint Protection (SCEP) észlelése a Windows Server 2008 R2 virtuális gépeken szükséges SCEP után a PowerShell 3.0 (vagy egy felső verzió) kell telepíteni.
>
>

## <a name="supported-paas-features"></a>Támogatott PaaS-szolgáltatások 


|Szolgáltatás|Javaslatok|Fenyegetések észlelése|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Az Azure Blob storage fiókok *|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|Virtuális hálózatok|✔| NA|
|Alhálózatok|✔| NA|
|Hálózati adapterek (NIC-k)|✔| ✔|
|NSG-k|✔| NA|
|Előfizetés|✔| ✔|

\* Nyilvános előzetes verziója jelenleg támogatja ezeket a szolgáltatásokat. 



## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [tervezéséhez és bevezetéséhez az Azure Security Center a tervezési szempontokat](security-center-planning-and-operations-guide.md).
- Tudjon meg többet [virtuális gép működésének elemzése és az összeomlási memóriakép memória elemzéseket végezhet a Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Keresés [az Azure Security Center használatával kapcsolatos gyakori kérdések](security-center-faq.md).
- Keresés [Azure biztonsági és megfelelőségi információ blogbejegyzések](https://blogs.msdn.com/b/azuresecurity/).
