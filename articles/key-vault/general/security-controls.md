---
title: Az Azure Key Vault biztonsági vezérlői
description: Az Azure Key Vault kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429862"
---
# <a name="security-controls-for-azure-key-vault"></a>Az Azure Key Vault biztonsági vezérlői

Ez a cikk az Azure Key Vaultba beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| Igen | Virtuális hálózat (VNet) szolgáltatásvégpontok használata. |
| A VNet injekciózás támogatása| Nem |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | A Virtuálishálózat tűzfalszabályainak használata. |
| Kényszerített bújtatástámogatása| Nem |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | A Log Analytics használatával. |
| Vezérlő/menedzsment sík naplózása és naplózása| Igen | A Log Analytics használatával. |
| Adatsík naplózása és naplózása| Igen | A Log Analytics használatával. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | A hitelesítés az Azure Active Directoryn keresztül történik. |
| Engedélyezés| Igen | A Key Vault hozzáférési házirend használata. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | Minden objektum titkosítva van. |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél vezérli a kulcstartóban lévő összes kulcsot. Ha hardveres biztonsági modul (HSM) biztonsági másolatot készíteni, a FIPS Level 2 HSM védi a kulcsot, a tanúsítványt vagy a titkos kulcsot. |
| Oszlopszintű titkosítás (Azure Data Services)| N/A |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | Minden kommunikáció titkosított API-hívásokon keresztül történik |
| TITKOSÍTOTT API-hívások| Igen | HTTPS használatával. |

## <a name="access-controls"></a>Hozzáférés-vezérlés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/kezelés sík hozzáférés-vezérlők | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík-hozzáférési vezérlők (minden szolgáltatási szinten) | Igen | A Key Vault hozzáférési irányelvei |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../../security/fundamentals/security-controls.md)