---
title: Az Azure Komplex veszélyforrások elleni védelem konfigurálása
titleSuffix: Azure SQL Managed Instance
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel, amelyek az Azure SQL felügyelt példányain lévő adatbázis potenciális biztonsági fenyegetéseit jelzik.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d2ea14356bf85c795769f1d406f1571f36adaa38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617916"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Az összetett veszélyforrások elleni védelem konfigurálása az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az [Azure SQL felügyelt példányainak](sql-managed-instance-paas-overview.md) komplex [veszélyforrások elleni védelme](../database/threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. A komplex veszélyforrások elleni védelem képes azonosítani a **lehetséges SQL-injektálásokat**, **a szokatlan helyről vagy adatközpontból való hozzáférést**, **az ismeretlen elsődleges vagy potenciálisan ártalmas alkalmazások elérését**, valamint a **találgatásos SQL-hitelesítő adatokat** . További részletek a komplex [veszélyforrások elleni védelmi riasztásokban](../database/threat-detection-overview.md#alerts).

Értesítéseket kaphat az észlelt fenyegetésekről az [e-mail-értesítések](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) vagy a [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal) használatával

A komplex [veszélyforrások elleni védelem](../database/threat-detection-overview.md) az [Azure Defender for SQL](../database/azure-defender-for-sql.md) -ajánlat része, amely a fejlett SQL biztonsági funkciók egységes csomagja. A komplex veszélyforrások elleni védelem a központi Azure Defender for SQL portálon keresztül érhető el és kezelhető.

##  <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a  [Azure Portalba](https://portal.azure.com). 
2. Navigáljon a védelemmel ellátni kívánt SQL felügyelt példány konfigurációs lapjára. A **Biztonság**területen válassza a **Security Center**elemet.
3. Az Azure Defender for SQL konfigurálása lapon
   - Az **Azure Defender** for SQL bekapcsolása.
   - Konfigurálja az **e-mailek listáját** , hogy biztonsági riasztásokat kapjon a rendellenes adatbázis-tevékenységek észlelése után.
   - Válassza ki azt az **Azure Storage-fiókot** , ahol a rendellenes veszélyforrások naplózási rekordjait menti a rendszer.
   - Válassza ki a konfigurálni kívánt komplex **veszélyforrások elleni védelem típusát** . További információ a komplex [veszélyforrások elleni védelem riasztásokról](../database/threat-detection-overview.md).
4. Kattintson a **Save (Mentés** ) gombra az új vagy frissített Azure Defender SQL-házirend mentéséhez.

## <a name="next-steps"></a>Következő lépések

- További információ a komplex [veszélyforrások elleni védelemről](../database/threat-detection-overview.md).
- A felügyelt példányok ismertetése: [Mi az az Azure SQL felügyelt példánya](sql-managed-instance-paas-overview.md).
- További információ a [Azure SQL Database összetett veszélyforrások elleni védelméről](../database/threat-detection-configure.md).
- További információ a [felügyelt SQL-példányok naplózásáról](https://go.microsoft.com/fwlink/?linkid=869430).
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro).
