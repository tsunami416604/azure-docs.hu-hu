---
title: Az Azure Event Hubs biztonsági attribútumai
description: Az Azure-Event Hubs értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin
ms.openlocfilehash: 2262609de774eb2b1334215bf46968b5554ed691
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442450"
---
# <a name="security-attributes-for-azure-event-hubs"></a>Az Azure Event Hubs biztonsági attribútumai

Ez a cikk az Azure Event Hubs beépített biztonsági attribútumait dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)|  Igen | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen |  |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Nem | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | |
| Authorization|  Igen | |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |
| Adatsíkok naplózása és naplózása| Igen |   |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |
