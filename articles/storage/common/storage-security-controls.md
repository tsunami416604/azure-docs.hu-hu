---
title: Biztonsági vezérlők
titleSuffix: Azure Storage
description: 'Tekintse meg a Security Control ellenőrzőlistákat az Azure Storage kiértékeléséhez. Érintett területek: adatvédelem, hálózat, figyelés és naplózás, identitás és konfiguráció.'
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: b816e7bd5e00b21700bc994fc0860195d39f2915
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826517"
---
# <a name="security-controls-for-azure-storage"></a>Az Azure Storage biztonsági vezérlői

Ez a cikk az Azure Storage-ba beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Lásd: [Storage Service encryption az ügyfél által felügyelt kulcsokkal Azure Key Vaultban](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Oszlop szintű titkosítás (Azure Data Services)| N/A |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A szabványos HTTPS/TLS-mechanizmusok támogatása.  A felhasználók a szolgáltatásba való továbbítás előtt is titkosítani tudják az adatokat. |
| Titkosított API-hívások| Igen |  |

## <a name="network"></a>Hálózat

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| Szolgáltatás-címkék támogatása| Igen | Az Azure Storage által támogatott szolgáltatási címkékkel kapcsolatos további információkért lásd: az [Azure-szolgáltatások címkéi – áttekintés](../../virtual-network/service-tags-overview.md) . |
| VNet-befecskendezés támogatása| N/A |  |
| Hálózati elkülönítés és tűzfal-támogatás| Igen | |
| Kényszerített bújtatás támogatása| N/A |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Azure Monitor metrikák|
| Vezérlési és felügyeleti síkok naplózása és naplózása | Igen | Azure-tevékenységnapló |
| Adatsíkok naplózása és naplózása| Igen | Erőforrás-naplók Azure Monitor |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Azure Active Directory, megosztott kulcs, közös hozzáférési jogkivonat. |
| Engedélyezés| Igen | Támogatás engedélyezése RBAC, POSIX ACL-eken és SAS-tokeneken keresztül |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Azure Resource Manager API-k támogatása az erőforrás-szolgáltatónál |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).