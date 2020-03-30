---
title: Biztonsági vezérlők
description: Az Azure SQL Database kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190696"
---
# <a name="security-controls-for-azure-sql-database"></a>Az Azure SQL Database biztonsági vezérlői

Ez a cikk az Azure SQL Database beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

Az SQL Database [egyetlen adatbázist](sql-database-single-index.yml) és [felügyelt példányt](sql-database-managed-instance.md)is tartalmaz. Eltérő rendelkezés hiányában a következő bejegyzések mindkét ajánlatra vonatkoznak.

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| Igen | Csak [egyetlen adatbázisra](sql-database-single-index.yml) vonatkozik. |
| Az Azure virtuális hálózat befecskendezéstámogatása| Igen | Csak a [felügyelt példányra](sql-database-managed-instance.md) vonatkozik. |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | Tűzfal adatbázis- és kiszolgálószinten egyaránt. A hálózati elkülönítés csak [felügyelt példányra](sql-database-managed-instance.md) van. |
| Kényszerített bújtatástámogatása| Igen | [Felügyelt példány](sql-database-managed-instance.md) [expressroute-vpn-en](../expressroute/index.yml) keresztül. |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Az Azure figyelési támogatása, például a Log Analytics vagy az Application Insights| Igen | A SecureSphere, az Imperva SIEM-megoldása az [Azure Event Hubs-integráció](../event-hubs/index.yml) az [SQL-naplózáson](sql-database-auditing.md)keresztül is támogatott. |
| Vezérlő-sík és a menedzsment-sík naplózása és naplózása| Igen | Igen csak bizonyos eseményekesetén |
| Adatsík naplózása és naplózása | Igen | [SQL-ellenőrzésen keresztül](sql-database-auditing.md) |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Azure Active Directory (Azure AD) |
| Engedélyezés| Igen | None |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | Az úgynevezett "titkosítás-in-use" a cikkben leírt [Mindig titkosított](sql-database-always-encrypted.md). A kiszolgálóoldali titkosítás [transzparens adattitkosítást](transparent-data-encryption-azure-sql.md)használ.|
| Titkosítás átvitel közben:<ul><li>Azure ExpressRoute titkosítás</li><li>Titkosítás virtuális hálózatban</li><li>Titkosítás a virtuális hálózatok között</ul>| Igen | HTTPS használatával. |
| Titkosítási kulcs kezelése, például CMK vagy BYOK| Igen | A szolgáltatás által felügyelt és az ügyfél által felügyelt kulcskezelés is elérhető. Ez utóbbi az [Azure Key Vault](../key-vault/index.yml)on keresztül érhető el. |
| Az Azure adatszolgáltatásai által biztosított oszlopszintű titkosítás| Igen | Keresztül [mindig titkosított](sql-database-always-encrypted.md). |
| Titkosított API-hívások| Igen | HTTPS/TLS használatával. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása, például a konfiguráció verziószámozása| Nem  | None |

## <a name="additional-security-controls-for-sql-database"></a>Az SQL Database további biztonsági vezérlői

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: sebezhetőség értékelése | Igen | Lásd: [Az SQL biztonsági résfelmérési szolgáltatása segít az adatbázis biztonsági rései azonosításában.](sql-vulnerability-assessment.md) |
| Megelőző: adatfelderítés és -besorolás  | Igen | Lásd: [Azure SQL Database és SQL Data Warehouse adatfelderítési & besorolás.](sql-database-data-discovery-and-classification.md) |
| Észlelés: fenyegetésészlelése | Igen | Lásd: [Komplex veszélyforrások elleni védelem az Azure SQL Database-hez.](sql-database-threat-detection-overview.md) |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
