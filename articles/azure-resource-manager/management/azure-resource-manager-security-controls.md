---
title: Biztonsági vezérlők
description: Az Azure Resource Manager szolgáltatás kiértékeléséhez beépített biztonsági vezérlők ellenőrzőlistája.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485623"
---
# <a name="security-controls-for-azure-resource-manager"></a>Az Azure Resource Manager biztonsági vezérlői

Ez a cikk az Azure Resource Manager beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | HTTPS/TLS. |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | N/A | Az Azure Resource Manager nem tárol ügyféltartalmat, csak vezérlőadatokat. |
| Oszlopszintű titkosítás (Azure Data Services)| Igen | |
| TITKOSÍTOTT API-hívások| Igen | |

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| Nem | |
| A VNet injekciózás támogatása| Igen | |
| Hálózati elkülönítés és tűzfaltámogatás| Nem |  |
| Kényszerített bújtatástámogatása| Nem |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Nem | |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | A tevékenységnaplók az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE) teszik elérhetővé; lásd: [Tevékenységnaplók megtekintése az erőforrásokkal kapcsolatos műveletek naplózásához.](view-activity-logs.md) |
| Adatsík naplózása és naplózása| N/A | |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Az Azure Active Directory](/azure/active-directory) alapú.|
| Engedélyezés| Igen | |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen |  |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../../security/fundamentals/security-controls.md)