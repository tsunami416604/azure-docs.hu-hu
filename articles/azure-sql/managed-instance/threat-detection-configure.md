---
title: Az Azure Komplex veszélyforrások elleni védelem konfigurálása
titleSuffix: Azure SQL Managed Instance
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel, amelyek az Azure SQL felügyelt példányain lévő adatbázis potenciális biztonsági fenyegetéseit jelzik.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: a60156762a4d8dfb6b11ae70e608fb26b07e5764
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045759"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Az összetett veszélyforrások elleni védelem konfigurálása az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az [Azure SQL felügyelt példányainak](sql-managed-instance-paas-overview.md) komplex [veszélyforrások elleni védelme](../database/threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A komplex veszélyforrások elleni védelem képes azonosítani a **lehetséges SQL-injektálásokat**, **a szokatlan helyről vagy adatközpontból való hozzáférést**, **az ismeretlen elsődleges vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatokat** . További részletek a komplex [veszélyforrások elleni védelmi riasztásokban](../database/threat-detection-overview.md#alerts).

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) vagy a [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-azure-portal) használatával

A komplex [veszélyforrások elleni védelem](../database/threat-detection-overview.md) a fejlett [adatbiztonsági](../database/advanced-data-security.md) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. Az Advanced Threat Protection a központi SQL ADS portálon keresztül érhető el és kezelhető.

##  <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com). 
2. Navigáljon a védelemmel ellátni kívánt SQL felügyelt példány konfigurációs lapjára. A **Beállítások** lapon kattintson a **speciális adatbiztonság**elemre.
3. A speciális adatbiztonság konfigurálása lapon
   - **A** speciális adatbiztonság bekapcsolása.
   - Konfigurálja az **e-mailek listáját** , hogy biztonsági riasztásokat kapjon a rendellenes adatbázis-tevékenységek észlelése után.
   - Válassza ki azt az **Azure Storage-fiókot** , ahol a rendellenes veszélyforrások naplózási rekordjait menti a rendszer.
   - Válassza ki a konfigurálni kívánt komplex **veszélyforrások elleni védelem típusát** . További információ a komplex [veszélyforrások elleni védelem riasztásokról](../database/threat-detection-overview.md).
4. Kattintson a **Save (Mentés** ) gombra az új vagy frissített speciális adatbiztonsági házirend mentéséhez.

   ![Fejlett fenyegetésvédelem](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>További lépések

- További információ a komplex [veszélyforrások elleni védelemről](../database/threat-detection-overview.md).
- A felügyelt példányok ismertetése: [Mi az az Azure SQL felügyelt példánya](sql-managed-instance-paas-overview.md).
- További információ a [Azure SQL Database összetett veszélyforrások elleni védelméről](../database/threat-detection-configure.md).
- További információ a [felügyelt SQL-példányok naplózásáról](https://go.microsoft.com/fwlink/?linkid=869430).
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro).
