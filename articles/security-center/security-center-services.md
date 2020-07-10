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
ms.date: 05/27/2020
ms.author: memildin
ms.openlocfilehash: 060db99cf0163876b68eeee2cff8ef2d03e2b5b9
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187013"
---
# <a name="feature-coverage-for-machines"></a>Szolgáltatások lefedettsége gépekhez

Az alábbi táblázatok a virtuális gépek és a kiszolgálók számára elérhető Azure Security Center funkciókat mutatják be.

## <a name="supported-features-for-virtual-machines-and-servers"></a>A virtuális gépek és a kiszolgálók által támogatott funkciók<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows rendszerű gépek](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Szolgáltatás**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Díjszabás**
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Standard|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](threat-protection.md)|✔|✔|✔|Javaslatok (ingyenes) </br></br> Biztonsági riasztások (standard)|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Hálózati biztonsági riasztások](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[VIRTUÁLIS gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Standard|
|[Natív sebezhetőségi felmérés](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Fájl integritásának figyelése](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptív hálózat megerősítése](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|-|-|-|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Ingyenes|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Ingyenes|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Ingyenes|
|Lemez titkosításának felmérése|✔|✔|-|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|


### <a name="linux-machines"></a>[Linux rendszerű gépek](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Szolgáltatás**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Díjszabás**
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|-|-|-|Standard|
|[Virtuális gép viselkedési elemzési (és biztonsági riasztások)](security-center-alerts-iaas.md)|✔</br>(támogatott verziók esetén)|✔</br>(támogatott verziók esetén)|✔|Javaslatok (ingyenes) </br></br> Biztonsági riasztások (standard)|
|[Nem biztonságos biztonsági riasztások](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Hálózati biztonsági riasztások](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[VIRTUÁLIS gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Standard|
|[Natív sebezhetőségi felmérés](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Fájl integritásának figyelése](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptív hálózat megerősítése](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptív hálózati vezérlők|✔|✔|-|Standard|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások elleni védelem a Docker által üzemeltetett IaaS-tárolókban|✔|✔|✔|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Ingyenes|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Ingyenes|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Ingyenes|
|Lemez titkosításának felmérése|✔|✔|-|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|

--- 


> [!TIP]
>A standard díjszabási szinten elérhető funkciókkal való kísérletezéshez az ingyenes szint felhasználói 30 napos próbaidőszak alatt regisztrálhatnak. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.


## <a name="supported-endpoint-protection-solutions"></a>Támogatott Endpoint Protection-megoldások<a name="endpoint-supported"></a>

A következő táblázat a következőkhöz nyújt mátrixot:

 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a listán szereplő Endpoint Protection-megoldás észlelhető, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Windows Defender (Microsoft-kártevőirtó)| Windows Server 2016| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – Deep Security | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux Server termékcsalád  | Nem | igen**\*** |
| Sophos v9 +| Linux Server termékcsalád  | Nem | igen**\***  |

 **\*** A lefedettségi állapot és a támogató adatmennyiség jelenleg csak a védett előfizetésekhez társított Log Analytics munkaterületen érhető el. Ez nem jelenik meg a Azure Security Center-portálon.

> [!NOTE]
> A System Center Endpoint Protection (SCEP) észlelése Windows Server 2008 R2 rendszerű virtuális gépen a PowerShell (v 3.0 vagy újabb) után telepíteni kell a SCEP-t.


## <a name="next-steps"></a>További lépések

- Ismerje meg [, hogyan gyűjt Security Center adatokat és a log Analytics ügynököt](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).