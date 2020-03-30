---
title: Az Azure VPN-átjáró biztonsági vezérlői
description: Az Azure VPN-átjáró kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972277"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Az Azure VPN-átjáró biztonsági vezérlői

Ez a cikk az Azure VPN-átjáróba beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| N/A | |
| A VNet injekciózás támogatása| N/A | |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | A VPN-átjárók dedikált virtuálisgép-példányok minden egyes ügyfél virtuális hálózatához  |
| Kényszerített bújtatástámogatása| Igen |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Lásd: [Azure Monitor diagnosztikai naplók/riasztás](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [az Azure Monitor metrikák/riasztás.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)  |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Az Azure Resource Manager tevékenységnaplója. |
| Adatsík naplózása és naplózása | Igen | [Azure Monitor diagnosztikai naplók](../azure-resource-manager/management/view-activity-logs.md) VPN-kapcsolat naplózása és naplózása. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Az Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a szolgáltatás kezeléséhez és az Azure VPN-átjáró konfigurálásához. |
| Engedélyezés| Igen | Támogatási engedélyezés [az RBAC-on](../role-based-access-control/overview.md)keresztül. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | N/A | VPN-átjáró átmenő ügyféladatok, nem tárolja az ügyféladatokat |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat titkosítása és Virtuálishálózati titkosítás)| Igen | A VPN-átjáró titkosítja az ügyfélcsomagokat az Azure VPN-átjárók és az ügyfél helyszíni VPN-eszközei (S2S) vagy VPN-ügyfelek (P2S) között. A VPN-átjárók is támogatják a virtuális hálózat és a virtuális hálózat közötti titkosítást. |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Az ügyfél által megadott előmegosztott kulcsok titkosítva vannak az inkett; de még nem integrált a CMK-vel. |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |
| TITKOSÍTOTT API-hívások| Igen | Az [Azure Resource Manager](../azure-resource-manager/index.yml) és a HTTPS segítségével  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | A felügyeleti műveletek esetén az Azure VPN-átjáró konfigurációjának állapota exportálható Azure Resource Manager-sablonként, és idővel verziószámmal verziójas. |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
