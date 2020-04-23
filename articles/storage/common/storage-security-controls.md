---
title: Biztonsági vezérlők
titleSuffix: Azure Storage
description: Az Azure Storage kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája.
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082356"
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
| Szolgáltatáscímkék támogatása| Igen | Az Azure Storage által támogatott szolgáltatáscímkékkel kapcsolatos további információkért tekintse meg az [Azure-szolgáltatáscímkék áttekintését.](../../virtual-network/service-tags-overview.md) |
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