---
title: Azure Key Vault biztonsági attribútumai
description: A Azure Key Vault értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444016"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure Key Vault biztonsági attribútumai

Ez a cikk a Azure Key Vault beépített biztonsági attribútumokat dokumentálja. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)| Igen | Minden objektum titkosítva van. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Minden kommunikáció titkosított API-hívásokon keresztül történik |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Az ügyfél a Key Vault összes kulcsát vezérli. Ha a hardveres biztonsági modul (HSM) által támogatott kulcsok meg vannak adva, akkor a 2. FIPS szintű HSM védi a kulcsot, a tanúsítványt vagy a titkos kulcsot. |
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | HTTPS használatával. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Virtual Network (VNet) szolgáltatási végpontok használata. |
| VNet-befecskendezés támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | VNet tűzfalszabályok használata. |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Log Analytics használata. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | Key Vault hozzáférési házirend használatával. |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési/felügyeleti síkok naplózása és naplózása| Igen | Log Analytics használata. |
| Adatsíkok naplózása és naplózása| Igen | Log Analytics használata. |

## <a name="access-controls"></a>Hozzáférés-szabályozás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti sík hozzáférés-vezérlése | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsíkok hozzáférés-vezérlése (minden szolgáltatási szinten) | Igen | Hozzáférési szabályzat Key Vault |