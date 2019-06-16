---
title: Az Azure VPN Gateway biztonsági attribútumok
description: A biztonsági attribútumok az Azure VPN Gateway kiértékelése ellenőrzőlista
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083131"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Az Azure VPN Gateway biztonsági attribútumok

Ez a cikk a gyakori biztonsági attribútumok Azure VPN Gateway épített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például a kiszolgálóoldali titkosítás, a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokat és más titkosítási szolgáltatások) | – | VPN gateway átviteli ügyféladatokat nem tárol ügyféladatokat |
| Titkosítás az átvitel során (például az ExpressRoute-titkosítás, a virtuális hálózatok közötti titkosítás és a VNet – VNet titkosítási)| Igen | VPN-átjáró ügyfél csomagok közötti Azure VPN-átjárók és az ügyfél a helyszíni VPN-eszközök (S2S) vagy (P2S) VPN-ügyfelek titkosítása. VPN-átjárókkal is támogatja a VNet – VNet titkosítást. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem | Az előmegosztott kulcsok ügyfél által megadott titkosított inaktív; de nem integrálva még CMK. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Keresztül [az Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS  |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| – | |
| VNet-injektálási támogatás| – | . |
| Hálózatelkülönítés és Firewalling támogatása| Igen | VPN-átjárók dedikált Virtuálisgép-példány minden egyes virtuális hálózati ügyfél  |
| Kényszerített bújtatás támogatása| Igen |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Lásd: [Azure Monitor Diagnostics Logs/riasztás](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor metrika-riasztás](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Az Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a szolgáltatás kezelése, és az Azure VPN-átjáró konfigurálásához. |
| Engedélyezés| Igen | Támogatja az engedélyezés a következővel [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Az Azure Resource Manager-tevékenységnapló. |
| Adatsík naplózása és naplózása | Igen | [Az Azure Monitor-diagnosztikai naplók](../azure-resource-manager/resource-group-audit.md) a naplózás és vizsgálat VPN-kapcsolatot. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | A felügyeleti műveletek esetében egy Azure VPN gateway konfigurációs állapotát exportálhatók rendszerverzióval ellátott és Azure Resource Manager-sablonok idővel. | 