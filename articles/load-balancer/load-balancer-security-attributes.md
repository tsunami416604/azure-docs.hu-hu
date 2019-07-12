---
title: Biztonsági attribútumok az Azure Load Balancerhez
description: A Load Balancer értékelésére biztonsági attribútumok ellenőrzőlista
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800080"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Biztonsági attribútumok az Azure Load Balancerhez

Ez a cikk a gyakori biztonsági attribútumok az Azure Load Balancer beépített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például a kiszolgálóoldali titkosítás, a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokat és más titkosítási szolgáltatások) | – | |
| Titkosítás az átvitel során (például az ExpressRoute-titkosítás, a virtuális hálózatok közötti titkosítás és a VNet – VNet titkosítási)| – | |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| – | |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | -N keresztül a [az Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| – | |
| VNet-injektálási támogatás| – | . |
| Hálózatelkülönítés és Firewalling támogatása| – |  |
| Kényszerített bújtatás támogatása| – | |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Lásd: [Azure Monitor-naplókban a alapszintű nyilvános Load Balancer](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| – |  |
| Authorization| – |  |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Lásd: [Azure Monitor-naplókban a alapszintű nyilvános Load Balancer](load-balancer-monitor-log.md). |
| Adatsík naplózása és naplózása | – |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| – |  | 
