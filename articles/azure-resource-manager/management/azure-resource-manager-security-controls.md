---
title: Biztonsági vezérlők
description: A Azure Resource Manager szolgáltatás értékelésére szolgáló beépített biztonsági vezérlők ellenőrzőlistája.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485623"
---
# <a name="security-controls-for-azure-resource-manager"></a>A Azure Resource Manager biztonsági vezérlői

Ez a cikk a Azure Resource Manager beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | HTTPS/TLS. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | – | Azure Resource Manager nem tárol felhasználói tartalmat, csak az adatokat vezérli. |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | |
| Titkosított API-hívások| Igen | |

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Nem | |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Tevékenységnaplók teszik elérhetővé az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Lásd: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek naplózásához](view-activity-logs.md). |
| Adatsíkok naplózása és naplózása| – | |

## <a name="identity"></a>Identitáskezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Azure Active Directory](/azure/active-directory) alapján.|
| Engedélyezés| Igen | |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).