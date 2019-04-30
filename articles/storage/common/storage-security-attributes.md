---
title: Az Azure Storage közös biztonsági attribútumok
description: A gyakori biztonsági attribútumok értékeléséhez az Azure Storage ellenőrzőlista
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 8fa43998d0b10eddacdc9e0dd512295559814255
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101956"
---
# <a name="common-security-attributes-for-azure-storage"></a>Az Azure Storage közös biztonsági attribútumok

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk a gyakori biztonsági attribútumok az Azure Storage-bA épített dokumentumok. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen |  |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Standard szintű HTTPS/TLS mechanizmusokat támogatja.  Felhasználók is titkosíthatja adatokat, mielőtt azt a szolgáltatás. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen |  |

## <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen |  |
| vNET-injektálási támogatás| – |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | |
| Kényszerített bújtatás támogatása| – |  |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az Azure Monitor metrikák elérhető, naplózza a kezdeti előzetes verzió |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Az Azure Active Directory, a megosztott kulcsot, a megosztott hozzáférési jogkivonatot. |
| Engedélyezés| Igen | Engedélyezés a következővel RBAC, a POSIX ACL-EK és a SAS-tokeneket támogatja |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása | Igen | Az Azure Resource Manager-tevékenységnapló |
| Adatsík naplózása és naplózása| Igen | Diagnosztikai naplók szolgáltatás és az Azure Monitor Naplózás kezdési előzetes verzió  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Támogatja az erőforrás-szolgáltató versioning Azure Resource Manager API-kon keresztül |