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
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529481"
---
# <a name="supported-features-available-in-azure-security-center"></a>A Azure Security Centerban elérhető támogatott szolgáltatások

> [!NOTE]
>Néhány funkció csak a standard szinttel érhető el. Ha még nem regisztrált a Security Center Standard csomagra, ingyenes próbaidőszak érhető el. További információkért tekintse meg a [Security Center díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

A következő szakaszban Security Center a [támogatott platformokhoz](security-center-os-coverage.md)elérhető funkciók láthatók.

* [Virtuális gépek/kiszolgálók](#vm-server-features)
* [Pásti-szolgáltatások](#paas-services)


## Virtuális gép/kiszolgáló által támogatott funkciók<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Kiszolgáló|Windows|||Linux|||Díjszabás|
|----|----|----|----|----|----|----|----|
|**Környezet**|**Azure**||**Nem Azure**|**Azure**||**Nem Azure**||
||**Virtuális gép**|**Virtuálisgép-méretezési csoport**||**Virtuális gép**|**Virtuálisgép-méretezési csoport**|
|[Microsoft Defender ATP-integráció](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|X|X|X|Standard|
|[A virtuális gép viselkedési elemzési veszélyforrások észlelésével kapcsolatos riasztások](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|Javaslatok (ingyenes) veszélyforrások észlelése (standard)|
|[A fájlok nem észlelhető veszélyforrások észlelésével kapcsolatos riasztások](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[Hálózati veszélyforrások észlelésével kapcsolatos riasztások](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[VIRTUÁLIS gépek igény szerinti elérése](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[Fájl integritásának figyelése](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[Adaptív alkalmazásvezérlők](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[Hálózati Térkép](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[Adaptív hálózat megerősítése](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|Adaptív hálózati vezérlők|✔|✔|X|✔|✔|X|Standard|
|[Szabályozási megfelelőségi irányítópult & jelentések](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások észlelése a Docker által üzemeltetett IaaS-tárolókban|X|X|X|✔|✔|✔|Standard|
|Hiányzó operációsrendszer-javítások értékelése|✔|✔|✔|✔|✔|✔|Ingyenes|
|Biztonsági konfigurációs beállítások értékelése|✔|✔|✔|✔|✔|✔|Ingyenes|
|[Endpoint Protection-Értékelés](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Ingyenes|
|Lemez titkosításának felmérése|✔|✔|X|✔|✔|X|Ingyenes|
|Harmadik féltől származó sebezhetőségi felmérés|✔|X|X|✔|X|X|Ingyenes|
|[Hálózati biztonsági értékelés](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|Ingyenes|

### Támogatott Endpoint Protection-megoldások<a name="endpoint-supported"></a>

A következő táblázat a következőkhöz nyújt mátrixot:

 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a listán szereplő Endpoint Protection-megoldás észlelhető, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection| Platformok | Security Center telepítése | Security Center felderítése |
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
>
> - A System Center Endpoint Protection (SCEP) Windows Server 2008 R2 rendszerű virtuális gépen való észleléséhez a PowerShell 3,0 (vagy egy felső verzió) után telepíteni kell a SCEP.
> - A Trend Micro Protection észlelése a mély biztonsági ügynökök esetében támogatott.  A OfficeScan-ügynökök nem támogatottak.


## A Pásti Services által <a name="paas-services"></a> támogatott funkciók

A Azure Security Center a következő Pásti-erőforrásokat támogatja:

|Szolgáltatás|Javaslatok (ingyenes)|Fenyegetések észlelése (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Blobtároló|✔| ✔|
|Tárfiók|✔| X|
|App Service|✔| ✔|
|Függvény|✔| X|
|Felhőszolgáltatás|✔| X|
|VNet|✔| X|
|Alhálózat|✔| X|
|Hálózati adapter|✔| X|
|NSG|✔| X|
|Előfizetés|✔ * *| ✔|
|Batch-fiók|✔| X|
|Service Fabric-fiók|✔| X|
|Automation-fiók|✔| X|
|Load Balancer|✔| X|
|Search|✔| X|
|Service Bus-névtér|✔| X|
|Stream Analytics|✔| X|
|Eseményközpont-névtér|✔| X|
|Logikai alkalmazások|✔| X|
|Redis|✔| X|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Key Vault|✔| X|

\* ezek a funkciók jelenleg a nyilvános előzetes verzióban támogatottak.

\* \* Azure Active Directory (Azure AD) javaslatok csak a standard előfizetések esetén érhetők el.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan gyűjt Security Center adatokat és a log Analytics ügynököt](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Megtudhatja, hogyan [tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat](security-center-planning-and-operations-guide.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).
- További információ a [Azure Security Center-beli virtuális gépek & kiszolgálói veszélyforrások észleléséről](security-center-alerts-iaas.md).
- [Gyakori kérdések a Azure Security Center használatáról](security-center-faq.md).
- [Blogbejegyzések az Azure biztonsági és megfelelőségi](https://blogs.msdn.com/b/azuresecurity/)funkcióiról.
