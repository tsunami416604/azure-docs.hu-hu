---
title: A veszélyforrások észlelésének konfigurálása – Azure SQL Database felügyelt példány | Microsoft Docs
description: A veszélyforrások észlelése rendellenes adatbázis-tevékenységeket észlel a felügyelt példányokban lévő adatbázis potenciális biztonsági fenyegetéseit jelezve.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567295"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>A veszélyforrások észlelésének konfigurálása (előzetes verzió) Azure SQL Database felügyelt példányban

A [felügyelt példányok](sql-database-managed-instance-index.yml) fenyegetés- [észlelése](sql-database-threat-detection-overview.md) rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas kísérletekre van lehetőség az adatbázisok eléréséhez vagy kiaknázásához. A fenyegetések észlelése képes azonosítani a **lehetséges SQL**-injektálásokat, **a szokatlan helyekről vagy adatközpontokból való hozzáférést**, **az ismeretlen rendszerbiztonsági tag vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatokat** – további részletek a [veszélyforrások észlelésével](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)kapcsolatos riasztások.

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy a [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) használatával

[](sql-database-threat-detection-overview.md) A veszélyforrások észlelése a [speciális adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. A fenyegetések észlelése a központi SQL ADS portálon keresztül érhető el és kezelhető. A veszélyforrások észlelésére szolgáló szolgáltatás díja 15 USD/hó, és az első 30 nap díjmentes.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>A felügyelt példány fenyegetés-észlelésének beállítása a Azure Portal

1. Indítsa el a Azure Portal [https://portal.azure.com](https://portal.azure.com)a következő helyen:.
2. Navigáljon a védelemmel ellátni kívánt felügyelt példány konfigurációs lapjára. A **Beállítások** lapon válassza a **veszélyforrások észlelése**elemet.
3. A veszélyforrások észlelése konfigurációs oldalon
   - **A** veszélyforrások észlelésének bekapcsolása.
   - Konfigurálja az **e-mailek listáját** , hogy biztonsági riasztásokat kapjon a rendellenes adatbázis-tevékenységek észlelése után.
   - Válassza ki azt az **Azure Storage-fiókot** , ahol a rendellenes veszélyforrások naplózási rekordjait menti a rendszer.
4. Kattintson a **Save (Mentés** ) gombra az új vagy frissített veszélyforrás-észlelési házirend mentéséhez.

   ![fenyegetések észlelése](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>További lépések

- További információ a [fenyegetések észleléséről](sql-database-threat-detection-overview.md).
- A felügyelt példányok ismertetése: [Mi az a felügyelt példány](sql-database-managed-instance.md).
- További információ az [önálló adatbázisok fenyegetés](sql-database-threat-detection.md)-észleléséről.
- További információ a [felügyelt példányok naplózásáról](https://go.microsoft.com/fwlink/?linkid=869430).
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro).
