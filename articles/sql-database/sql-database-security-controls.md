---
title: Biztonsági vezérlők
description: A Azure SQL Database értékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190696"
---
# <a name="security-controls-for-azure-sql-database"></a>A Azure SQL Database biztonsági vezérlői

Ez a cikk a Azure SQL Database beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database magában foglalja az [önálló adatbázist](sql-database-single-index.yml) és a [felügyelt példányt](sql-database-managed-instance.md)is. A következő bejegyzések mindkét ajánlatra érvényesek, kivéve ha ez másként nincs jelezve.

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Csak az [önálló adatbázisra](sql-database-single-index.yml) vonatkozik. |
| Azure Virtual Network injekciós támogatás| Igen | Csak a [felügyelt példányra](sql-database-managed-instance.md) vonatkozik. |
| Hálózati elkülönítés és tűzfal-támogatás| Igen | Tűzfal az adatbázis szintjén és a kiszolgáló szintjén is. A hálózati elkülönítés csak [felügyelt példány](sql-database-managed-instance.md) esetén használható. |
| Kényszerített bújtatás támogatása| Igen | [Felügyelt példány](sql-database-managed-instance.md) egy [ExpressRoute](../expressroute/index.yml) VPN-en keresztül. |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás, például Log Analytics vagy Application Insights| Igen | A SecureSphere, a inperverztől származó SIEM-megoldás az [Azure Event Hubs](../event-hubs/index.yml) -integráción keresztül is támogatott az [SQL-naplózással](sql-database-auditing.md). |
| Vezérlés – sík és felügyelet – a sík naplózása és naplózása| Igen | Igen, csak néhány eseménynél |
| Adatsíkok naplózása és naplózása | Igen | [SQL auditon](sql-database-auditing.md) keresztül |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Azure Active Directory (Azure AD) |
| Engedélyezés| Igen | None |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A "titkosítás használaton kívül" kifejezést a [Always encrypted](sql-database-always-encrypted.md)című cikkben leírtak szerint kell elnevezni. A kiszolgálóoldali titkosítás [transzparens adattitkosítást](transparent-data-encryption-azure-sql.md)használ.|
| Titkosítás az átvitel során:<ul><li>Azure ExpressRoute-titkosítás</li><li>Titkosítás egy virtuális hálózaton</li><li>Titkosítás a virtuális hálózatok között</ul>| Igen | HTTPS használatával. |
| Titkosítás – kulcsok kezelését, például CMK vagy BYOK| Igen | A szolgáltatással felügyelt és az ügyfél által felügyelt kulcsok kezelése egyaránt elérhető. Az utóbbit [Azure Key Vaulton](../key-vault/index.yml)keresztül kínáljuk. |
| Az Azure-adatszolgáltatások által biztosított oszlop szintű titkosítás| Igen | [Always Encryptedon](sql-database-always-encrypted.md)keresztül. |
| Titkosított API-hívások| Igen | HTTPS/TLS használatával. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció-felügyeleti támogatás, például a konfiguráció verziószámozása| Nem  | None |

## <a name="additional-security-controls-for-sql-database"></a>További biztonsági vezérlők SQL Database

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: sebezhetőségi felmérés | Igen | Lásd az [SQL sebezhetőség-felmérési szolgáltatás segítséget nyújt az adatbázis-biztonsági rések azonosításához](sql-vulnerability-assessment.md). |
| Megelőző: adatfelderítés és besorolás  | Igen | Lásd: [Azure SQL Database és SQL Data Warehouse az adatfelderítés & besorolása](sql-database-data-discovery-and-classification.md). |
| Észlelés: fenyegetések észlelése | Igen | Lásd: [a Azure SQL Database összetett veszélyforrások elleni védelme](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
