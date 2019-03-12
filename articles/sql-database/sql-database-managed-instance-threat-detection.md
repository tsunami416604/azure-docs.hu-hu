---
title: Konfigurálja a fenyegetések észlelése – Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések a felügyelt példány az adatbázishoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0c6bf22527278d0d2217a8b384eb8bcd2210856a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779512"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példány fenyegetésészlelési funkciójának (előzetes verzió) konfigurálása

[Veszélyforrások detektálása](sql-database-threat-detection-overview.md) számára egy [felügyelt példány](sql-database-managed-instance-index.yml) észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek. A fenyegetésészlelés azonosíthatja **lehetséges SQL-injektálás**, **hozzáférés szokatlan helyről vagy a data Center**, **ismeretlen egyszerű vagy potenciálisan káros alkalmazástvalóhozzáférés**, és **találgatásos SQL hitelesítő adatok** – további részletek az [fenyegetés-észlelési riasztás](sql-database-threat-detection-overview.md#threat-detection-alerts).

Is fogadhatja az értesítéseket keresztül észlelt fenyegetésről [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy [Azure Portalon](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Veszélyforrások detektálása](sql-database-threat-detection-overview.md) része a [adatbiztonság speciális](sql-database-advanced-data-security.md) (ADS) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A fenyegetésészlelés érheti el és a központi SQL ADS portálon keresztül kezelhetők. Fenyegetés-észlelési szolgáltatás 15 USD/hó / felügyelt példány díjának 30 napig ingyenesen.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Az Azure Portalon a felügyelt példány fenyegetésészlelési beállítása

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a következő felügyelt példányt védeni kívánt konfiguráció lapján. Az a **beállítások** lapon jelölje be **Fenyegetésészlelés**.
3. A Fenyegetésészlelés konfigurációs lapja
   - Kapcsolja be **ON** fenyegetések észlelése.
   - Konfigurálja a **e-mailek listájának** a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld.
   - Válassza ki a **Azure storage-fiók** rendellenes threat naplózási bejegyzések mentési helye.
4. Kattintson a **mentése** menteni az új vagy frissített fenyegetésészlelési szabályzatát.

   ![Fenyegetések észlelése](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [fenyegetésészlelés](sql-database-threat-detection-overview.md).
- További tudnivalók a felügyelt példányok, lásd: [Mi az a felügyelt példány](sql-database-managed-instance.md).
- Tudjon meg többet [fenyegetésészlelés önálló adatbázis](sql-database-threat-detection.md).
- Tudjon meg többet [felügyelt példány naplózás](https://go.microsoft.com/fwlink/?linkid=869430).
- Tudjon meg többet [az Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro).
