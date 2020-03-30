---
title: Speciális veszélyforrások elleni védelem konfigurálása – felügyelt példány
description: A komplex veszélyforrások elleni védelem észleli a rendellenes adatbázis-tevékenységeket, amelyek egy felügyelt példányban az adatbázist fenyegető potenciális biztonsági fenyegetésekre utalnak.
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
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822550"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>A komplex veszélyforrások elleni védelem konfigurálása az Azure SQL Database által felügyelt példányban

A [felügyelt példányok](sql-database-managed-instance-index.yml) [komplex veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket. A komplex veszélyforrások elleni védelem képes azonosítani **a potenciális SQL-injektálást**, **a szokatlan helyről vagy adatközpontból való hozzáférést**, az ismeretlen egyszerű vagy **potenciálisan káros alkalmazásból való hozzáférést**és a **Találgatásos SQL-hitelesítő adatokat** - további részleteket az [Advanced Threat Protection riasztásokban](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)talál.

Az észlelt fenyegetésekről [e-mailes értesítésekvagy](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) [Az Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) on keresztül kaphat értesítéseket.

[A komplex veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) része a [fejlett adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlatnak, amely egy egységes csomag a fejlett SQL biztonsági képességekhez. Az Advanced Threat Protection a központi SQL ADS portálon keresztül érhető el és kezelhető.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>A komplex veszélyforrások elleni védelem beállítása az Azure Portalon

1. Indítsa el az [https://portal.azure.com](https://portal.azure.com)Azure Portalt a helyen.
2. Nyissa meg a védeni kívánt felügyelt példány konfigurációs lapját. A **Beállítások** lapon válassza a **Speciális adatbiztonság**lehetőséget.
3. A Speciális adatbiztonság konfigurációs lapján
   - Kapcsolja be **a** speciális adatbiztonságot.
   - Konfigurálja úgy **az e-mailek listáját,** hogy a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztásokat kapjon.
   - Válassza ki azt az **Azure storage-fiókot,** ahol rendellenes fenyegetés-naplózási rekordok at menti.
   - Válassza ki a konfigurálni kívánt **komplex veszélyforrások elleni védelemtípusokat.** További információ a [komplex veszélyforrások elleni riasztásokról.](sql-database-threat-detection-overview.md)
4. Kattintson a **Mentés** gombra az új vagy frissített speciális adatbiztonsági házirend mentéséhez.

   ![Fejlett fenyegetésvédelem](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > A képernyőképeken az árak nem mindig tükrözik az aktuális árat, és példát mutatnak.

## <a name="next-steps"></a>További lépések

- További információ a [komplex veszélyforrások elleni védelemről.](sql-database-threat-detection-overview.md)
- A felügyelt példányok ról a [Mi a felügyelt példány.](sql-database-managed-instance.md)
- További információ az [egyetlen adatbázis hoz való komplex veszélyforrások elleni védelemről.](sql-database-threat-detection.md)
- További információ a [felügyelt példányok naplózásáról.](https://go.microsoft.com/fwlink/?linkid=869430)
- További információ az [Azure biztonsági központjáról.](https://docs.microsoft.com/azure/security-center/security-center-intro)
