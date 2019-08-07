---
title: Komplex veszélyforrások elleni védelem – Azure SQL Database | Microsoft Docs
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel, amelyekkel az adatbázis potenciális biztonsági fenyegetéseket jelenthet egyetlen adatbázisban vagy rugalmas készletben.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 755a3b391cb7b4909169b034cc8d89892ec2ed05
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816548"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database komplex veszélyforrások elleni védelem egyetlen vagy készletezett adatbázisokhoz

Az önálló és a készletezett adatbázisok komplex [veszélyforrások elleni védelme](sql-database-threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A komplex veszélyforrások elleni védelem képes azonosítani a **lehetséges SQL**-injektálásokat, **a szokatlan helyekről vagy**adatközpontokból való hozzáférést, **az ismeretlen elsődleges vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatok** megtekintését. További információ a komplex [veszélyforrások elleni védelem riasztásokról](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy a [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) használatával

A komplex [veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) a [fejlett adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. A komplex veszélyforrások elleni védelem a központi SQL ADS portálon keresztül érhető el és kezelhető.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Az összetett veszélyforrások elleni védelem beállítása a Azure Portal

1. Indítsa el a Azure Portal [https://portal.azure.com](https://portal.azure.com)a következő helyen:.
2. Navigáljon a védelemmel ellátni kívánt Azure SQL Database-kiszolgáló konfigurációs lapjára. A biztonsági beállítások területen válassza a **speciális adatbiztonság**lehetőséget.
3. A **speciális adatbiztonság** beállítása lapon:

   - Engedélyezze a speciális adatbiztonságot a kiszolgálón.
   - Az **összetett veszélyforrások elleni védelem beállításaiban**a **riasztások küldése** a szövegmezőbe mezőbe írja be azoknak az e-maileknek a listáját, amelyek biztonsági riasztásokat kapnak a rendellenes adatbázis-tevékenységek észlelése után.
  
   ![Komplex veszélyforrások elleni védelem beállítása](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > A képernyőképek árai nem mindig tükrözik az aktuális árat, és példa erre.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Komplex veszélyforrások elleni védelem beállítása a PowerShell használatával

A parancsfájlokra példa: a [naplózás és az összetett veszélyforrások elleni védelem konfigurálása a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>További lépések

- További információ a komplex [veszélyforrások elleni védelemről](sql-database-threat-detection-overview.md).
- További információ a [felügyelt példányok komplex veszélyforrások elleni védelméről](sql-database-managed-instance-threat-detection.md).  
- További információ a [speciális](sql-database-advanced-data-security.md)adatbiztonságról.
- További információ a [naplózásról](sql-database-auditing.md)
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
- A díjszabással kapcsolatos további információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) .  
