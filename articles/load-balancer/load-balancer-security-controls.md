---
title: Az Azure Load Balancer biztonsági vezérlői
description: A terheléselosztó kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214904"
---
# <a name="security-controls-for-azure-load-balancer"></a>Az Azure Load Balancer biztonsági vezérlői

Ez a cikk az Azure Load Balancer beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| N/A | |
| A VNet injekciózás támogatása| N/A | |
| Hálózati elkülönítés és tűzfaltámogatás| N/A |  |
| Kényszerített bújtatástámogatása| N/A | |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Tekintse meg [az Azure Monitor naplóit a nyilvános alapszintű terheléselosztóhoz.](load-balancer-monitor-log.md) |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Tekintse meg [az Azure Monitor naplóit a nyilvános alapszintű terheléselosztóhoz.](load-balancer-monitor-log.md) |
| Adatsík naplózása és naplózása | N/A |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| N/A |  |
| Engedélyezés| N/A |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | N/A | |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat titkosítása és Virtuálishálózati titkosítás)| N/A | |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | N/A | |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |
| TITKOSÍTOTT API-hívások| Igen | Az [Azure Resource Manager](../azure-resource-manager/index.yml)segítségével. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| N/A |  | 

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)