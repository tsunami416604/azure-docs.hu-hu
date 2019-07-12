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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798688"
---
# <a name="security-attributes-for-azure-sql-database"></a>Az Azure SQL Database biztonsági attribútumok

Ez a cikk a gyakori biztonsági attribútumok az Azure SQL Database-be épített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Az SQL Database szolgáltatás egyaránt [önálló adatbázis](sql-database-single-index.yml) és [felügyelt példány](sql-database-managed-instance.md). A következő bejegyzések mindkét ajánlatok, kivéve, ha másként vonatkoznak.

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók, például az ügyfél oldalán vagy az Always Encrypted</ul>| Igen | "Titkosítási-használatban lévő," nevű, a cikkben leírtak szerint [Always Encrypted](sql-database-always-encrypted.md). Kiszolgálóoldali titkosítás [transzparens adattitkosítás](transparent-data-encryption-azure-sql.md).|
| Titkosítás az átvitel során:<ul><li>Azure ExpressRoute encryption</li><li>Titkosítás a virtuális hálózaton</li><li>Virtuális hálózatok közötti titkosítás</ul>| Igen | HTTPS-en keresztül. |
| Titkosítási kulcs, például a CMK vagy BYOK kezelése| Igen | Szolgáltatás által kezelt, mind az ügyfél által felügyelt kulcs kezelése érhető el. Keresztül felajánlott [Azure Key Vault](../key-vault/index.yml). |
| Az Azure data services által kínált oszlopszintű titkosítást| Igen | Keresztül [Always Encrypted](sql-database-always-encrypted.md). |
| Titkosított API-hívások| Igen | HTTPS-/ SSL segítségével. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Érvényes [önálló adatbázis](sql-database-single-index.yml) csak. |
| Az Azure Virtual Network-injektálási támogatás| Igen | Érvényes [felügyelt példány](sql-database-managed-instance.md) csak. |
| Hálózatelkülönítés és tűzfaltámogatás| Igen | Adatbázisszintű és kiszolgálószintű tűzfal. Hálózatelkülönítés szól [felügyelt példány](sql-database-managed-instance.md) csak. |
| Kényszerített bújtatás támogatása| Igen | [Felügyelt példány](sql-database-managed-instance.md) keresztül egy [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása, például a Log Analytics vagy az Application Insights| Igen | Keresztül is támogatja a SecureSphere, a SIEM-megoldások az Impervától, [Azure Event Hubs](../event-hubs/index.yml) integration via [SQL-naplózás](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Azure Active Directory (Azure AD) |
| Authorization| Igen | Nincsenek |

## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlősík és a felügyeleti sík naplózása és naplózása| Igen | Igen, csak bizonyos események |
| Adatsík naplózás és naplózása | Igen | Keresztül [SQL-naplózás](sql-database-auditing.md) |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Kezelési támogatás, például a konfigurációs verziókezelését| Nem  | None |

## <a name="additional-security-attributes-for-sql-database"></a>További biztonsági attribútumok SQL Database-hez

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: a biztonságirés-értékelési | Igen | Lásd: [SQL-sebezhetőségi felmérés szolgáltatás segít azonosítani az adatbázis biztonsági rések](sql-vulnerability-assessment.md). |
| Megelőző: az adatfelderítés és besorolás  | Igen | Lásd: [Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md). |
| Észlelés: fenyegetések észlelése | Igen | Lásd: [komplex veszélyforrások elleni védelem az Azure SQL Database](sql-database-threat-detection-overview.md). |
