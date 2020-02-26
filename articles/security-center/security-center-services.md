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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 8700421551af227f158abaa38d5f96c8e2987ba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603402"
---
# <a name="supported-features-available-in-azure-security-center"></a>A Azure Security Centerban elérhető támogatott szolgáltatások

> [!NOTE]
>Néhány funkció csak a standard szinttel érhető el. Ha még nem regisztrált a Security Center Standard csomagra, ingyenes próbaidőszak érhető el. További információkért tekintse meg a [Security Center díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

A következő szakaszban Security Center a [támogatott platformokhoz](security-center-os-coverage.md)elérhető funkciók láthatók.

* [Virtuális gépek/kiszolgálók](#vm-server-features)
* [Pásti-szolgáltatások](#paas-services)


## Virtuális gép/kiszolgáló által támogatott funkciók<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure-Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Díjszabás**
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|Standard|
|[A virtuális gép viselkedési elemzési veszélyforrások észlelésével kapcsolatos riasztások](security-center-alerts-iaas.md)|✔|✔|✔|Javaslatok (ingyenes) veszélyforrások észlelése (standard)|
|[A fájlok nem észlelhető veszélyforrások észlelésével kapcsolatos riasztások](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Hálózati veszélyforrások észlelésével kapcsolatos riasztások](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[VIRTUÁLIS gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Standard|
|[Natív sebezhetőségi felmérés](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Fájl integritásának figyelése](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptív hálózat megerősítése](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptív hálózati vezérlők|✔|✔|-|Standard|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások észlelése a Docker által üzemeltetett IaaS-tárolókban|-|-|-|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Ingyenes|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Ingyenes|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Ingyenes|
|Lemez titkosításának felmérése|✔|✔|-|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure-Virtual Machine Scale Sets**|**Nem Azure-beli gépek**|**Díjszabás**
|[Microsoft Defender ATP-integráció](security-center-wdatp.md)|-|-|-|Standard|
|[A virtuális gép viselkedési elemzési veszélyforrások észlelésével kapcsolatos riasztások](security-center-alerts-iaas.md)|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|Javaslatok (ingyenes) veszélyforrások észlelése (standard)|
|[A fájlok nem észlelhető veszélyforrások észlelésével kapcsolatos riasztások](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Hálózati veszélyforrások észlelésével kapcsolatos riasztások](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[VIRTUÁLIS gépek igény szerinti elérése](security-center-just-in-time.md)|✔|-|-|Standard|
|[Natív sebezhetőségi felmérés](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Fájl integritásának figyelése](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Hálózati Térkép](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptív hálózat megerősítése](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptív hálózati vezérlők|✔|✔|-|Standard|
|[Szabályozási megfelelőségi irányítópult & jelentések](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások észlelése a Docker által üzemeltetett IaaS-tárolókban|✔|✔|✔|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|Ingyenes|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|Ingyenes|
|[Endpoint Protection-Értékelés](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Ingyenes|
|Lemez titkosításának felmérése|✔|✔|-|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|-|-|Ingyenes|
|[Hálózati biztonsági értékelés](security-center-network-recommendations.md)|✔|✔|-|Ingyenes|

--- 

## Támogatott Endpoint Protection-megoldások<a name="endpoint-supported"></a>

A következő táblázat a következőkhöz nyújt mátrixot:

 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a listán szereplő Endpoint Protection-megoldás észlelhető, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection (Végpontok védelme)| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Windows Defender (Microsoft-kártevőirtó)| Windows Server 2016| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – minden verzió * | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux Server termékcsalád  | Nem | Igen **\*** |
| Sophos v9 +| Linux Server termékcsalád  | Nem | Igen **\***  |

 **\*** A lefedettségi állapot és a támogató adatmennyiség jelenleg csak a védett előfizetésekhez társított Log Analytics munkaterületen érhető el. Ez nem jelenik meg a Azure Security Center-portálon.

> [!NOTE]
> - A System Center Endpoint Protection (SCEP) Windows Server 2008 R2 rendszerű virtuális gépen való észleléséhez a PowerShell 3,0 (vagy egy felső verzió) után telepíteni kell a SCEP.
> - A Trend Micro Protection észlelése a mély biztonsági ügynökök esetében támogatott.  A OfficeScan-ügynökök nem támogatottak.


## A Pásti Services által <a name="paas-services"></a> támogatott funkciók

A Azure Security Center a következő Pásti-erőforrásokat támogatja:

|Szolgáltatás|Javaslatok (ingyenes)|Fenyegetések észlelésével kapcsolatos riasztások (standard)|Sebezhetőségi felmérés (standard)|
|----|:----:|:----:|:----:|
|SQL Database-adatbázisok|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure-CosmosDB *|-|✔|-|
|Tárfiókok|✔|-|-|
|Blob Storage|✔|✔|-|
|App Service|✔|✔|-|
|Függvényalkalmazás|✔|-|-|
|Felhőszolgáltatások|✔|-|-|
|Virtual Network|✔|-|-|
|Alhálózat|✔|-|-|
|Hálózati adapter|✔|-|-|
|Network Security Groups (Hálózati biztonsági csoportok)|✔|-|-|
|Előfizetést|✔ **|✔|-|
|Batch-fiók|✔|-|-|
|Service Fabric fiók|✔|-|-|
|Automation-fiók|✔|-|-|
|Load Balancer|✔|-|-|
|Cognitive Search|✔|-|-|
|Service Bus-névtér|✔|-|-|
|Stream Analytics|✔|-|-|
|Eseményközpont-névtér|✔|-|-|
|Logikai alkalmazások|✔|-|-|
|Gyorsítótár a Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Key Vault|✔|✔ *|-|

\* ezek a funkciók jelenleg előzetes verzióban támogatottak.

\*\* Azure Active Directory (Azure AD) javaslatok csak a standard előfizetések esetén érhetők el.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan gyűjt Security Center adatokat és a log Analytics ügynököt](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Megtudhatja, hogyan [tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat](security-center-planning-and-operations-guide.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).
- További információ a [Azure Security Center-beli virtuális gépek & kiszolgálói veszélyforrások észleléséről](security-center-alerts-iaas.md).
- [Gyakori kérdések a Azure Security Centerról](faq-general.md).
- [Blogbejegyzések az Azure biztonsági és megfelelőségi](https://blogs.msdn.com/b/azuresecurity/)funkcióiról.