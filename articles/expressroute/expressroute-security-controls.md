---
title: 'Azure ExpressRoute: Biztonsági vezérlők'
description: Az Azure ExpressRoute kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079934"
---
# <a name="security-controls-for-azure-expressroute"></a>Az Azure ExpressRoute biztonsági vezérlői

Ez a cikk az Azure ExpressRoute beépített biztonsági vezérlőit dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| N/A |  |
| A VNet injekciózás támogatása| N/A | |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | Minden ügyfél a saját útválasztási tartományában található, és a saját virtuális hálózatához bújtatva |
| Kényszerített bújtatástámogatása| N/A | a Border Gateway Protokoll (BGP) protokollon keresztül. |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Lásd: [ExpressRoute-figyelés, metrikák és riasztások.](expressroute-monitoring-metrics-alerts.md)|
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen |  |
| Adatsík naplózása és naplózása| Nem |   |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | A Microsoft Gateway (GWM) (vezérlő) szolgáltatásfiókja; Csak időben (JIT) hozzáférés a dev és az OP. |
| Engedélyezés|  Igen |A Microsoft Gateway (GWM) (vezérlő) szolgáltatásfiókja; Csak időben (JIT) hozzáférés a dev és az OP. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok |  N/A | Az ExpressRoute nem tárolja az ügyféladatokat. |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | N/A |  |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Nem | |
| TITKOSÍTOTT API-hívások| Igen | Az [Azure Resource Manager](../azure-resource-manager/index.yml) és https. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | a hálózati erőforrás-szolgáltatón (NRP) keresztül. |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)