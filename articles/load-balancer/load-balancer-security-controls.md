---
title: A Azure Load Balancer biztonsági vezérlői
description: A Load Balancer értékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214904"
---
# <a name="security-controls-for-azure-load-balancer"></a>A Azure Load Balancer biztonsági vezérlői

Ez a cikk a Azure Load Balancer beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| N/A | |
| VNet-befecskendezés támogatása| N/A | |
| Hálózati elkülönítés és tűzfalak támogatása| N/A |  |
| Kényszerített bújtatás támogatása| N/A | |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor naplók a nyilvános Alapszintű Load Balancerhoz](load-balancer-monitor-log.md). |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Lásd: [Azure monitor naplók a nyilvános Alapszintű Load Balancerhoz](load-balancer-monitor-log.md). |
| Adatsíkok naplózása és naplózása | N/A |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| N/A |  |
| Engedélyezés| N/A |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | N/A | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| N/A | |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | N/A | |
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |
| Titkosított API-hívások| Igen | A [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| N/A |  | 

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).