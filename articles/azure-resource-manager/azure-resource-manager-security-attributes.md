---
title: Gyakori biztonsági attribútumok az Azure Resource Manager
description: Egy Azure Resource Manager kiértékeléséhez közös biztonsági attribútumok ellenőrzőlista
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002260"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Az Azure Resource Manager biztonsági attribútumok

Ez a cikk az Azure Resource Manager beépített biztonsági attribútumok dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen |  |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | HTTPS/TLS. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| – | Az Azure Resource Manager nincs ügyfél tartalom, csak a vezérlő adatokat tárolja. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Igen | |
| Titkosított API-hívások| Igen | |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem | |
| VNet-injektálási támogatás| Igen | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Nem |  |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Nem | |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Az Azure Active Directory](/azure/active-directory) alapján.|
| Engedélyezés| Igen | |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Tevékenységnaplók elérhetővé tétele minden írási az erőforrások; a végrehajtott műveletek (PUT, POST, DELETE) Lásd: [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](resource-group-audit.md). |
| Adatsík naplózása és naplózása| – | |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen |  |
