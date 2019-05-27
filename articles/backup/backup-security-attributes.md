---
title: Gyakori biztonsági attribútumok az Azure Backuphoz
description: Az Azure Backup értékelésére közös biztonsági attribútumok ellenőrzőlista
services: backup
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 86b8ab96a94a6ffc44c304d8a0a689301560a989
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002803"
---
# <a name="security-attributes-for-azure-backup"></a>Az Azure Backup biztonsági attribútumok

Ez a cikk az Azure Backup beépített biztonsági attribútumok dokumentumok. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | A storage service encryption segítségével a storage-fiókok. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Nem | HTTPS-en keresztül. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Nem |  |
| Titkosított API-hívások| Igen |  |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem |  |
| VNet-injektálási támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Kényszerített bújtatás támogatott virtuális gépek biztonsági mentését. Kényszerített bújtatás virtuális gépeken belül futó munkaterhelések esetében nem támogatott. |
| Kényszerített bújtatás támogatása| Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A log Analytics diagnosztikai naplóinak keresztül támogatott. Lásd: [figyelése az Azure Backup-alapú számítási feladatokat a Log Analytics által védett](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) további információt. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Létrehozott ügyfél és a beépített RBAC-szerepkör használja. Lásd: [Use Role-Based hozzáférés-vezérlés kezelése az Azure Backup helyreállítási pontok](/azure/backup/backup-rbac-rs-vault) további információt. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Tevékenységnaplók az Azure Portalról az összes aktivált ügyfelek műveletek naplózása. |
| Adatsík naplózása és naplózása| Nem | Az Azure Backup az adatsík nem közvetlenül érhető el.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen|  |