---
title: Biztonsági vezérlők
description: A Azure Resource Manager szolgáltatás értékelésére szolgáló beépített biztonsági vezérlők ellenőrzőlistája.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054457"
---
# <a name="security-controls-for-azure-resource-manager"></a>A Azure Resource Manager biztonsági vezérlői

Ez a cikk a Azure Resource Manager beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes | HTTPS/TLS. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | N.A. | Azure Resource Manager nem tárol felhasználói tartalmat, csak az adatokat vezérli. |
| Oszlop szintű titkosítás (Azure Data Services)| Yes | |
| Titkosított API-hívások| Yes | |

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| No | |
| VNet-befecskendezés támogatása| Yes | |
| Hálózati elkülönítés és tűzfalak támogatása| No |  |
| Kényszerített bújtatás támogatása| No |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| No | |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes | A Tevékenységnaplók teszik elérhetővé az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Lásd: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek naplózásához](view-activity-logs.md). |
| Adatsíkok naplózása és naplózása| N.A. | |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Yes | [Azure Active Directory](../../active-directory/index.yml) alapján.|
| Engedélyezés| Yes | |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).
