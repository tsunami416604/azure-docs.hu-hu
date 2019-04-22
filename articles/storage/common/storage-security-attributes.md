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
ms.openlocfilehash: 33765d57f1a0e5788011b2d9d9c2f57d06713ddb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680689"
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