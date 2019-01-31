---
title: Fenyegetések észlelése – az Azure SQL Database |} A Microsoft Docs
description: Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek jelzi az adatbázis egy önálló adatbázis vagy a rugalmas készletet az esetleges biztonsági fenyegetéseket.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 032146742c1a49fc8cdbda24d5c732add3cc5ea7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465350"
---
# <a name="azure-sql-database-threat-detection-for-standalone-or-pooled-database"></a>Az Azure SQL Database Threat Detection önálló vagy készletezett adatbázis

Az Azure SQL [Fenyegetésészlelés](sql-database-threat-detection-overview.md) önálló és a készletezett adatbázisok észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek. A Fenyegetésészlelés azonosíthatja **lehetséges SQL-injektálás**, **hozzáférés szokatlan helyről vagy a data Center**, **ismeretlen egyszerű vagy potenciálisan káros alkalmazástvalóhozzáférés**, és **találgatásos SQL hitelesítő adatok** – további részletek az [Fenyegetésészlelési riasztásokat](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Is fogadhatja az értesítéseket keresztül észlelt fenyegetésről [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy [Azure Portalon](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Veszélyforrások Detektálása](sql-database-threat-detection-overview.md) része a [SQL speciális adatbiztonság](sql-advanced-threat-protection.md) (ADS) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A Fenyegetésészlelés érheti el és a központi SQL ADS portálon keresztül kezelhetők. Az adatok biztonságú csomag díjának 15 USD/hó / logikai kiszolgáló, az első 30 napban az ingyenesen elérhető.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Állítsa be az Azure Portalon az adatbázishoz tartozó fenyegetések észlelése

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a konfigurációs lapon, a védeni kívánt Azure SQL Database-kiszolgáló. Válassza ki a biztonsági beállítások **adatok biztonságú**.
3. Az a **adatok biztonságú** konfigurációs lapon:

   - Adatok biztonságú engedélyezése a kiszolgálón.
   - A **Fenyegetésészlelési beállításokat**, a a **küldje a riasztásokat** szöveget adja meg a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld e-mailek listáját.
  
   ![Állítsa be a fenyegetések észlelése](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Állítsa be a PowerShell-lel fenyegetésészlelés

A parancsfájl példa: [PowerShell-lel, naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Fenyegetésészlelés](sql-database-threat-detection-overview.md).
- Tudjon meg többet [a felügyelt példány Fenyegetésészlelési](sql-database-managed-instance-threat-detection.md).  
- Tudjon meg többet [SQL biztonságú adatok](sql-advanced-threat-protection.md).
- Tudjon meg többet [Azure SQL Database naplózási szolgáltatásával](sql-database-auditing.md)
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Díjszabási információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/)  
