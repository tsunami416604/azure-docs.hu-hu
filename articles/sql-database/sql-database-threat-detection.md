---
title: Fenyegetések észlelése – Azure SQL Database |} A Microsoft Docs
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
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793772"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Az Azure SQL Database fenyegetésészlelési önálló vagy készletezett adatbázisok

[Veszélyforrások detektálása](sql-database-threat-detection-overview.md) egyetlen vagy készletezett adatbázisok észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek. A fenyegetésészlelés azonosíthatja **lehetséges SQL-injektálás**, **hozzáférés szokatlan helyről vagy a data Center**, **ismeretlen egyszerű vagy potenciálisan káros alkalmazástvalóhozzáférés**, és **találgatásos SQL hitelesítő adatok** – további részletek az [fenyegetés-észlelési riasztás](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Is fogadhatja az értesítéseket keresztül észlelt fenyegetésről [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy [Azure Portalon](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Veszélyforrások detektálása](sql-database-threat-detection-overview.md) része a [adatbiztonság speciális](sql-database-advanced-data-security.md) (ADS) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A fenyegetésészlelés érheti el és a központi SQL ADS portálon keresztül kezelhetők. A speciális biztonsági csomag díjának 15 USD/hó / logikai kiszolgáló, az első 30 napban az ingyenesen elérhető.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Állítsa be az Azure Portalon az adatbázishoz tartozó fenyegetések észlelése

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a konfigurációs lapon, a védeni kívánt Azure SQL Database-kiszolgáló. Válassza ki a biztonsági beállítások **adatok biztonságú**.
3. Az a **adatok biztonságú** konfigurációs lapon:

   - Lehetővé teszi a speciális biztonsági a kiszolgálón.
   - A **Fenyegetésészlelési beállításokat**, a a **küldje a riasztásokat** szöveget adja meg a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld e-mailek listáját.
  
   ![Állítsa be a fenyegetések észlelése](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Állítsa be a PowerShell-lel fenyegetésészlelés

A parancsfájl példa: [PowerShell-lel, naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [fenyegetésészlelés](sql-database-threat-detection-overview.md).
- Tudjon meg többet [fenyegetések észlelése a felügyelt példány](sql-database-managed-instance-threat-detection.md).  
- Tudjon meg többet [adatbiztonság speciális](sql-database-advanced-data-security.md).
- Tudjon meg többet [naplózása](sql-database-auditing.md)
- Tudjon meg többet [az Azure security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Díjszabási információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/)  
