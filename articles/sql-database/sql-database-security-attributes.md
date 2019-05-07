---
title: Az Azure SQL Database biztonsági attribútumok
description: Attribútumainak kiértékelése az Azure SQL Database biztonsági ellenőrzőlista
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204241"
---
# <a name="security-attributes-for-azure-sql-database"></a>Az Azure SQL Database biztonsági attribútumok

Ez a cikk a gyakori biztonsági attribútumok az Azure SQL Database-be épített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Az Azure SQL Database szolgáltatás is [önálló adatbázis](sql-database-single-index.yml) és [felügyelt példány](sql-database-managed-instance.md). Az alábbi bejegyzések alkalmazni mindkét ajánlatait, kivéve, ha másként.

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | A továbbiakban "titkosítási-használatban", a cikkben leírtak szerint [Always Encrypted](sql-database-always-encrypted.md). Szolgáltatásoldali titkosítás [transzparens adattitkosítás](transparent-data-encryption-azure-sql.md) (TDE).|
| Titkosítás az átvitel során:<ul><li>Az ExpressRoute-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | HTTPS-en keresztül. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Szolgáltatás által kezelt, mind az ügyfél által felügyelt kulcs kezelése érhetők el (az utóbbi keresztül [Azure Key Vault](../key-vault/index.yml). |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Igen | Keresztül [Always Encrypted](sql-database-always-encrypted.md). |
| Titkosított API-hívások| Igen | HTTPS-/ SSL segítségével. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatásvégpont-támogatás| Igen | Érvényes [önálló adatbázis](sql-database-single-index.yml) csak. |
| vNET-injektálási támogatás| Igen | Érvényes [felügyelt példány](sql-database-managed-instance.md) csak. |
| Hálózatelkülönítés / tűzfalas támogatása| Igen | A tűzfal, mindkét adatbázis - és kiszolgálói szintű; a hálózatelkülönítés [felügyelt példány](sql-database-managed-instance.md) csak |
| Kényszerített bújtatás támogatása | Igen | [felügyelt példány](sql-database-managed-instance.md) keresztül [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A külső SIEM megoldások az Impervától (SecureSphere) is támogatják, keresztül [Azure Event Hubs](../event-hubs/index.yml) integration via [SQL audit](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hozzáférés-kezelés – hitelesítés| Igen | Azure Active Directory. |
| Hozzáférés-kezelés – engedélyezés| Igen |  |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti naplózás és a naplózási tervezése| Igen | Igen, csak bizonyos események. |
| Adatok naplózása és naplózási adatsík | Igen | Keresztül [SQL audit](sql-database-auditing.md). |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Nem  | | 

## <a name="additional-security-attributes-for-sql-database"></a>További biztonsági attribútumok SQL Database-hez

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: a biztonságirés-értékelési | Igen | Lásd: [SQL-sebezhetőségi felmérés szolgáltatás segít azonosítani az adatbázis biztonsági rések](sql-vulnerability-assessment.md). |
| Megelőző: az adatfelderítés és besorolás  | Igen | Lásd: [Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md). |
| Észlelés: fenyegetések észlelése | Igen | Lásd: [komplex veszélyforrások elleni védelem az Azure SQL Database](sql-database-threat-detection-overview.md). |
