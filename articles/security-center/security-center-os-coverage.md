---
title: Funkciók és az Azure Security Center által támogatott platformok |} A Microsoft Docs
description: Ez a dokumentum az a funkciók és az Azure Security Center által támogatott platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: monhaber
ms.openlocfilehash: c5b5b88ee1334ac6d7b39b8ad53bd020e6042454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480535"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformok és az Azure Security Center által támogatott funkciók

Biztonsági állapot monitorozása és javaslatok érhetők el a virtuális gépek (VM), a klasszikus és Resource Manager üzembe helyezési modellel, és a számítógépek által létrehozott.

> [!NOTE]
> Tudjon meg többet a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Az adatgyűjtési ügynökét támogató platformok 

Ez a szakasz felsorolja a platformokat, amelyeken az Azure Security Center-ügynök futtathatja, és, amelyről adatokat tudjon gyűjteni.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows-számítógépek és virtuális gépek által támogatott platformok
Az alábbi Windows operációs rendszerek támogatottak:

* A Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> A Windows Defender ATP-integráció csak a Windows Server 2012 R2 és Windows Server 2016 támogatja.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux rendszerű számítógépek és virtuális gépek által támogatott platformok

A következő Linux operációs rendszerek támogatottak:

> [!NOTE]
> Mivel a támogatott Linux operációs rendszerek listája folyamatosan változik, ha szeretné, kattintson a [Itt](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) a támogatott verziók a legfrissebb lista megtekintéséhez, abban az esetben, ha módosítás történt, mivel ez a témakör legutóbbi közzétételének.

64 bites
* CentOS 6 és 7
* Amazon Linux 2017.09
* Oracle Linux 6 és 7
* Red Hat Enterprise Linux Server 6 és 7
* Debian GNU/Linux 8. és 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS és 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bites
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8. és 9
* És az Ubuntu Linux 14.04 LTS, 16.04 LTS

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Cloud Services
A felhőalapú szolgáltatásként futtató virtuális gépeket is támogatottak. Csak cloud services webes és feldolgozói szerepkörök üzemelési pont felcserélése futtató figyeli a program. A cloud services kapcsolatos további információkért lásd: [áttekintése az Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Támogatott IaaS-szolgáltatások

> [!div class="mx-tableFixed"]
> 

|Kiszolgáló|Windows||Linux||||Díjszabás|
|----|----|----|----|----|----|----|----|
|**Környezet**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuális gép**|**Virtuálisgép-méretezési csoportot**||**Virtuális gép**|**Virtuálisgép-méretezési csoportot**|
|VMBA fenyegetésészlelési riasztások|✔|✔|✔|✔ (a támogatott verziók)|✔ (a támogatott verziók)|✔|Javaslatok (ingyenes) Fenyegetésészlelés (szokásos)|
|Hálózati fenyegetésészlelés riasztásai|✔|✔|X|✔|✔|X|Standard|
|A Windows Defender ATP-integráció|✔ (a támogatott verziók)|✔ (a támogatott verziók)|✔|X|X|X|Standard|
|Hiányzó javítások|✔|✔|✔|✔|✔|✔|Ingyenes|
|Biztonsági konfigurációk|✔|✔|✔|✔|✔|✔|Ingyenes|
|Endpoint protection-értékelés|✔|✔|✔|X|X|X|Ingyenes|
|Virtuális gépek igény szerinti hozzáférés|✔|X|X|✔|X|X|Standard|
|Adaptív alkalmazásvezérlők|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Lemez titkosítási vizsgálata|✔|✔|X|✔|✔|X|Ingyenes|
|Külső üzembe helyezés|✔|X|X|✔|X|X|Ingyenes|
|NSG-értékelés|✔|✔|X|✔|✔|X|Ingyenes|
|Fileless fenyegetésészlelés|✔|✔|✔|X|X|X|Standard|
|Hálózati térkép létrehozásához|✔|✔|X|✔|✔|X|Standard|
|Az adaptív hálózati vezérlők|✔|✔|X|✔|✔|X|Standard|
|Szabályozási megfelelőségi irányítópult és jelentések|✔|✔|✔|✔|✔|✔|Standard|
|Javaslatok és a fenyegetésészlelés az IaaS-ban üzemeltetett Docker-tárolókon|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Támogatott végpontvédelmi megoldások

Az alábbi táblázat foglalja össze:
 - Az Azure Security Center segítségével akár telepítheti Önnek az egyes megoldások.
 - Melyik endpoint protection megoldásokat a Security Center képes felderíteni. Endpoint protection megoldást egyet felismernek, ha a Security Center nem ajánlott telepítését.

Ezek a védelmi módszerek mindegyike javaslatok létrehozásának kapcsolatos információkért lásd: [Endpoint Protection-értékelés és javaslatok](security-center-endpoint-protection.md).

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

## <a name="supported-paas-features"></a>Támogatott PaaS-szolgáltatások


|Szolgáltatás|A javaslatok (ingyenes)|Fenyegetésészlelés (szokásos)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Az Azure Blob storage-fiókok|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|Virtuális hálózatok|✔| n/a|
|Alhálózatok|✔| n/a|
|Hálózati adapterek (NIC-k)|✔| n/a|
|NSG-k|✔| n/a|
|Előfizetés|✔ **| ✔|
|Batch|✔| n/a|
|Service Fabric|✔| n/a|
|Automation-fiók|✔| n/a|
|Terheléselosztó|✔| n/a|
|Keresés|✔| n/a|
|Service Bus|✔| n/a|
|Stream Analytics|✔| n/a|
|Eseményközpont|✔| n/a|
|Logikai alkalmazások|✔| n/a|
|Tárfiók|✔| n/a|
|Redis|✔| n/a|
|A Data lake analytics|✔| n/a|
|Key Vault|✔| n/a|




\* Nyilvános előzetes verziója jelenleg támogatja ezeket a szolgáltatásokat.

\*\* Aad-ben csak a standard szintű előfizetésekhez érhető el az ajánlások



## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [tervezéséhez és bevezetéséhez az Azure Security Center a tervezési szempontokat](security-center-planning-and-operations-guide.md).
- Tudjon meg többet [virtuális gép működésének elemzése és az összeomlási memóriakép memória elemzéseket végezhet a Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Keresés [az Azure Security Center használatával kapcsolatos gyakori kérdések](security-center-faq.md).
- Keresés [Azure biztonsági és megfelelőségi információ blogbejegyzések](https://blogs.msdn.com/b/azuresecurity/).
