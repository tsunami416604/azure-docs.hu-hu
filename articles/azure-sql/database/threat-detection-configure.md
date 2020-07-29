---
title: Az Azure Komplex veszélyforrások elleni védelem konfigurálása
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel, amelyeken az adatbázis lehetséges biztonsági fenyegetéseket jelez Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321546"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Az összetett veszélyforrások elleni védelem konfigurálása Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database komplex [veszélyforrások elleni védelme](threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A komplex veszélyforrások elleni védelem képes azonosítani a **lehetséges SQL-injektálásokat**, **a szokatlan helyről vagy adatközpontból való hozzáférést**, **az ismeretlen elsődleges vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatokat** . További részletek a komplex [veszélyforrások elleni védelmi riasztásokban](threat-detection-overview.md#alerts).

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](threat-detection-overview.md#explore-detection-of-a-suspicious-event) vagy a [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal) használatával

A komplex [veszélyforrások elleni védelem](threat-detection-overview.md) a fejlett [adatbiztonsági](advanced-data-security.md) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. A komplex veszélyforrások elleni védelem a központi SQL Advanced Security portálon keresztül érhető el és kezelhető.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Az összetett veszélyforrások elleni védelem beállítása a Azure Portal

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com).
2. Navigáljon a védetté tenni kívánt kiszolgáló konfigurációs lapjára. A biztonsági beállítások területen válassza a **speciális adatbiztonság**lehetőséget.
3. A **speciális adatbiztonság** beállítása lapon:

   - Engedélyezze a speciális adatbiztonságot a kiszolgálón.
   - Az **összetett veszélyforrások elleni védelem beállításaiban**a **riasztások küldése** a szövegmezőbe mezőbe írja be azoknak az e-maileknek a listáját, amelyek biztonsági riasztásokat kapnak a rendellenes adatbázis-tevékenységek észlelése után.
  
   ![Komplex veszélyforrások elleni védelem beállítása](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Az Advanced Threat Protection beállítása PowerShell használatával

A parancsfájlokra példa: a [naplózás és az összetett veszélyforrások elleni védelem konfigurálása a PowerShell használatával](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>További lépések

- További információ a komplex [veszélyforrások elleni védelemről](threat-detection-overview.md).
- További információ a komplex [veszélyforrások elleni védelemről az SQL felügyelt példányában](../managed-instance/threat-detection-configure.md).  
- További információ a [speciális adatbiztonságról](advanced-data-security.md).
- További információ a [naplózásról](../../azure-sql/database/auditing-overview.md)
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
- A díjszabással kapcsolatos további információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) .  
