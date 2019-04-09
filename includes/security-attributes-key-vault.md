---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 0a52b7a5234b292fa3f8c3e19fe7f62f989d639c
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007242"
---
## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Összes objektum titkosított. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Minden kommunikáció titkosított API-hívásokon keresztül van |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Ügyfél szabályozza az összes kulcsok a Key vaultban. Ha hardveres biztonsági modul (HSM) a biztonsági kulcsok meg van adva, a FIPS-szint 2 HSM védi a kulcsot, a tanúsítvány vagy a titkos kulcs. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | HTTPS-en keresztül. |

## <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatásvégpont-támogatás| Igen | Virtuális hálózat (Vnet) Szolgáltatásvégpontok használatával. |
| vNET-injektálási támogatás| Nem |  |
| Hálózatelkülönítés / tűzfalas támogatása| Igen | Virtuális hálózatok közötti tűzfalszabályok használatával. |
| Kényszerített bújtatás támogatása | Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A Log Analytics használatával. |

## <a name="iam-support"></a>IAM-támogatás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hozzáférés-kezelés – hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Hozzáférés-kezelés – engedélyezés| Igen | Kulcstartó hozzáférési házirendjének használatával. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti naplózás és a naplózási adatsík| Igen | A Log Analytics használatával. |
| Adatok naplózása és naplózási adatsík| Igen | A Log Analytics használatával. |

## <a name="access-controls"></a>Hozzáférés-szabályozás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti sík hozzáférés-vezérlés | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík hozzáférés-vezérlést (szinten minden szolgáltatás) | Igen | Kulcstartó-hozzáférési házirend |
