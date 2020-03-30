---
title: Fejlett fenyegetésvédelem
description: A komplex veszélyforrások elleni védelem észleli a rendellenes adatbázis-tevékenységeket, amelyek potenciális biztonsági fenyegetéseket jeleznek az Azure SQL Database-ben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124760"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database – Advanced Threat Protection

Az Azure [SQL Database](sql-database-technical-overview.md) és az [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.

A komplex veszélyforrások elleni védelem része a [speciális adatbiztonsági](sql-database-advanced-data-security.md) (ADS) ajánlatnak, amely egy egységes csomag a fejlett SQL biztonsági képességekhez. Az Advanced Threat Protection a központi SQL ADS portálon keresztül érhető el és kezelhető.

> [!NOTE]
> Ez a témakör az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és Azure Synapse rendszerre is vonatkozik. Az egyszerűség kedvéért az SQL Database az SQL Database és az Azure Synapse hivatkozva használatos.

## <a name="what-is-advanced-threat-protection"></a>Mi az advanced threat protection

 A komplex veszélyforrások elleni védelem egy új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy észleljék és reagáljanak a potenciális fenyegetésekre, amikor azok előfordulnak a rendellenes tevékenységekre vonatkozó biztonsági riasztások biztosításával. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a potenciális biztonsági résekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A komplex veszélyforrások elleni védelem integrálja a riasztásokat az [Azure Security Centerrel,](https://azure.microsoft.com/services/security-center/)amely tartalmazza a gyanús tevékenységek részleteit, és javaslatot tesz a fenyegetés kivizsgálására és csökkentésére irányuló lépésekre. A komplex veszélyforrások elleni védelem egyszerűvé teszi az adatbázist fenyegető potenciális fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, vagy fejlett biztonsági figyelőrendszereket kellene kezelnie.

A teljes vizsgálati élmény érdekében ajánlott engedélyezni az [SQL Database Auditing](sql-database-auditing.md), amely az Azure storage-fiók naplójába írja az adatbázis-eseményeket.  

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni riasztások

Az Azure SQL Database komplex veszélyforrások elleni védelme észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányulnak, és a következő riasztásokat indíthatja el:

- **Sql-injektálás biztonsági rése**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás hibás SQL-utasítást hoz létre az adatbázisban. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:

  - Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
  - Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
- **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan Azure-adatközpontból jelentkezett be az SQL-kiszolgálóra, ami a kiszolgálón is látható volt a közelmúltban. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új Azure-, Power BI- vagy Azure SQL Query Editor-alkalmazást). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan résztvevő (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>A rendellenes adatbázis-tevékenységek feltárása gyanús események észlelése esetén

A rendellenes adatbázis-tevékenységek észlelésekor e-mailben értesítést kap. Az e-mail a gyanús biztonsági eseményről nyújt információkat, beleértve a rendellenes tevékenységek jellegét, az adatbázis nevét, a kiszolgáló nevét, az alkalmazás nevét és az esemény idejét. Ezenkívül az e-mail tájékoztatást nyújt a lehetséges okok és ajánlott műveletek az adatbázis potenciális veszélyének kivizsgálására és csökkentésére.

![Rendellenes tevékenységjelentés](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kattintson a **Legutóbbi SQL-riasztások megtekintése** hivatkozásra az e-mailben az Azure Portal elindításához és az Azure Security Center riasztási lapjának megjelenítéséhez, amely áttekintést nyújt az SQL-adatbázisban észlelt aktív fenyegetésekről.

   ![Tevékenységi fenyegetések](./media/sql-database-threat-detection/active_threats.png)

2. Kattintson egy adott riasztásra a fenyegetés kivizsgálásához és a jövőbeli fenyegetések elhárításához szükséges további részletek és műveletek leküzdése érdekében.

   Az SQL-injektálás például az egyik leggyakoribb webes alkalmazásbiztonsági probléma az interneten, amely az adatközpontú alkalmazások támadására szolgál. A támadók az alkalmazások biztonsági réseinek előnyeit kihasználva rosszindulatú SQL-utasításokat juttatnak az alkalmazásbeviteli mezőkbe, megsértve vagy módosítva az adatbázis adatait. AZ SQL Injection riasztások, a riasztás részletei közé tartozik a sebezhető SQL-utasítás, amely kiaknázva.

   ![Különleges riasztás](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Fedezze fel az adatbázis komplex veszélyforrások elleni riasztásait az Azure Portalon

A Komplex veszélyforrások elleni védelem integrálja a riasztásokat az [Azure biztonsági központjával.](https://azure.microsoft.com/services/security-center/) Az Azure Portalon található Élő SQL Komplex veszélyforrások elleni védelem csempéi az adatbázison belül és az SQL ADS panelek az aktív fenyegetések állapotát követik nyomon.

Kattintson **a Komplex veszélyforrások elleni riasztásra** az Azure Security Center riasztási lapjának elindításához, és az adatbázisban észlelt aktív SQL-fenyegetések áttekintéséhez.

   ![Komplex veszélyforrások elleni riasztás](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Komplex veszélyforrások elleni riasztás2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>További lépések

- További információ [az egy- és összevont adatbázisokban található komplex veszélyforrások elleni védelemről.](sql-database-threat-detection.md)
- További információ [a felügyelt példányok komplex veszélyforrások elleni védelméről.](sql-database-managed-instance-threat-detection.md)
- További információ a [speciális adatbiztonságról](sql-database-advanced-data-security.md).
- További információ az [Azure SQL Database naplózásáról](sql-database-auditing.md)
- További információ az [Azure biztonsági központjáról](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Az árképzésről az SQL [Database díjszabási lapján](https://azure.microsoft.com/pricing/details/sql-database/) talál további információt.  
