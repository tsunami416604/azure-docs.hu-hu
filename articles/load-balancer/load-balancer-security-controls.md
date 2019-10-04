---
title: A Azure Load Balancer biztonsági vezérlői
description: A Load Balancer értékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e0be6635a0916183e1dfe776bef4c547578383dc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886450"
---
# <a name="security-controls-for-azure-load-balancer"></a>A Azure Load Balancer biztonsági vezérlői

Ez a cikk a Azure Load Balancer beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – | |
| VNet-befecskendezés támogatása| – | |
| Hálózati elkülönítés és tűzfalak támogatása| – |  |
| Kényszerített bújtatás támogatása| – | |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor naplók a nyilvános](load-balancer-monitor-log.md)alapszintű Load Balancerhoz. |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Lásd: [Azure monitor naplók a nyilvános](load-balancer-monitor-log.md)alapszintű Load Balancerhoz. |
| Adatsíkok naplózása és naplózása | – |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| – |  |
| Authorization| – |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | – | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| – | |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | – | |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | A [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| – |  | 

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).