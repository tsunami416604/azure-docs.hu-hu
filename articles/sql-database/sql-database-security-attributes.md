---
title: Azure SQL Database biztonsági attribútumai
description: A Azure SQL Database értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444392"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database biztonsági attribútumai

Ez a cikk a Azure SQL Database beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database magában foglalja az [önálló adatbázist](sql-database-single-index.yml) és a [felügyelt példányt](sql-database-managed-instance.md)is. A következő bejegyzések mindkét ajánlatra érvényesek, kivéve ha ez másként nincs jelezve.

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás nyugalmi állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsokkal</li><li>Egyéb titkosítási funkciók, például ügyféloldali vagy Always Encrypted</ul>| Igen | A "titkosítás használaton kívül" kifejezést a [Always encrypted](sql-database-always-encrypted.md)című cikkben leírtak szerint kell elnevezni. A kiszolgálóoldali titkosítás [transzparens](transparent-data-encryption-azure-sql.md)adattitkosítást használ.|
| Titkosítás az átvitel során:<ul><li>Azure ExpressRoute-titkosítás</li><li>Titkosítás egy virtuális hálózaton</li><li>Titkosítás a virtuális hálózatok között</ul>| Igen | HTTPS használatával. |
| Titkosítás – kulcsok kezelését, például CMK vagy BYOK| Igen | A szolgáltatással felügyelt és az ügyfél által felügyelt kulcsok kezelése egyaránt elérhető. Az utóbbit Azure Key Vaulton [](../key-vault/index.yml)keresztül kínáljuk. |
| Az Azure-adatszolgáltatások által biztosított oszlop szintű titkosítás| Igen | [Always Encryptedon](sql-database-always-encrypted.md)keresztül. |
| Titkosított API-hívások| Igen | HTTPS/SSL használatával. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Csak az [önálló adatbázisra](sql-database-single-index.yml) vonatkozik. |
| Azure Virtual Network injekciós támogatás| Igen | Csak a [felügyelt példányra](sql-database-managed-instance.md) vonatkozik. |
| Hálózati elkülönítés és tűzfal-támogatás| Igen | Tűzfal az adatbázis szintjén és a kiszolgáló szintjén is. A hálózati elkülönítés csak [felügyelt példány](sql-database-managed-instance.md) esetén használható. |
| Kényszerített bújtatás támogatása| Igen | [Felügyelt példány](sql-database-managed-instance.md) egy [ExpressRoute](../expressroute/index.yml) VPN-en keresztül. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás, például Log Analytics vagy Application Insights| Igen | A SecureSphere, a inperverztől származó SIEM-megoldás az [Azure Event Hubs](../event-hubs/index.yml) -integráción keresztül is támogatott az [SQL](sql-database-auditing.md)-naplózással. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Azure Active Directory (Azure AD) |
| Authorization| Igen | Nincsenek |

## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlés – sík és felügyelet – a sík naplózása és naplózása| Igen | Igen, csak néhány eseménynél |
| Adatsíkok naplózása és naplózása | Igen | [SQL auditon](sql-database-auditing.md) keresztül |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció-felügyeleti támogatás, például a konfiguráció verziószámozása| Nem  | None |

## <a name="additional-security-attributes-for-sql-database"></a>A SQL Database további biztonsági attribútumai

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: sebezhetőségi felmérés | Igen | Lásd az [SQL sebezhetőség-felmérési szolgáltatás segítséget nyújt az adatbázis-biztonsági rések azonosításához](sql-vulnerability-assessment.md). |
| Megelőző: adatfelderítés és besorolás  | Igen | Lásd: [Azure SQL Database és SQL Data Warehouse az adatfelderítés & besorolása](sql-database-data-discovery-and-classification.md). |
| Észlelés: fenyegetések észlelése | Igen | Lásd: [a Azure SQL Database összetett veszélyforrások elleni védelme](sql-database-threat-detection-overview.md). |
