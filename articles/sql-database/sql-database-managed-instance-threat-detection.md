---
title: Komplex veszélyforrások elleni védelem konfigurálása – Azure SQL Database felügyelt példány | Microsoft Docs
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel, amelyeken a felügyelt példányok adatbázisa potenciális biztonsági fenyegetéseket jelez.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: eb6d8229e85e54a6a3fc8591dc4688a73773fd8b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816583"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Az összetett veszélyforrások elleni védelem konfigurálása Azure SQL Database felügyelt példányban

A felügyelt példányok [](sql-database-managed-instance-index.yml) komplex [veszélyforrások elleni védelme](sql-database-threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A komplex veszélyforrások elleni védelem képes azonosítani a **lehetséges SQL**-injektálásokat, **a szokatlan helyekről vagy**adatközpontokból való hozzáférést, **az ismeretlen elsődleges vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatok** megtekintését. További információ a komplex [veszélyforrások elleni védelem riasztásokról](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy a [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) használatával

A komplex [veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) a [fejlett adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. A komplex veszélyforrások elleni védelem a központi SQL ADS portálon keresztül érhető el és kezelhető.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Az összetett veszélyforrások elleni védelem beállítása a Azure Portal

1. Indítsa el a Azure Portal [https://portal.azure.com](https://portal.azure.com)a következő helyen:.
2. Navigáljon a védelemmel ellátni kívánt felügyelt példány konfigurációs lapjára. A **Beállítások** lapon kattintson a **speciális adatbiztonság**elemre.
3. A speciális adatbiztonság konfigurálása lapon
   - **A** speciális adatbiztonság bekapcsolása.
   - Konfigurálja az **e-mailek listáját** , hogy biztonsági riasztásokat kapjon a rendellenes adatbázis-tevékenységek észlelése után.
   - Válassza ki azt az **Azure Storage-fiókot** , ahol a rendellenes veszélyforrások naplózási rekordjait menti a rendszer.
   - Válassza ki a konfigurálni kívánt komplex **veszélyforrások elleni védelem típusát** . További információ a komplex [veszélyforrások elleni védelem riasztásokról](sql-database-threat-detection-overview.md).
4. Kattintson a **Save (Mentés** ) gombra az új vagy frissített speciális adatbiztonsági házirend mentéséhez.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > A képernyőképek árai nem mindig tükrözik az aktuális árat, és példa erre.

## <a name="next-steps"></a>További lépések

- További információ a komplex [veszélyforrások elleni védelemről](sql-database-threat-detection-overview.md).
- A felügyelt példányok ismertetése: [Mi az a felügyelt példány](sql-database-managed-instance.md).
- További információ [az önálló adatbázisok komplex veszélyforrások elleni védelméről](sql-database-threat-detection.md).
- További információ a [felügyelt példányok naplózásáról](https://go.microsoft.com/fwlink/?linkid=869430).
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro).
