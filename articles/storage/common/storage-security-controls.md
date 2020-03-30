---
title: Biztonsági vezérlők az Azure Storage-hoz
description: Az Azure Storage kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061113"
---
# <a name="security-controls-for-azure-storage"></a>Biztonsági vezérlők az Azure Storage-hoz

Ez a cikk az Azure Storage beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen |  |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Lásd: [Tárolási szolgáltatás titkosítása az ügyfél által felügyelt kulcsok használatával az Azure Key Vaultban.](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
| Oszlopszintű titkosítás (Azure Data Services)| N/A |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | Támogatja a szabványos HTTPS/TLS mechanizmusokat.  A felhasználók titkosíthatják az adatokat is, mielőtt azokat továbbítanák a szolgáltatásnak. |
| TITKOSÍTOTT API-hívások| Igen |  |

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| Igen |  |
| A VNet injekciózás támogatása| N/A |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | |
| Kényszerített bújtatástámogatása| N/A |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Az Azure Monitor metrikák|
| Vezérlő és felügyeleti sík naplózása és naplózása | Igen | Az Azure Resource Manager tevékenységnaplója |
| Adatsík naplózása és naplózása| Igen | Szolgáltatásdiagnosztikai naplók.|

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Azure Active Directory, Megosztott kulcs, Megosztott hozzáférési jogkivonat. |
| Engedélyezés| Igen | Támogatási engedélyezés RBAC, POSIX ACL-ek és SAS-tokenek en keresztül |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | Az Erőforrás-szolgáltató verziószámozásának támogatása az Azure Resource Manager API-kon keresztül |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../../security/fundamentals/security-controls.md)