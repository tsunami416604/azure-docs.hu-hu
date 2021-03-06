---
title: Biztonsági vezérlők
description: A Azure Resource Manager szolgáltatás értékelésére szolgáló beépített biztonsági vezérlők ellenőrzőlistája.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054457"
---
# <a name="security-controls-for-azure-resource-manager"></a>A Azure Resource Manager biztonsági vezérlői

Ez a cikk a Azure Resource Manager beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | HTTPS/TLS. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | N/A | Azure Resource Manager nem tárol felhasználói tartalmat, csak az adatokat vezérli. |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | |
| Titkosított API-hívások| Igen | |

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Nem | |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Tevékenységnaplók teszik elérhetővé az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Lásd: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek naplózásához](view-activity-logs.md). |
| Adatsíkok naplózása és naplózása| N/A | |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Igen | [Azure Active Directory](../../active-directory/index.yml) alapján.|
| Engedélyezés| Igen | |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).
