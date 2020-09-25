---
title: A Azure Security Centerban elérhető támogatott szolgáltatások | Microsoft Docs
description: Ez a dokumentum a Azure Security Center által támogatott szolgáltatások listáját tartalmazza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: cece53544bfc8287f6ef542575d92b05032991f0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318041"
---
# <a name="feature-coverage-for-machines"></a>Szolgáltatások lefedettsége gépekhez

Az alábbi két lap a Windows és Linux rendszerű virtuális gépekhez és kiszolgálókhoz elérhető Azure Security Center funkcióit mutatja be.

## <a name="supported-features-for-virtual-machines-and-servers"></a>A virtuális gépek és a kiszolgálók által támogatott funkciók <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows rendszerű gépek**](#tab/features-windows)

|**Funkció**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Azure Defender szükséges**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Yes|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](alerts-reference.md)|✔|✔|✔|Yes|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|✔|✔|✔|Yes|
|[Hálózati biztonsági riasztások](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Yes|
|[Natív sebezhetőségi felmérés](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Yes|
|[Fájlintegritás monitorozása](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Adaptív hálózat-megerősítés](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|-|-|-|Yes|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Azure: ingyenes<br><br>Nem Azure: igen|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|Lemez titkosításának felmérése|✔</br>( [támogatott forgatókönyvek](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)esetén)|✔|-|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|


### <a name="linux-machines"></a>[**Linux rendszerű gépek**](#tab/features-linux)

|**Funkció**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Azure Defender szükséges**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|-|-|-|Yes|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](security-center-alerts-iaas.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Yes|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|-|-|-|Yes|
|[Hálózati biztonsági riasztások](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Yes|
|[Natív sebezhetőségi felmérés](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Yes|
|[Fájlintegritás monitorozása](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Adaptív hálózat-megerősítés](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|✔|✔|✔|Yes|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Azure: ingyenes<br><br>Nem Azure: igen|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Azure: ingyenes<br><br>Nem Azure: igen|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Ingyenes|
|Lemez titkosításának felmérése|✔</br>( [támogatott forgatókönyvek](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)esetén)|✔|-|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|

--- 


> [!TIP]
>A csak az Azure Defender szolgáltatással elérhető funkciókkal való kísérletezéshez 30 napos próbaverziót regisztrálhat. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.


## <a name="supported-endpoint-protection-solutions"></a>Támogatott Endpoint Protection-megoldások <a name="endpoint-supported"></a>

A következő táblázat a következőkhöz nyújt mátrixot:

 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a listán szereplő Endpoint Protection-megoldás észlelhető, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Microsoft Defender víruskereső| Windows Server 2016 vagy újabb| Nincs, az operációs rendszerbe van beépítve| Yes |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Yes |
| Trend Micro – Deep Security | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux Server termékcsalád  | No | igen **\*** |
| Sophos v9 +| Linux Server termékcsalád  | No | igen  **\***  |

 **\*** A lefedettségi állapot és a támogató adatmennyiség jelenleg csak a védett előfizetésekhez társított Log Analytics munkaterületen érhető el. Ez nem jelenik meg a Azure Security Center-portálon.

> [!NOTE]
> A System Center Endpoint Protection (SCEP) észlelése Windows Server 2008 R2 rendszerű virtuális gépen a PowerShell (v 3.0 vagy újabb) után telepíteni kell a SCEP-t.


## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan gyűjt Security Center adatokat és a log Analytics ügynököt](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).