---
title: A Azure Key Vault biztonsági vezérlői
description: A Azure Key Vault értékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429862"
---
# <a name="security-controls-for-azure-key-vault"></a>A Azure Key Vault biztonsági vezérlői

Ez a cikk a Azure Key Vault beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Virtual Network (VNet) szolgáltatási végpontok használata. |
| VNet-befecskendezés támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | VNet tűzfalszabályok használata. |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Log Analytics használata. |
| Vezérlési/felügyeleti síkok naplózása és naplózása| Igen | Log Analytics használata. |
| Adatsíkok naplózása és naplózása| Igen | Log Analytics használata. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Engedélyezés| Igen | Key Vault hozzáférési házirend használatával. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | Minden objektum titkosítva van. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél a Key Vault összes kulcsát vezérli. Ha a hardveres biztonsági modul (HSM) által támogatott kulcsok meg vannak adva, akkor a 2. FIPS szintű HSM védi a kulcsot, a tanúsítványt vagy a titkos kulcsot. |
| Oszlop szintű titkosítás (Azure Data Services)| N.A. |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Minden kommunikáció titkosított API-hívásokon keresztül történik |
| Titkosított API-hívások| Igen | HTTPS használatával. |

## <a name="access-controls"></a>Hozzáférés-vezérlés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti sík hozzáférés-vezérlése | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsíkok hozzáférés-vezérlése (minden szolgáltatási szinten) | Igen | Hozzáférési szabályzat Key Vault |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).