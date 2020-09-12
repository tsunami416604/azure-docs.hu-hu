---
title: Biztonsági vezérlők
description: A Azure SQL Database értékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442088"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>A Azure SQL Database és az SQL felügyelt példányának biztonsági vezérlői
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk a Azure SQL Database és az Azure SQL felügyelt példányaiba beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Yes | Csak [SQL Databasere](../index.yml) vonatkozik. |
| Azure Virtual Network injekciós támogatás| Yes | Csak az [SQL felügyelt példányára](../managed-instance/sql-managed-instance-paas-overview.md) vonatkozik. |
| Hálózati elkülönítés és tűzfal-támogatás| Yes | Tűzfal az adatbázis szintjén és a kiszolgáló szintjén is. A hálózat elkülönítése csak az [SQL felügyelt példányai](../managed-instance/sql-managed-instance-paas-overview.md) esetében használható. |
| Kényszerített bújtatás támogatása| Yes | [SQL felügyelt példány](../managed-instance/sql-managed-instance-paas-overview.md) egy [ExpressRoute](../expressroute/../index.yml) VPN-en keresztül. |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás, például Log Analytics vagy Application Insights| Yes | A SecureSphere, a inperverztől származó SIEM-megoldás az [Azure Event Hubs](../event-hubs/../index.yml) -integráción keresztül is támogatott az [SQL-naplózással](../../azure-sql/database/auditing-overview.md). |
| Vezérlés – sík és felügyelet – a sík naplózása és naplózása| Yes | Igen, csak néhány eseménynél |
| Adatsíkok naplózása és naplózása | Yes | [SQL auditon](../../azure-sql/database/auditing-overview.md) keresztül |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Yes | Azure Active Directory (Azure AD) |
| Engedélyezés| Yes | Nincs |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | A "titkosítás használaton kívül" kifejezést a [Always encrypted](always-encrypted-certificate-store-configure.md)című cikkben leírtak szerint kell elnevezni. A kiszolgálóoldali titkosítás [transzparens adattitkosítást](transparent-data-encryption-tde-overview.md)használ.|
| Titkosítás az átvitel során:<ul><li>Azure ExpressRoute-titkosítás</li><li>Titkosítás egy virtuális hálózaton</li><li>Titkosítás a virtuális hálózatok között</ul>| Yes | HTTPS használatával. |
| Titkosítás – kulcsok kezelését, például CMK vagy BYOK| Yes | A szolgáltatással felügyelt és az ügyfél által felügyelt kulcsok kezelése egyaránt elérhető. Az utóbbit [Azure Key Vaulton](../key-vault/../index.yml)keresztül kínáljuk. |
| Az Azure-adatszolgáltatások által biztosított oszlop szintű titkosítás| Yes | [Always Encryptedon](always-encrypted-certificate-store-configure.md)keresztül. |
| Titkosított API-hívások| Yes | HTTPS/TLS használatával. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Konfiguráció-felügyeleti támogatás, például a konfiguráció verziószámozása| No  | Nincs |

## <a name="additional-security-controls-for-sql-database"></a>További biztonsági vezérlők SQL Database

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Megelőző: sebezhetőségi felmérés | Yes | Lásd az [SQL sebezhetőség-felmérési szolgáltatás segítséget nyújt az adatbázis-biztonsági rések azonosításához](sql-vulnerability-assessment.md). |
| Megelőző: adatfelderítés és besorolás  | Yes | Lásd: [Azure SQL Database és az Azure szinapszis Analytics adatfelderítési & besorolása](data-discovery-and-classification-overview.md). |
| Észlelés: fenyegetések észlelése | Yes | Lásd: [a Azure SQL Database összetett veszélyforrások elleni védelme](threat-detection-overview.md). |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).
