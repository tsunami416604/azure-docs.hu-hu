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
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: a311439e8efc5481fbfd7431c1514ba6be576e39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858013"
---
# <a name="feature-coverage-for-machines"></a>Szolgáltatások lefedettsége gépekhez

Az alábbi két lap a Windows és Linux rendszerű virtuális gépekhez és kiszolgálókhoz elérhető Azure Security Center funkcióit mutatja be.

## <a name="supported-features-for-virtual-machines-and-servers"></a>A virtuális gépek és a kiszolgálók által támogatott funkciók <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows rendszerű gépek**](#tab/features-windows)

|**Szolgáltatás**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Azure Defender szükséges**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Igen|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](alerts-reference.md)|✔|✔|✔|Igen|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|✔|✔|✔|Igen|
|[Hálózati biztonsági riasztások](other-threat-protections.md#network-layer)|✔|✔|-|Igen|
|[Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Igen|
|[Natív sebezhetőségi felmérés](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Igen|
|[Fájlintegritás monitorozása](security-center-file-integrity-monitoring.md)|✔|✔|✔|Igen|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Igen|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Igen|
|[Adaptív hálózat-megerősítés](security-center-adaptive-network-hardening.md)|✔|-|-|Igen|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Igen|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|-|-|-|Igen|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|Lemez titkosításának felmérése|✔</br>( [támogatott forgatókönyvek](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)esetén)|✔|-|Nem|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Nem|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Nem|


### <a name="linux-machines"></a>[**Linux rendszerű gépek**](#tab/features-linux)

|**Szolgáltatás**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Azure Defender szükséges**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|-|-|-|Igen|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](security-center-alerts-iaas.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Igen|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|-|-|-|Igen|
|[Hálózati biztonsági riasztások](other-threat-protections.md#network-layer)|✔|✔|-|Igen|
|[Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Igen|
|[Natív sebezhetőségi felmérés](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Igen|
|[Fájlintegritás monitorozása](security-center-file-integrity-monitoring.md)|✔|✔|✔|Igen|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Igen|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Igen|
|[Adaptív hálózat-megerősítés](security-center-adaptive-network-hardening.md)|✔|-|-|Igen|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Igen|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|✔|✔|✔|Igen|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Azure: nem<br><br>Nem Azure: igen|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Nem|
|Lemez titkosításának felmérése|✔</br>( [támogatott forgatókönyvek](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)esetén)|✔|-|Nem|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Nem|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Nem|

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
| Microsoft Defender víruskereső| Windows Server 2016 vagy újabb| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – Deep Security | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux Server termékcsalád  | Nem | igen **\*** |
| Sophos v9 +| Linux Server termékcsalád  | Nem | igen  **\***  |

 **\*** A lefedettségi állapot és a támogató adatmennyiség jelenleg csak a védett előfizetésekhez társított Log Analytics munkaterületen érhető el. Ez nem jelenik meg a Azure Security Center-portálon.

> [!NOTE]
> A System Center Endpoint Protection (SCEP) észlelése Windows Server 2008 R2 rendszerű virtuális gépen a PowerShell (v 3.0 vagy újabb) után telepíteni kell a SCEP-t.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogy [a Security Center hogyan gyűjt adatokat az log Analytics ügynökkel](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).
