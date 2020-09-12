---
title: 'Azure ExpressRoute: biztonsági vezérlők'
description: Ismerje meg az Azure ExpressRoute biztonsági vezérlőit, amelyek olyan tulajdonságokat és funkciókat biztosítanak, amelyek segítenek a biztonsági rések megelőzésében, észlelésében és megválaszolásában.
services: expressroute
ms.service: expressroute
author: duongau
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: duau
ms.openlocfilehash: 24057de44f3d28df96bcb93e89af9c3afa6fa3c6
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394907"
---
# <a name="security-controls-for-azure-expressroute"></a>Az Azure ExpressRoute biztonsági vezérlői

Ez a cikk az Azure ExpressRoute beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| N/A |  |
| VNet-befecskendezés támogatása| N/A | |
| Hálózati elkülönítés és tűzfalak támogatása| Yes | Minden ügyfél saját útválasztási tartományában található, és a saját VNet bújtatott |
| Kényszerített bújtatás támogatása| N/A | Border Gateway Protocol (BGP) használatával. |

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
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  N/A | A ExpressRoute nem tárolja az ügyféladatokat. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | N/A |  |
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| No | |
| Titkosított API-hívások| Yes | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Yes | A hálózati erőforrás-szolgáltató (NRP) használatával. |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).