---
title: Az Azure Key Vault közös biztonsági attribútumok
description: A közös biztonsági attribútumok az Azure Key Vault értékelésére ellenőrzőlista
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000181"
---
# <a name="security-attributes-for-azure-key-vault"></a>Az Azure Key Vault biztonsági attribútumok

Ez a cikk az Azure Key Vault beépített biztonsági attribútumok dokumentumok. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Összes objektum titkosított. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Minden kommunikáció titkosított API-hívásokon keresztül van |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Az ügyfél összes kulcsok a Key vaultban szabályozza. Ha hardveres biztonsági modul (HSM) a biztonsági kulcsok meg van adva, a FIPS-szint 2 HSM védi a kulcsot, a tanúsítvány vagy a titkos kulcs. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | HTTPS-en keresztül. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Virtuális hálózat (VNet) Szolgáltatásvégpontok használatával. |
| VNet-injektálási támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Virtuális hálózatok közötti tűzfalszabályok használatával. |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A Log Analytics használatával. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Kulcstartó hozzáférési házirendjének használatával. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti naplózás és a naplózási adatsík| Igen | A Log Analytics használatával. |
| Adatsík naplózása és naplózása| Igen | A Log Analytics használatával. |

## <a name="access-controls"></a>Hozzáférés-szabályozás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti sík hozzáférés-vezérlés | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík hozzáférés-vezérlést (szinten minden szolgáltatás) | Igen | Kulcstartó-hozzáférési házirend |