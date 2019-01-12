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
ms.date: 01/11/2019
ms.openlocfilehash: 330726eecc19659d978b1072ad02ad6d5a4ccb8b
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244375"
---
# <a name="azure-sql-database-threat-detection-for-single-database"></a>Az Azure SQL Database Threat Detection önálló adatbázis

Az Azure SQL [Fenyegetésészlelés](sql-database-threat-detection-overview.md) a [SQL Database](sql-database-technical-overview.md) önálló adatbázisok észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek. A Fenyegetésészlelés azonosíthatja **lehetséges SQL-injektálás**, **hozzáférés szokatlan helyről vagy a data Center**, **ismeretlen egyszerű vagy potenciálisan káros alkalmazástvalóhozzáférés**, és **találgatásos SQL hitelesítő adatok** – további részletek az [Fenyegetésészlelési riasztásokat](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Is fogadhatja az értesítéseket keresztül észlelt fenyegetésről [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy [Azure Portalon](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Veszélyforrások Detektálása](sql-database-threat-detection-overview.md) része a [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md) (ATP) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A Fenyegetésészlelés érheti el és a központi SQL ATP portálján felügyelt. Fenyegetés-észlelési szolgáltatás díjának 15 USD/hónap logikai kiszolgálónként 30 napig ingyenesen.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Állítsa be az Azure Portalon az adatbázishoz tartozó fenyegetések észlelése

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a konfigurációs lapon, a védeni kívánt Azure SQL Database-kiszolgáló. Válassza ki a biztonsági beállítások **komplex veszélyforrások elleni védelem**.
3. Az a **komplex veszélyforrások elleni védelem** konfigurációs lapon:

   - Komplex veszélyforrások elleni védelem engedélyezése a kiszolgálón.
   - A **Fenyegetésészlelési beállításokat**, a a **küldje a riasztásokat** szöveget adja meg a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld e-mailek listáját.
  
   ![Állítsa be a fenyegetések észlelése](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Állítsa be a PowerShell-lel fenyegetésészlelés

A parancsfájl példa: [PowerShell-lel, naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Fenyegetésészlelés](sql-database-threat-detection-overview.md).
- Tudjon meg többet [a felügyelt példány Fenyegetésészlelési](sql-database-managed-instance-threat-detection.md).  
- Tudjon meg többet [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md).
- Tudjon meg többet [Azure SQL Database naplózási szolgáltatásával](sql-database-auditing.md)
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Díjszabási információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/)  
