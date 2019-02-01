---
author: msmbaldwin
ms.service: backup
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 5a4cc3ea822c5613fc7a50b8e370d6846b683721
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513511"
---
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
| Azure monitorozási támogatása (a Log analytics, az App insights stb.)| Igen | A log Analytics diagnosztikai naplóinak keresztül támogatott. Tekintse meg a figyelő az Azure Backup-alapú számítási feladatokat a Log Analytics által védett (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) további információt. |

## <a name="iam-support"></a>IAM-támogatás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hozzáférés-kezelés – hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Hozzáférés-kezelés – engedélyezés| Igen | Létrehozott ügyfél és a beépített RBAC-szerepkör használja. Tekintse meg a Use Role-Based hozzáférés-vezérlés kezelése az Azure Backup helyreállítási pontok (https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault) további információt. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti naplózás és a naplózási tervezése| Igen | Tevékenységnaplók az Azure Portalról az összes aktivált ügyfelek műveletek naplózása. |
| Adatok naplózása és naplózási adatsík| Nem | Az Azure Backup az adatsík nem közvetlenül érhető el.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb)| Igen|  |