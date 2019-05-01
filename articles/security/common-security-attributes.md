---
title: Az Azure-szolgáltatások biztonsági attribútumok
description: Az Azure Service Fabric értékelésére közös biztonsági attribútumok ellenőrzőlista
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 94681351758f34cc53c425f3207660bdb09f6494
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717791"
---
# <a name="common-security-attributes-for-azure-services"></a>Gyakori biztonsági attribútumok az Azure-szolgáltatásokhoz

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk összegyűjti a kiválasztott Azure-szolgáltatások általános biztonsági attribútumait. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | A storage service encryption segítségével a storage-fiókok. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Nem | HTTPS-en keresztül. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Nem |  |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem |  |
| VNet-injektálási támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Kényszerített bújtatás támogatott virtuális gépek biztonsági mentését. Kényszerített bújtatás virtuális gépeken belül futó munkaterhelések esetében nem támogatott. |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A log Analytics diagnosztikai naplóinak keresztül támogatott. Tekintse meg a figyelő az Azure Backup-alapú számítási feladatokat a Log Analytics által védett (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) további információt. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Létrehozott ügyfél és a beépített RBAC-szerepkör használja. Tekintse meg a Use Role-Based hozzáférés-vezérlés kezelése az Azure Backup helyreállítási pontok (/ azure/biztonsági mentés/backup-rbac-rs-tároló) további információt. |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Tevékenységnaplók az Azure Portalról az összes aktivált ügyfelek műveletek naplózása. |
| Adatsík naplózása és naplózása| Nem | Az Azure Backup az adatsík nem közvetlenül érhető el.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Összes objektum titkosított. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Minden kommunikáció titkosított API-hívásokon keresztül van |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Ügyfél szabályozza az összes kulcsok a Key vaultban. Ha hardveres biztonsági modul (HSM) a biztonsági kulcsok meg van adva, a FIPS-szint 2 HSM védi a kulcsot, a tanúsítvány vagy a titkos kulcs. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | HTTPS-en keresztül. |

### <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Virtuális hálózat (VNet) Szolgáltatásvégpontok használatával. |
| VNet-injektálási támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Virtuális hálózatok közötti tűzfalszabályok használatával. |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A Log Analytics használatával. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Kulcstartó hozzáférési házirendjének használatával. |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti naplózás és a naplózási adatsík| Igen | A Log Analytics használatával. |
| Adatsík naplózása és naplózása| Igen | A Log Analytics használatával. |

### <a name="access-controls"></a>Hozzáférés-szabályozás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti sík hozzáférés-vezérlés | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík hozzáférés-vezérlést (szinten minden szolgáltatás) | Igen | Kulcstartó-hozzáférési házirend |

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Az ügyfél a tulajdonosa a fürt és a virtuális gép (VM) méretezési, a fürt épül. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen |  |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Az ügyfél a tulajdonosa a fürt és a virtuális gép (VM) méretezési, a fürt épül. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageren keresztül történik. Egy érvényes JSON webes jogkivonat (JWT) megadása kötelező. |

### <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen |  |
| VNet-injektálási támogatás| Igen |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Hálózati biztonsági csoportok (NSG) használatával. |
| Kényszerített bújtatás támogatása| Igen | Azure-hálózatok biztosítják a kényszerített bújtatás. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Figyelés és külső támogatást az Azure használatával. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Identitás és hozzáférés-kezelés (IAM) keresztül SFRP-hívásokhoz. Közvetlenül a fürt végpontja hívások két szerepkör támogatja: Felhasználó és rendszergazda. Az ügyfél leképezheti az API-k bármelyik szerepkörhöz. |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Az összes vezérlési síkjával végzett műveletek haladjon végig a naplózási és jóváhagyási folyamatok. |
| Adatsík naplózása és naplózása| – | Ügyfél a tulajdonosa a fürtöt.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | A szolgáltatás konfigurációs, verzióval ellátott és üzembe helyezett üzembe az Azure használatával. A kód (alkalmazás- és futásidejű) rendszerverzióval ellátott Azure Build használatával.
 |

## <a name="azure-storage"></a>Azure Storage

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen |  |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Standard szintű HTTPS/TLS mechanizmusokat támogatja.  Felhasználók is titkosíthatja adatokat, mielőtt azt a szolgáltatás. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen |  |
| VNet-injektálási támogatás| – |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | |
| Kényszerített bújtatás támogatása| – |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az Azure Monitor metrikák elérhető, naplózza a kezdeti előzetes verzió |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Az Azure Active Directory, a megosztott kulcsot, a megosztott hozzáférési jogkivonatot. |
| Engedélyezés| Igen | Engedélyezés a következővel RBAC, a POSIX ACL-EK és a SAS-tokeneket támogatja |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása | Igen | Az Azure Resource Manager-tevékenységnapló |
| Adatsík naplózása és naplózása| Igen | Diagnosztikai naplók szolgáltatás és az Azure Monitor Naplózás kezdési előzetes verzió  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Támogatja az erőforrás-szolgáltató versioning Azure Resource Manager API-kon keresztül |