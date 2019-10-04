---
title: Az Azure ExpressRoute biztonsági vezérlői
description: Az Azure ExpressRoute kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886432"
---
# <a name="security-controls-for-azure-expressroute"></a>Az Azure ExpressRoute biztonsági vezérlői

Ez a cikk az Azure ExpressRoute beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – |  |
| VNet-befecskendezés támogatása| – | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Minden ügyfél saját útválasztási tartományában található, és a saját VNet bújtatott |
| Kényszerített bújtatás támogatása| – | Border Gateway Protocol (BGP) használatával. |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [ExpressRoute-figyelés, mérőszámok és riasztások](expressroute-monitoring-metrics-alerts.md).|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |
| Adatsíkok naplózása és naplózása| Nem |   |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Szolgáltatásfiók a Microsoft számára (GWM) (vezérlő); Igény szerinti (JIT) hozzáférés a dev és az OP számára. |
| Authorization|  Igen |Szolgáltatásfiók a Microsoft számára (GWM) (vezérlő); Igény szerinti (JIT) hozzáférés a dev és az OP számára. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  – | A ExpressRoute nem tárolja az ügyféladatokat. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | – |  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Nem | |
| Titkosított API-hívások| Igen | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | A hálózati erőforrás-szolgáltató (NRP) használatával. |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).