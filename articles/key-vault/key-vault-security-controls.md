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
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886582"
---
# <a name="security-controls-for-azure-key-vault"></a>A Azure Key Vault biztonsági vezérlői

Ez a cikk a Azure Key Vault beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Virtual Network (VNet) szolgáltatási végpontok használata. |
| VNet-befecskendezés támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | VNet tűzfalszabályok használata. |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Log Analytics használata. |
| Vezérlési/felügyeleti síkok naplózása és naplózása| Igen | Log Analytics használata. |
| Adatsíkok naplózása és naplózása| Igen | Log Analytics használata. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | Key Vault hozzáférési házirend használatával. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | Minden objektum titkosítva van. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél a Key Vault összes kulcsát vezérli. Ha a hardveres biztonsági modul (HSM) által támogatott kulcsok meg vannak adva, akkor a 2. FIPS szintű HSM védi a kulcsot, a tanúsítványt vagy a titkos kulcsot. |
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Minden kommunikáció titkosított API-hívásokon keresztül történik |
| Titkosított API-hívások| Igen | HTTPS használatával. |

## <a name="access-controls"></a>Hozzáférés-szabályozás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti sík hozzáférés-vezérlése | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsíkok hozzáférés-vezérlése (minden szolgáltatási szinten) | Igen | Hozzáférési szabályzat Key Vault |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).