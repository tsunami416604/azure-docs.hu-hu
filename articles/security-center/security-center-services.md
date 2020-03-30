---
title: Az Azure Security Center támogatott szolgáltatásai | Microsoft dokumentumok
description: Ez a dokumentum az Azure Security Center által támogatott szolgáltatások listáját tartalmazza.
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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245238"
---
# <a name="feature-coverage-for-machines"></a>Szolgáltatás lefedettség gépek

Az alábbi táblázatok az Azure Security Center virtuális gépekhez és kiszolgálókhoz elérhető funkcióit mutatják.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Virtuális gépek és kiszolgálók támogatott szolgáltatásai<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows-gépek](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Szolgáltatás**|**Azure virtuális gépek**|**Azure Virtual Machine Scale Sets**|**Nem Azure-alapú gépek**|**Díjszabás**
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|✔</br>(a támogatott verziókon)|✔</br>(a támogatott verziókon)|✔|Standard|
|[Virtuális gép viselkedési elemzése (és biztonsági riasztások)](threat-protection.md)|✔|✔|✔|Ajánlások (ingyenes) </br></br> Biztonsági riasztások (Standard)|
|[Fájl nélküli biztonsági riasztások](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Hálózati biztonsági riasztások](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time virtuális gép hozzáférés](security-center-just-in-time.md)|✔|-|-|Standard|
|[Natív sebezhetőségi értékelés](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Fájlintegritás figyelése](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Hálózati térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptív hálózati edzés](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptív hálózati vezérlők|✔|✔|-|Standard|
|[A jogszabályi megfelelőség irányítópultja & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Javaslatok és fenyegetésvédelem a Docker által üzemeltetett IaaS-tárolókon|-|-|-|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Ingyenes|
|A biztonság helytelen konfigurációinak felmérése|✔|✔|✔|Ingyenes|
|[Végpontvédelem értékelése](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Ingyenes|
|Lemeztitkosítás értékelése|✔|✔|-|Ingyenes|
|Harmadik fél biztonsági résének értékelése|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|


### <a name="linux-machines"></a>[Linux gépek](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Szolgáltatás**|**Azure virtuális gépek**|**Azure Virtual Machine Scale Sets**|**Nem Azure-alapú gépek**|**Díjszabás**
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|-|-|-|Standard|
|[Virtuális gép viselkedési elemzése (és biztonsági riasztások)](security-center-alerts-iaas.md)|✔</br>(a támogatott verziókon)|✔</br>(a támogatott verziókon)|✔|Ajánlások (ingyenes) </br></br> Biztonsági riasztások (Standard)|
|[Fájl nélküli biztonsági riasztások](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Hálózati biztonsági riasztások](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time virtuális gép hozzáférés](security-center-just-in-time.md)|✔|-|-|Standard|
|[Natív sebezhetőségi értékelés](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Fájlintegritás figyelése](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Hálózati térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptív hálózati edzés](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptív hálózati vezérlők|✔|✔|-|Standard|
|[A jogszabályi megfelelőség irányítópultja & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Javaslatok és fenyegetésvédelem a Docker által üzemeltetett IaaS-tárolókon|✔|✔|✔|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Ingyenes|
|A biztonság helytelen konfigurációinak felmérése|✔|✔|✔|Ingyenes|
|[Végpontvédelem értékelése](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Ingyenes|
|Lemeztitkosítás értékelése|✔|✔|-|Ingyenes|
|Harmadik fél biztonsági résének értékelése|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|

--- 


> [!TIP]
>Ha olyan funkciókkal szeretne kísérletezni, amelyek csak a standard tarifacsomagon érhetők el, az ingyenes szint felhasználói regisztrálhatnak egy 30 napos próbaverzióra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.


## <a name="supported-endpoint-protection-solutions"></a>Támogatott végpontvédelmi megoldások<a name="endpoint-supported"></a>

Az alábbi táblázat a következőkmátrixát tartalmazza:

 - Használhatja-e az Azure Security Centert az egyes megoldások telepítéséhez.
 - Mely végpontvédelmi megoldásokat fedezheti fel a Security Center. Ha a rendszer végpontvédelmi megoldást fedez fel ebből a listából, a Security Center nem javasolja a telepítést.

Ha tudni szeretné, hogy mikor jönnek létre javaslatok az egyes védelemekhez, olvassa el [az Endpoint protection assessment and Recommendations (Végpontok védelmének értékelése és ajánlásai) című témakört.](security-center-endpoint-protection.md)

| Endpoint Protection (Végpontok védelme)| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Windows Defender (Microsoft-kártevőirtó)| Windows Server 2016| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – Minden verzió* | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux server család  | Nem | igen**\*** |
| Sophos V9+| Linux server család  | Nem | igen**\***  |

 **\*** A lefedettségi állapot és a támogató adatok jelenleg csak a védett előfizetésekhez társított Log Analytics-munkaterületen érhetők el. Ez nem tükröződik az Azure Security Center portálon.

> [!NOTE]
> - A System Center Endpoint Protection (SCEP) észlelése Windows Server 2008 R2 virtuális gépen az SCEP telepítéséhez a PowerShell 3.0 (vagy egy felső verzió) után kell telepíteni.
> - A Trend Micro védelem észlelése a Deep Security ügynökök számára támogatott.  Az OfficeScan-ügynökök nem támogatottak.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [gyűjti a Security Center az adatokat és a Log Analytics-ügynököt.](security-center-enable-data-collection.md)
- Ismerje meg, hogyan [kezeli és védi a Security Center az adatokat.](security-center-data-security.md)
- Ismerje meg, hogyan [tervezheti meg és ismerheti meg az Azure Security Center bevezetésének tervezési szempontjait.](security-center-planning-and-operations-guide.md)
- Tekintse át a [biztonsági központot támogató platformokat.](security-center-os-coverage.md)
- További információ a [Windows- és Linux-gépek fenyegetésvédelmi](threat-protection.md#windows-machines)szolgáltatásáról az Azure Security Centerben.
- Gyakori [kérdések az Azure Security Center használatával kapcsolatban.](faq-general.md)