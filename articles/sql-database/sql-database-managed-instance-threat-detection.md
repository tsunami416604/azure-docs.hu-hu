---
title: Fenyegetések észlelése – konfigurálása az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések a felügyelt példány az adatbázishoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158669"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példány Fenyegetésészlelési funkciójának (előzetes verzió) konfigurálása

Az Azure SQL [Fenyegetésészlelés](sql-database-threat-detection-overview.md) a [SQL Database felügyelt példányain](sql-database-managed-instance-index.yml) észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek. A Fenyegetésészlelés azonosíthatja **lehetséges SQL-injektálás**, **hozzáférés szokatlan helyről vagy a data Center**, **ismeretlen egyszerű vagy potenciálisan káros alkalmazástvalóhozzáférés**, és **találgatásos SQL hitelesítő adatok** – további részletek az [Fenyegetésészlelési riasztásokat](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Is fogadhatja az értesítéseket keresztül észlelt fenyegetésről [e-mail-értesítések](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) vagy [Azure Portalon](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Veszélyforrások Detektálása](sql-database-threat-detection-overview.md) része a [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md) (ATP) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A Fenyegetésészlelés érheti el és a központi SQL ATP portálján felügyelt. Fenyegetés-észlelési szolgáltatás 15 USD/hó / felügyelt példány díjának 30 napig ingyenesen.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Az Azure Portalon a felügyelt példány Fenyegetésészlelési beállítása

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a felügyelt példány számára védelmet kíván konfiguráció lapján. Az a **beállítások** lapon jelölje be **Fenyegetésészlelés**.
3. A Fenyegetésészlelés konfigurációs lapja
   - Kapcsolja be **ON** fenyegetések észlelése.
   - Konfigurálja a **e-mailek listájának** a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld.
   - Válassza ki a **Azure storage-fiók** rendellenes threat naplózási bejegyzések mentési helye.
4. Kattintson a **mentése** menteni az új vagy frissített fenyegetésészlelési szabályzatát.

   ![Fenyegetések észlelése](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Fenyegetésészlelés](sql-database-threat-detection-overview.md).
- Felügyelt példánnyal kapcsolatos további tudnivalókat a [mit jelent a felügyelt példány](sql-database-managed-instance.md).
- Tudjon meg többet [önálló adatbázis Fenyegetésészlelési](sql-database-threat-detection.md).
- Tudjon meg többet [felügyelt példány naplózás](https://go.microsoft.com/fwlink/?linkid=869430).
- Tudjon meg többet [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
