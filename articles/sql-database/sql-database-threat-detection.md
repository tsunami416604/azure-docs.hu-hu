---
title: Az Azure Komplex veszélyforrások elleni védelem konfigurálása
description: A komplex veszélyforrások elleni védelem észleli a rendellenes adatbázis-tevékenységeket, amelyek egyetlen adatbázisban vagy rugalmas készletben jelzik az adatbázispotenciális biztonsági fenyegetéseit.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822507"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database Komplex veszélyforrások elleni védelem egy- vagy készletezésű adatbázisokhoz

Az egy- és készletezésű adatbázisok [komplex veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) észleli az adatbázisokhoz való szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket. A komplex veszélyforrások elleni védelem képes azonosítani **a potenciális SQL-injektálást**, **a szokatlan helyről vagy adatközpontból való hozzáférést**, az ismeretlen egyszerű vagy **potenciálisan káros alkalmazásból való hozzáférést**és a **Találgatásos SQL-hitelesítő adatokat** - további részleteket az [Advanced Threat Protection riasztásokban](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)talál.

Az észlelt fenyegetésekről [e-mailes értesítésekvagy](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) [Az Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) on keresztül kaphat értesítéseket.

[A komplex veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) része a [fejlett adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlatnak, amely egy egységes csomag a fejlett SQL biztonsági képességekhez. Az Advanced Threat Protection a központi SQL ADS portálon keresztül érhető el és kezelhető.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>A komplex veszélyforrások elleni védelem beállítása az Azure Portalon

1. Indítsa el az [https://portal.azure.com](https://portal.azure.com)Azure Portalt a helyen.
2. Keresse meg a védeni kívánt Azure SQL Database-kiszolgáló konfigurációs lapját. A biztonsági beállítások ban válassza a **Speciális adatbiztonság**lehetőséget.
3. A **Speciális adatbiztonság** konfigurációs lapján:

   - Engedélyezze a speciális adatbiztonságot a kiszolgálón.
   - A **Komplex veszélyforrások elleni védelem beállításai**párbeszédpanelen a Riasztások küldése a **szövegbe** mezőben adja meg a biztonsági riasztások fogadásához a rendellenes adatbázis-tevékenységek észlelése kor érkező e-mailek listáját.
  
   ![Komplex veszélyforrások elleni védelem beállítása](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > A képernyőképeken az árak nem mindig tükrözik az aktuális árat, és példát mutatnak.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Az Advanced Threat Protection beállítása PowerShell használatával

Például parancsfájlt, [lásd: Naplózás és komplex veszélyforrások elleni védelem konfigurálása a PowerShell használatával című témakörben.](scripts/sql-database-auditing-and-threat-detection-powershell.md)

## <a name="next-steps"></a>További lépések

- További információ a [komplex veszélyforrások elleni védelemről.](sql-database-threat-detection-overview.md)
- További információ [a felügyelt példányok komplex veszélyforrások elleni védelméről.](sql-database-managed-instance-threat-detection.md)  
- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- További információ a [naplózásról](sql-database-auditing.md)
- További információ az [Azure biztonsági központjáról](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Az árképzésről az SQL [Database díjszabási lapján](https://azure.microsoft.com/pricing/details/sql-database/) talál további információt.  
