---
title: Azure Resource Manager biztonsági attribútumai
description: A Azure Resource Manager értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444152"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Azure Resource Manager biztonsági attribútumai

Ez a cikk a Azure Resource Manager beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)| Igen |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | HTTPS/TLS. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| – | Azure Resource Manager nem tárol felhasználói tartalmat, csak az adatokat vezérli. |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | |
| Titkosított API-hívások| Igen | |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Nem | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | [Azure Active Directory](/azure/active-directory) alapján.|
| Authorization| Igen | |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Tevékenységnaplók teszik elérhetővé az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Lásd: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek naplózásához](resource-group-audit.md). |
| Adatsíkok naplózása és naplózása| – | |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  |
