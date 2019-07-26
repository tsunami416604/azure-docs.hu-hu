---
title: Az Azure VPN Gateway biztonsági attribútumai
description: Az Azure-VPN Gateway értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444587"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Az Azure VPN Gateway biztonsági attribútumai

Ez a cikk az Azure VPN Gateway beépített biztonsági attribútumait dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | – | A VPN Gateway tranzit ügyféladatok nem tárolja az ügyféladatokat |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A VPN-átjáró az ügyfelek csomagjait az Azure VPN-átjárók és az ügyfél helyszíni VPN-eszközei (S2S) vagy a VPN-ügyfelek (P2S-EK) között titkosítja. A VPN-átjárók támogatják a VNet-VNet titkosítást is. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem | Az ügyfél által megadott előmegosztott kulcsok titkosítva vannak a nyugalmi állapotban; de még nincs integrálva a CMK. |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül  |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – | |
| VNet-befecskendezés támogatása| – | . |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A VPN-átjárók dedikált virtuálisgép-példányok minden ügyfélnél Virtual Network  |
| Kényszerített bújtatás támogatása| Igen |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor diagnosztikai naplók/riasztás](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metrikák/riasztások](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a szolgáltatás kezeléséhez és az Azure VPN Gateway konfigurálásához. |
| Authorization| Igen | Támogatás engedélyezése a [RBAC](../role-based-access-control/overview.md)-on keresztül. |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Azure Resource Manager tevékenység naplója. |
| Adatsíkok naplózása és naplózása | Igen | [Azure monitor diagnosztikai naplók](../azure-resource-manager/resource-group-audit.md) a VPN-kapcsolat naplózásához és naplózásához. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetében az Azure VPN Gateway konfigurációjának állapota exportálható Azure Resource Manager sablonként, és az idő múlásával is elvégezhető. | 