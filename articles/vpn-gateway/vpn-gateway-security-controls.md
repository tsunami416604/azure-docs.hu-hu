---
title: Az Azure VPN Gateway biztonsági vezérlői
description: Az Azure-VPN Gateway kiértékelésére szolgáló biztonsági ellenőrzési ellenőrzőlista
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127855"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Az Azure VPN Gateway biztonsági vezérlői

Ez a cikk az Azure VPN Gateway beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| N/A | |
| VNet-befecskendezés támogatása| N/A | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A VPN-átjárók dedikált virtuálisgép-példányok minden ügyfélnél Virtual Network  |
| Kényszerített bújtatás támogatása| Igen |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor log riasztás](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metrikák riasztása](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Azure Resource Manager tevékenység naplója. |
| Adatsíkok naplózása és naplózása | Igen | Azure Monitor a VPN-kapcsolat naplózása és naplózása [tevékenység naplóit](../azure-resource-manager/management/view-activity-logs.md) . |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a szolgáltatás kezeléséhez és az Azure VPN Gateway konfigurálásához. |
| Engedélyezés| Igen | Támogatás engedélyezése a [RBAC](../role-based-access-control/overview.md)-on keresztül. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | N/A | A VPN Gateway tranzit ügyféladatok nem tárolja az ügyféladatokat |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A VPN-átjáró az ügyfelek csomagjait az Azure VPN-átjárók és az ügyfél helyszíni VPN-eszközei (S2S) vagy a VPN-ügyfelek (P2S-EK) között titkosítja. A VPN-átjárók támogatják a VNet-VNet titkosítást is. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Az ügyfél által megadott előmegosztott kulcsok titkosítva vannak a nyugalmi állapotban; de még nincs integrálva a CMK. |
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |
| Titkosított API-hívások| Igen | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetében az Azure VPN Gateway konfigurációjának állapota exportálható Azure Resource Manager sablonként, és az idő múlásával is elvégezhető. |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
