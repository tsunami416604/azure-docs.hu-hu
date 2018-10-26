---
title: Fenyegetések észlelése – az Azure SQL Database |} A Microsoft Docs
description: Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek jelzi az adatbázis egy önálló adatbázis vagy a rugalmas készletet az esetleges biztonsági fenyegetéseket.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 10/25/2018
ms.openlocfilehash: 2882bd782359697cf714214e68166c9f997f52e4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093547"
---
# <a name="azure-sql-database-threat-detection"></a>Az Azure SQL Database Threat Detection

Az Azure SQL Fenyegetésészlelési [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek.

A Fenyegetésészlelés része a [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md) (ATP) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A Fenyegetésészlelés érheti el és a központi SQL ATP portálján felügyelt.


> [!NOTE] 
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.


## <a name="what-is-threat-detection"></a>Mi az a Fenyegetésészlelés?

SQL-fenyegetésészlelés egy új réteget jelent, amely lehetővé teszi az ügyfelek bekövetkezésük pillanatában biztonsági riasztások révén a rendellenes tevékenységekre adott reagáljon a lehetséges veszélyforrásokra, és. A felhasználók kapnak riasztás esetén a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, és az SQL-injektálási támadások, valamint a rendellenes adatbázis-hozzáférés, és lekérdezi a minták. Az SQL Fenyegetésészlelési riasztásokat az integrálható [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely tartalmazza a gyanús tevékenység és a javasolt művelet vizsgálata, valamint a fenyegetés részleteit. Az SQL Fenyegetésészlelési is egyszerűvé teszi cím potenciális fenyegetések, az adatbázis szakértői biztonsági szakértelem vagy fejlett biztonsági monitorozási rendszerek felügyelete nélkül. 

Teljes vizsgálat biztosítása érdekében, javasoljuk, hogy engedélyezze [SQL Database naplózási szolgáltatásával](sql-database-auditing.md), amely írja az adatbázissal kapcsolatos események egy naplófájlba, jelentkezzen be az Azure storage-fiókjában.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Állítsa be az Azure Portalon az adatbázishoz tartozó fenyegetések észlelése
1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a konfigurációs lapon, a védeni kívánt Azure SQL Database-kiszolgáló. Válassza ki a biztonsági beállítások **komplex veszélyforrások elleni védelem**.
3. Az a **komplex veszélyforrások elleni védelem** konfigurációs lapon:

   - Komplex veszélyforrások elleni védelem engedélyezése a kiszolgálón.
   - A **Fenyegetésészlelési beállításokat**, a a **küldje a riasztásokat** szöveget adja meg a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld e-mailek listáját.
  
   ![Állítsa be a fenyegetések észlelése](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Állítsa be a PowerShell-lel fenyegetésészlelés

A parancsfájl példa: [PowerShell-lel, naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Ismerkedés a rendellenes adatbázis-tevékenységek észlelésekor a gyanús esemény

Rendellenes adatbázis-tevékenységek észlelésekor e-mail-értesítést kap. Az e-mailt a gyanús biztonsági eseményről, beleértve az esetleges rendellenes tevékenységekről, adatbázisnevet, kiszolgáló neve, alkalmazás neve és az esemény időpontját jellege nyújt tájékoztatást. Emellett az e-mailt információkat biztosít a lehetséges okokat, és javasolt műveleteket is vizsgálata és enyhítése érdekében az esetleges adatbázis-fenyegetések.

![Rendellenes tevékenység jelentés](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Kattintson a **SQL legutóbbi riasztás megtekintése** hivatkozásra az e-mailben, megnyílik az Azure portal és az aktív fenyegetéseket észlel áttekintést nyújt az SQL database az Azure Security Center riasztások lap megjelenítése.

   ![Activty fenyegetések](./media/sql-database-threat-detection/active_threats.png)

2. Kattintson egy adott riasztás kivizsgálása a fenyegetés és a jövőbeli fenyegetésekkel szembeni szervizelését további részletek és műveletek beolvasásához.

   Például SQL-injektálás az egyik a leggyakoribb webes biztonsági probléma az interneten, amellyel adatvezérelt alkalmazások támadásokkal szemben. A támadók előnyeit alkalmazások biztonsági réseinek rosszindulatú SQL-utasításokat injektálhatnak alkalmazás beviteli mezőibe visszaéléshez vagy azok az adatbázisban található adatok módosításához. SQL-injektálás riasztások a riasztás részleteit tartalmazza a sebezhető kihasználni SQL-utasítás.

   ![Adott riasztás](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Ismerkedés a fenyegetésészlelési riasztásokat az adatbázis az Azure Portalon

Az SQL Database Threat Detection a riasztásokat az integrálható [az Azure Security Center](https://azure.microsoft.com/services/security-center/). Egy élő SQL fenyegetés észlelése csempék belül az adatbázis és az Azure Portal SQL ATP paneleket az aktív fenyegetéseket állapotát követi nyomon.

Kattintson a **fenyegetés-észlelési riasztás** , indítsa el az Azure Security Center riasztások lapon, és áttekintheti a aktív SQL észlelt veszélyforrások alapján az adatbázist, sem az adattárházra.

   ![Fenyegetés-észlelési riasztás](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Fenyegetés-észlelési alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Az Azure SQL Database Fenyegetésészlelési riasztások 
Az Azure SQL Database Fenyegetésészlelés észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek, és azt is aktiválhatja a következő riasztásokat:
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

* Tudjon meg többet [SQL komplex veszélyforrások elleni védelem](sql-advanced-threat-protection.md). 
* Tudjon meg többet [Azure SQL Database naplózási szolgáltatásával](sql-database-auditing.md)
* Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Díjszabási információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/)  
