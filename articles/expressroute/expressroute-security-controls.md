---
title: 'Azure ExpressRoute: biztonsági vezérlők'
description: Az Azure ExpressRoute kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74079934"
---
# <a name="security-controls-for-azure-expressroute"></a>Az Azure ExpressRoute biztonsági vezérlői

Ez a cikk az Azure ExpressRoute beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| N.A. |  |
| VNet-befecskendezés támogatása| N.A. | |
| Hálózati elkülönítés és tűzfalak támogatása| Yes | Minden ügyfél saját útválasztási tartományában található, és a saját VNet bújtatott |
| Kényszerített bújtatás támogatása| N.A. | Border Gateway Protocol (BGP) használatával. |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | Lásd: [ExpressRoute-figyelés, mérőszámok és riasztások](expressroute-monitoring-metrics-alerts.md).|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes |  |
| Adatsíkok naplózása és naplózása| No |   |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Yes | Szolgáltatásfiók a Microsoft számára (GWM) (vezérlő); Igény szerinti (JIT) hozzáférés a dev és az OP számára. |
| Engedélyezés|  Yes |Szolgáltatásfiók a Microsoft számára (GWM) (vezérlő); Igény szerinti (JIT) hozzáférés a dev és az OP számára. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  N.A. | A ExpressRoute nem tárolja az ügyféladatokat. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | N.A. |  |
| Oszlop szintű titkosítás (Azure Data Services)| N.A. | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| No | |
| Titkosított API-hívások| Yes | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Yes | A hálózati erőforrás-szolgáltató (NRP) használatával. |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).