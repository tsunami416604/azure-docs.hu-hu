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
ms.openlocfilehash: d756f9dfbd0012f884bb0c4a1e27efc76d613234
ms.sourcegitcommit: 387da88b8262368c1b67fffea58fe881308db1c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71982837"
---
# <a name="supported-features-available-in-azure-security-center"></a>A Azure Security Centerban elérhető támogatott szolgáltatások

> [!NOTE]
>Néhány funkció csak a standard szinttel érhető el. Ha még nem regisztrált a Security Center Standard csomagra, ingyenes próbaidőszak érhető el. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).

A következő szakaszban Security Center a [támogatott platformokhoz](security-center-os-coverage.md)elérhető funkciók láthatók.

* [Virtuális gépek/kiszolgálók](#vm-server-features)
* [Pásti-szolgáltatások](#paas-services)


## Virtuális gép/kiszolgáló által támogatott funkciók<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Kiszolgáló|Windows|||Linux|||Díjszabás|
|----|----|----|----|----|----|----|----|
|**Környezet**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuális gép**|**Virtuálisgép-méretezési csoport**||**Virtuális gép**|**Virtuálisgép-méretezési csoport**|
|A virtuális gép viselkedési elemzési veszélyforrások észlelésével kapcsolatos riasztások|✔|✔|✔|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|Javaslatok (ingyenes) veszélyforrások észlelése (standard)|
|Hálózati veszélyforrások észlelésével kapcsolatos riasztások|✔|✔|X|✔|✔|X|Standard|
|Microsoft Defender ATP-integráció|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|X|X|X|Standard|
|Hiányzó javítások|✔|✔|✔|✔|✔|✔|Free|
|Biztonsági konfigurációk|✔|✔|✔|✔|✔|✔|Free|
|Endpoint Protection-Értékelés|✔|✔|✔|X|X|X|Free|
|VIRTUÁLIS gépek igény szerinti elérése|✔|X|X|✔|X|X|Standard|
|Adaptív alkalmazásvezérlés|✔|X|✔|✔|X|✔|Standard|
|Fájlintegritási monitorozás|✔|✔|✔|✔|✔|✔|Standard|
|Lemez titkosításának felmérése|✔|✔|X|✔|✔|X|Free|
|Külső gyártótól származó telepítés|✔|X|X|✔|X|X|Free|
|NSG-Értékelés|✔|✔|X|✔|✔|X|Free|
|Fájlok nem észlelhető veszélyforrások észlelése|✔|✔|✔|X|X|X|Standard|
|Hálózati térkép|✔|✔|X|✔|✔|X|Standard|
|Hálózatok adaptív megerősítése|✔|X|X|✔|X|X|Standard|
|Adaptív hálózati vezérlők|✔|✔|X|✔|✔|X|Standard|
|Szabályozási megfelelőségi irányítópult & jelentések|✔|✔|✔|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások észlelése a Docker által üzemeltetett IaaS-tárolókban|X|X|X|✔|✔|✔|Standard|

### Támogatott Endpoint Protection-megoldások<a name="endpoint-supported"></a>

A következő táblázat a következőkhöz nyújt mátrixot:

 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a rendszer felderíti az egyik Endpoint Protection-megoldást, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Windows Defender (Microsoft-kártevőirtó)| Windows Server 2016| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – minden verzió * | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Linux Server termékcsalád  | Nem | Igen **@no__t – 1** |
| Sophos v9 +| Linux Server termékcsalád  | Nem | Igen **@no__t – 1**  |

 **@no__t – 1** A lefedettségi állapot és a támogató adatmennyiség jelenleg csak a védett előfizetésekhez társított Log Analytics munkaterületen érhető el, és nem jelenik meg Azure Security Center portálon.

> [!NOTE]
>
> - A System Center Endpoint Protection (SCEP) Windows Server 2008 R2 rendszerű virtuális gépen való észleléséhez a PowerShell 3,0 (vagy egy felső verzió) után telepíteni kell a SCEP.
> - A Trend Micro Protection észlelése a mély biztonsági ügynökök esetében támogatott.  A OfficeScan-ügynökök nem támogatottak.


## A Pásti Services által <a name="paas-services"></a> támogatott funkciók

A Azure Security Center a következő Pásti-erőforrásokat támogatja:

|Szolgáltatás|Javaslatok (ingyenes)|Fenyegetések észlelése (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Blob Storage|✔| ✔|
|Tárfiók|✔| NA|
|App Service-ben|✔| ✔|
|Függvény|✔| X|
|Felhőszolgáltatás|✔| X|
|VNet|✔| NA|
|Subnet|✔| NA|
|Hálózati adapter|✔| NA|
|NSG|✔| NA|
|Subscription|✔ **| ✔|
|Batch-fiók|✔| X|
|Service Fabric-fiók|✔| X|
|Automation-fiók|✔| X|
|Terheléselosztó|✔| X|
|Keresés|✔| X|
|Service Bus-névtér|✔| X|
|Streamelemzés|✔| X|
|Eseményközpont-névtér|✔| X|
|Logikai alkalmazások|✔| X|
|Redis|✔| NA|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Kulcstartó|✔| X|

\* ezek a funkciók jelenleg a nyilvános előzetes verzióban támogatottak.

\* @ no__t-1 Azure Active Directory (Azure AD) javaslatok csak standard előfizetések esetén érhetők el.

## <a name="next-steps"></a>További lépések

- Ismerje meg [, hogyan gyűjt Security Center adatokat és a log Analytics ügynököt](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Megtudhatja, hogyan [tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat](security-center-planning-and-operations-guide.md).
- Tekintse át a [Security centert támogató platformokat](security-center-os-coverage.md).
- További információ a [Azure Security Center-beli virtuális gépek & kiszolgálói veszélyforrások észleléséről](security-center-alerts-iaas.md).
- [Gyakori kérdések a Azure Security Center használatáról](security-center-faq.md).
- [Blogbejegyzések az Azure biztonsági és megfelelőségi](https://blogs.msdn.com/b/azuresecurity/)funkcióiról.
