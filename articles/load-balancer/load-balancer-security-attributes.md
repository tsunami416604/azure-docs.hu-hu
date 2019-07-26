---
title: Azure Load Balancer biztonsági attribútumai
description: A Load Balancer értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440869"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure Load Balancer biztonsági attribútumai

Ez a cikk a Azure Load Balancer beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | – | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| – | |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| – | |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | A [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – | |
| VNet-befecskendezés támogatása| – | . |
| Hálózati elkülönítés és tűzfalak támogatása| – |  |
| Kényszerített bújtatás támogatása| – | |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor naplók a nyilvános](load-balancer-monitor-log.md)alapszintű Load Balancerhoz. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| – |  |
| Authorization| – |  |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Lásd: [Azure monitor naplók a nyilvános](load-balancer-monitor-log.md)alapszintű Load Balancerhoz. |
| Adatsíkok naplózása és naplózása | – |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| – |  | 
