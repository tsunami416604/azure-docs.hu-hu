---
title: Gyakori biztonsági attribútumok az Azure Backuphoz
description: Az Azure Backup értékelésére közös biztonsági attribútumok ellenőrzőlista
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679679"
---
# <a name="common-security-attributes-for-azure-backup"></a>Gyakori biztonsági attribútumok az Azure Backuphoz

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk a gyakori biztonsági attribútumok az Azure Backup beépített dokumentumok. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | A storage service encryption segítségével a storage-fiókok. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Nem | HTTPS-en keresztül. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Nem |  |
| Titkosított API-hívások| Igen |  |

## <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatásvégpont-támogatás| Nem |  |
| vNET-injektálási támogatás| Nem |  |
| Hálózatelkülönítés / tűzfalas támogatása| Igen | Kényszerített bújtatás támogatott virtuális gépek biztonsági mentését. Kényszerített bújtatás virtuális gépeken belül futó munkaterhelések esetében nem támogatott. |
| Kényszerített bújtatás támogatása | Nem |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A log Analytics diagnosztikai naplóinak keresztül támogatott. Lásd: [figyelése az Azure Backup-alapú számítási feladatokat a Log Analytics által védett](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) további információt. |

## <a name="iam-support"></a>IAM-támogatás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hozzáférés-kezelés – hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Hozzáférés-kezelés – engedélyezés| Igen | Létrehozott ügyfél és a beépített RBAC-szerepkör használja. Lásd: [Use Role-Based hozzáférés-vezérlés kezelése az Azure Backup helyreállítási pontok](/azure/backup/backup-rbac-rs-vault) további információt. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti naplózás és a naplózási tervezése| Igen | Tevékenységnaplók az Azure Portalról az összes aktivált ügyfelek műveletek naplózása. |
| Adatok naplózása és naplózási adatsík| Nem | Az Azure Backup az adatsík nem közvetlenül érhető el.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen|  |