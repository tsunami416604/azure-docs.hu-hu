---
title: Veszélyforrások Detektálása - Azure SQL adatbázis |} Microsoft Docs
description: A fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket észleli, amelyek esetleges biztonsági fenyegetéseket jelezhetnek.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-sql-database-threat-detection"></a>Az Azure SQL adatbázis fenyegetések észlelése

Az Azure SQL adatbázis Fenyegetésészlelés szokatlan és potenciálisan káros kísérletek eléréséhez, vagy a biztonsági rések elleni adatbázisok jelző rendellenes tevékenységek észleli.

A Fenyegetésészlelés része a [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) ajánlat, amely a speciális SQL biztonsági képességei egyesített csomagok. A Fenyegetésészlelés érhető el, és a központi SQL ATP portálon kezelt.

## <a name="what-is-threat-detection"></a>Mi az a Fenyegetésészlelés?

A Fenyegetésészlelés SQL biztonsági, amelyek segítségével a felhasználók észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások a rendellenes tevékenységek reagáljon a lehetséges veszélyforrásokra egy új réteget biztosít. Felhasználók adatbázis gyanús tevékenységeket, a potenciális biztonsági réseket, hogy riasztást kapjon, és az SQL-injektálás támadások, valamint hozzáfér a rendellenes adatbázis, és lekérdezi a minták. SQL Fenyegetésészlelés integrálja riasztások [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely tartalmazza a gyanús tevékenységet, és az ajánlott művelet vizsgálja meg, és a fenyegetések mérséklésére. A Fenyegetésészlelés SQL egyszerűen potenciális fenyegetések ellen az adatbázisba történő szakértői biztonsági vagy speciális biztonsági rendszerek kezelése nélkül. 

A teljes vizsgálat élmény érdekében javasoljuk, hogy engedélyezze [SQL Database Auditing](sql-database-auditing.md), amely ír az adatbázis naplózási események jelentkezzen be az Azure storage-fiók.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>A fenyegetésészlelés az adatbázis az Azure portálon beállítása
1. Indítsa el az Azure portálon, a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a lap a védeni kívánt Azure SQL adatbázis-kiszolgáló. Válassza ki a biztonsági beállítások **Advanced Threat Protection**.
3. Az a **Advanced Threat Protection** konfigurálása lapon:

   - Advanced Threat Protection engedélyezéséhez a kiszolgálón.
   - A **fenyegetés észlelési beállítások**, a a **küldje a riasztásokat** szöveget adja meg a biztonsági riasztások adatbázist érintő rendellenes tevékenységeket a észlelésekor e-mailek listáját.
  
   ![A fenyegetésészlelés beállítása](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Állítsa be a fenyegetésészlelés PowerShell használatával

Tekintse meg a parancsfájl például [konfigurálhatja a naplózás és a fenyegetések észlelésére, a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Fedezze fel az adatbázist érintő rendellenes tevékenységeket gyanús esemény észlelése

A adatbázist érintő rendellenes tevékenységeket észlelésekor e-mailben értesítést kapni. Az e-mailt a gyanús biztonsági esemény, például a rendellenes tevékenységek, adatbázisnév, kiszolgálónév, az alkalmazásnév és az esemény időpontja jellege tájékoztatást nyújt. Emellett az e-mailt lehetséges okok és a javasolt műveletek vizsgálatához és az adatbázis következő potenciális fenyegetések csökkentésében.

![Rendellenes tevékenységgel kapcsolatos jelentés](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Kattintson a **legújabb SQL-riasztások megtekintése** indítsa el az Azure-portálon, és aktív fenyegetéseket észlelt áttekintést nyújt az SQL Database az Azure Security Center riasztások lap megjelenítése az e-mailben szereplő hivatkozásra.

   ![Activty fenyegetések](./media/sql-database-threat-detection/active_threats.png)

2. Kattintson a további részletek és a műveletek lekérése vizsgálja az ilyen veszélyek kockázatát, és a jövőbeli fenyegetések szervizelés egy meghatározott riasztásra.

   Például SQL-injektálás az egyik a leggyakrabban használt webes alkalmazás biztonsági problémákra, amellyel az adatvezérelt alkalmazások támadás az interneten. A támadók előnyeit alkalmazás biztonsági rések rosszindulatú SQL-utasítások alkalmazás mezőkbe szúrjon megsértése vagy módosítása az adatbázis adatai. Az SQL-injektálás riasztásokat a riasztás részletei tartalmazza azokat az sebezhető SQL-utasítás kihasználni.

   ![Adott riasztás](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Megismerheti a figyelmeztetések az adatbázis az Azure-portálon

SQL-adatbázis Fenyegetésészlelés integrálja a riasztások [az Azure Security Center](https://azure.microsoft.com/services/security-center/). Az adatbázis és az Azure portálon SQL ATP paneleken élő SQL-threat észlelési csempék aktív fenyegetések állapotának nyomon követi.

Kattintson a **fenyegetés észleléséről szóló figyelmeztetés a** elindíthatja az Azure Security Center riasztások lapon, és a rendszer észlelt az adatbázis aktív SQL fenyegetések áttekintést kaphat.

   ![Fenyegetés-észlelési riasztás](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Fenyegetések észlelése alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Az Azure SQL adatbázis Fenyegetésészlelés riasztások 
A Fenyegetésészlelés az Azure SQL Database szokatlan és potenciálisan káros kísérletek eléréséhez, vagy a biztonsági rések elleni adatbázisok jelző rendellenes tevékenységek észleli, és azt is elindíthatja az alábbi riasztások:
- **Biztonsági rés az SQL-injektálás számára**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázisban. Ez az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:
   - Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
   - Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
- **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan Azure-adatközpontból jelentkezett be az SQL-kiszolgálóra, ami a kiszolgálón is látható volt a közelmúltban. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új Azure-, Power BI- vagy Azure SQL Query Editor-alkalmazást). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan résztvevő (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>További lépések

* További információ [SQL Advanced Threat Protection](sql-advanced-threat-protection.md). 
* További információ [Azure SQL Database Auditing](sql-database-auditing.md)
* További információ [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Az árakkal kapcsolatos további információkért lásd: a [lap SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)  
