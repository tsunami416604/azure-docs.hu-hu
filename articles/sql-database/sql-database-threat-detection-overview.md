---
title: Fejlett fenyegetésvédelem
description: A komplex veszélyforrások elleni védelem rendellenes adatbázis-tevékenységeket észlel a Azure SQL Database lehetséges biztonsági fenyegetéseket jelezve.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124760"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database – Advanced Threat Protection

A [Azure SQL Database](sql-database-technical-overview.md) és az [Azure szinapszis Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A komplex veszélyforrások elleni védelem a [fejlett adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. Az Advanced Threat Protection a központi SQL ADS portálon keresztül érhető el és kezelhető.

> [!NOTE]
> Ez a témakör az Azure SQL Serverre, valamint az Azure SQL Serveren létrehozott SQL Database és az Azure Szinapszisra is vonatkozik. Az egyszerűség kedvéért SQL Database a rendszer akkor használja, ha a SQL Database és az Azure Szinapszisra hivatkozik.

## <a name="what-is-advanced-threat-protection"></a>Mi az a komplex veszélyforrások elleni védelem?

 A komplex veszélyforrások elleni védelem új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelje és válaszolják meg a potenciális fenyegetéseket. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a potenciális sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A komplex veszélyforrások elleni védelem a riasztásokat [Azure Security Centerokkal](https://azure.microsoft.com/services/security-center/)integrálja, amelyek tartalmazzák a gyanús tevékenységek részleteit, és javaslatokat tesznek a fenyegetés kivizsgálására és enyhítésére. A komplex veszélyforrások elleni védelem révén egyszerűen kezelheti az adatbázis potenciális veszélyforrásait anélkül, hogy biztonsági szakértőnek kellene lennie, vagy speciális biztonsági monitorozási rendszereket kellene kezelnie.

A teljes körű vizsgálathoz ajánlott engedélyezni [SQL Database naplózást](sql-database-auditing.md), amely az adatbázis-eseményeket az Azure Storage-fiókban lévő naplóba írja.  

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni védelem – riasztások

A Azure SQL Database komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához, és a következő riasztásokat indíthatják el:

- **Biztonsági rés az SQL-injektáláshoz**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás hibás SQL-utasítást hoz létre az adatbázisban. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:

  - Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
  - Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
- **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan Azure-adatközpontból jelentkezett be az SQL-kiszolgálóra, ami a kiszolgálón is látható volt a közelmúltban. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új Azure-, Power BI- vagy Azure SQL Query Editor-alkalmazást). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan résztvevő (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>A rendellenes adatbázis-tevékenységek megismerése gyanús események észlelése után

A rendellenes adatbázis-tevékenységek észlelése után e-mailben értesítést kap. Az e-mail információt nyújt a gyanús biztonsági eseményekről, beleértve a rendellenes tevékenységek jellegét, az adatbázis nevét, a kiszolgáló nevét, az alkalmazás nevét és az esemény időpontját. Emellett az e-mail-cím a lehetséges okokkal és a javasolt műveletekkel kapcsolatos információkat nyújt az adatbázis lehetséges fenyegetésének kivizsgálásához és enyhítéséhez.

![Rendellenes tevékenység jelentés](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kattintson a **legutóbbi SQL-riasztások megtekintése** hivatkozásra az e-mailben a Azure Portal elindításához és a Azure Security Center riasztások oldal megjelenítéséhez, amely áttekintést nyújt az SQL Database-ben észlelt aktív fenyegetésekről.

   ![Tevékenységgel kapcsolatos fenyegetések](./media/sql-database-threat-detection/active_threats.png)

2. Egy adott riasztásra kattintva további részleteket és műveleteket kaphat a fenyegetés kivizsgálásához és a jövőbeli fenyegetések szervizelését.

   Az SQL injection például az egyik leggyakoribb webalkalmazási biztonsági probléma az interneten, amely az adatvezérelt alkalmazások támadására szolgál. A támadók kihasználhatják az alkalmazások biztonsági réseit, hogy rosszindulatú SQL-utasításokat szúrnak be az Application Entry mezőibe, vagy megsértsék vagy módosíthassák az adatokat az adatbázisban. Az SQL-injektálási riasztások esetében a riasztás részletei közé tartozik a kihasznált sebezhető SQL-utasítás.

   ![Adott riasztás](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Ismerje meg az adatbázishoz tartozó komplex veszélyforrások elleni védelmi riasztásokat a Azure Portal

A komplex veszélyforrások elleni védelem a riasztásokat az [Azure Security Center](https://azure.microsoft.com/services/security-center/)szolgáltatással integrálja. Az adatbázison belüli élő SQL Advanced Threat Protection csempék és az SQL-hirdetések pengéi a Azure Portal nyomon követik az aktív fenyegetések állapotát.

Kattintson a komplex **veszélyforrások elleni védelem riasztása** lehetőségre a Azure Security Center riasztások oldal elindításához, és tekintse át az adatbázisban észlelt aktív SQL-fenyegetések áttekintését.

   ![Komplex veszélyforrások elleni védelem riasztása](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Komplex veszélyforrások elleni védelem alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>További lépések

- További információ a komplex [veszélyforrások elleni védelemről az önálló és a készletezett adatbázisokban](sql-database-threat-detection.md).
- További információ a [felügyelt példányok komplex veszélyforrások elleni védelméről](sql-database-managed-instance-threat-detection.md).
- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- További információ a [Azure SQL Database naplózásról](sql-database-auditing.md)
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
- A díjszabással kapcsolatos további információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) .  
