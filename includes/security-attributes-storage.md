---
author: msmbaldwin
ms.service: storage
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: b242bda524c747b28453061c797afde02cf6f455
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007562"
---
## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen |  |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Standard szintű HTTPS/TLS mechanizmusokat támogatja.  Felhasználók is titkosíthatja adatokat, mielőtt azt a szolgáltatás. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](../articles/storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen |  |

## <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatásvégpont-támogatás| Igen |  |
| vNET-injektálási támogatás| – |  |
| Hálózatelkülönítés / tűzfalas támogatása| Igen | |
| Kényszerített bújtatás támogatása | – |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az Azure Monitor metrikák elérhető, naplózza a kezdeti előzetes verzió |

## <a name="iam-support"></a>IAM-támogatás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hozzáférés-kezelés – hitelesítés| Igen | Az Azure Active Directory, a megosztott kulcsot, a megosztott hozzáférési jogkivonatot. |
| Hozzáférés-kezelés – engedélyezés| Igen | Engedélyezés a következővel RBAC, a POSIX ACL-EK és a SAS-tokeneket támogatja |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti naplózás és a naplózási tervezése | Igen | Az Azure Resource Manager-tevékenységnapló |
| Adatok naplózása és naplózási adatsík| Igen | Diagnosztikai naplók szolgáltatás és az Azure Monitor Naplózás kezdési előzetes verzió  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Támogatja az erőforrás-szolgáltató versioning Azure Resource Manager API-kon keresztül |