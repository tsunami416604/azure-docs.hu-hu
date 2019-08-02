---
title: Fenyegetések észlelése – Azure SQL Database | Microsoft Docs
description: A veszélyforrások észlelése rendellenes adatbázis-tevékenységeket észlel, amely az adatbázisra vonatkozó lehetséges biztonsági fenyegetéseket mutatja egyetlen adatbázisban vagy rugalmas készletben.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 5549d016978e8bf9491c3745e335e3c4c793212c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566330"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Azure SQL Database veszélyforrások észlelése egyetlen vagy készletezett adatbázis esetén

[](sql-database-threat-detection-overview.md) Az Egypéldányos és a készletezett adatbázisok fenyegetésének észlelése rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas próbálkozások érhetők el az adatbázisok eléréséhez vagy kiaknázásához A fenyegetések észlelése képes azonosítani a **lehetséges SQL**-injektálásokat, **a szokatlan helyekről vagy adatközpontokból való hozzáférést**, **az ismeretlen rendszerbiztonsági tag vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatokat** – további részletek a [veszélyforrások észlelésével](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)kapcsolatos riasztások.

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy a [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) használatával

[](sql-database-threat-detection-overview.md) A veszélyforrások észlelése a [speciális adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. A fenyegetések észlelése a központi SQL ADS portálon keresztül érhető el és kezelhető. A speciális adatbiztonsági csomagot a rendszer minden logikai kiszolgálón 15 $/hó terheli, az első 30 nap díjmentesen.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>A veszélyforrások észlelésének beállítása az adatbázishoz a Azure Portal

1. Indítsa el a Azure Portal [https://portal.azure.com](https://portal.azure.com)a következő helyen:.
2. Navigáljon a védelemmel ellátni kívánt Azure SQL Database-kiszolgáló konfigurációs lapjára. A biztonsági beállítások területen válassza a **speciális adatbiztonság**lehetőséget.
3. A **speciális adatbiztonság** beállítása lapon:

   - Engedélyezze a speciális adatbiztonságot a kiszolgálón.
   - A **veszélyforrások észlelése beállításban**a **riasztások küldése** szövegmezőbe mezőben adja meg azoknak az e-maileknek a listáját, amelyek biztonsági riasztásokat kapnak a rendellenes adatbázis-tevékenységek észlelése után.
  
   ![Fenyegetés észlelésének beállítása](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Fenyegetések észlelésének beállítása a PowerShell használatával

A parancsfájlokra példa: a [naplózás és a veszélyforrások észlelésének konfigurálása a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>További lépések

- További információ a [fenyegetések észleléséről](sql-database-threat-detection-overview.md).
- További információ a [fenyegetések észleléséről a felügyelt példányon](sql-database-managed-instance-threat-detection.md).  
- További információ a [speciális](sql-database-advanced-data-security.md)adatbiztonságról.
- További információ a [naplózásról](sql-database-auditing.md)
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
- A díjszabással kapcsolatos további információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) .  
