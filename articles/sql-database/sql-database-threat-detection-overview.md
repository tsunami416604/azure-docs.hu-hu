---
title: Fenyegetések észlelése – az Azure SQL Database |} A Microsoft Docs
description: Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az Azure SQL Database-adatbázishoz.
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
ms.date: 01/29/2019
ms.openlocfilehash: 2f52cbaf5d50eca13a87ff8e745b0f8baefbe23e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210369"
---
# <a name="azure-sql-database-threat-detection"></a>Az Azure SQL Database Threat Detection

Az Azure SQL Fenyegetésészlelési [Azure SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek.

Része a Fenyegetésészlelés az [SQL speciális adatbiztonság](sql-advanced-threat-protection.md) (ADS) ajánlat, amely egységes csomag egy tapasztalt SQL biztonsági funkciók. A Fenyegetésészlelés érheti el és a központi SQL ADS portálon keresztül kezelhetők.

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.


## <a name="what-is-threat-detection"></a>Mi az a Fenyegetésészlelés

SQL-fenyegetésészlelés egy új réteget jelent, amely lehetővé teszi az ügyfelek bekövetkezésük pillanatában biztonsági riasztások révén a rendellenes tevékenységekre adott reagáljon a lehetséges veszélyforrásokra, és. A felhasználók kapnak riasztás esetén a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, és az SQL-injektálási támadások, valamint a rendellenes adatbázis-hozzáférés, és lekérdezi a minták. Az SQL Fenyegetésészlelési riasztásokat az integrálható [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely tartalmazza a gyanús tevékenység és a javasolt művelet vizsgálata, valamint a fenyegetés részleteit. Az SQL Fenyegetésészlelési is egyszerűvé teszi cím potenciális fenyegetések, az adatbázis szakértői biztonsági szakértelem vagy fejlett biztonsági monitorozási rendszerek felügyelete nélkül.

Teljes vizsgálat biztosítása érdekében, javasoljuk, hogy engedélyezze [SQL Database naplózási szolgáltatásával](sql-database-auditing.md), amely írja az adatbázissal kapcsolatos események egy naplófájlba, jelentkezzen be az Azure storage-fiókjában.  

## <a name="azure-sql-database-threat-detection-alerts"></a>Az Azure SQL Database Fenyegetésészlelési riasztások

Az Azure SQL Database Fenyegetésészlelés észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek, és azt is aktiválhatja a következő riasztásokat:

- **Biztonsági rés az SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázis. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:

  - Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
  - Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
- **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy olyan azonosított alkalmazás biztonsági rés az SQL-injektálás elleni történik. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan földrajzi helyről hozzáférési mintájában változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-beli adat központból**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server ezen a kiszolgálón a legutóbbi időszakban látott szokatlan Azure-beli adat központból hozzáférési mintájában változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új Azure-, Power BI- vagy Azure SQL Query Editor-alkalmazást). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan résztvevő (SQL-felhasználó) használatával hozzáférési mintájában változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan káros alkalmazás**: Ez a riasztás akkor aktiválódik, ha egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos támadás SQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha van egy rendellenes magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Ismerkedés a rendellenes adatbázis-tevékenységek észlelésekor a gyanús esemény

Rendellenes adatbázis-tevékenységek észlelésekor e-mail-értesítést kap. Az e-mailt a gyanús biztonsági eseményről, beleértve az esetleges rendellenes tevékenységekről, adatbázisnevet, kiszolgáló neve, alkalmazás neve és az esemény időpontját jellege nyújt tájékoztatást. Emellett az e-mailt információkat biztosít a lehetséges okokat, és javasolt műveleteket is vizsgálata és enyhítése érdekében az esetleges adatbázis-fenyegetések.

![Rendellenes tevékenység jelentés](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kattintson a **SQL legutóbbi riasztás megtekintése** hivatkozásra az e-mailben, megnyílik az Azure portal és az aktív fenyegetéseket észlel áttekintést nyújt az SQL database az Azure Security Center riasztások lap megjelenítése.

   ![Tevékenység fenyegetések](./media/sql-database-threat-detection/active_threats.png)

2. Kattintson egy adott riasztás kivizsgálása a fenyegetés és a jövőbeli fenyegetésekkel szembeni szervizelését további részletek és műveletek beolvasásához.

   Például SQL-injektálás az egyik a leggyakoribb webes biztonsági probléma az interneten, amellyel adatvezérelt alkalmazások támadásokkal szemben. A támadók előnyeit alkalmazások biztonsági réseinek rosszindulatú SQL-utasításokat injektálhatnak alkalmazás beviteli mezőibe visszaéléshez vagy azok az adatbázisban található adatok módosításához. SQL-injektálás riasztások a riasztás részleteit tartalmazza a sebezhető kihasználni SQL-utasítás.

   ![Adott riasztás](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Ismerkedés a fenyegetésészlelési riasztásokat az adatbázis az Azure Portalon

Az SQL Database Threat Detection a riasztásokat az integrálható [az Azure Security Center](https://azure.microsoft.com/services/security-center/). Az élő SQL threat detection csempék belül az adatbázis és az SQL-HIRDETÉSEK paneleket az Azure Portalon aktív fenyegetéseket állapotának nyomon követése.

Kattintson a **fenyegetés-észlelési riasztás** , indítsa el az Azure Security Center riasztások lapon, és áttekintheti a aktív SQL észlelt veszélyforrások alapján az adatbázist, sem az adattárházra.

   ![Fenyegetés-észlelési riasztás](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Fenyegetés-észlelési alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [egy adatbázis Fenyegetésészlelési](sql-database-threat-detection.md).
- Tudjon meg többet [a felügyelt példány Fenyegetésészlelési](sql-database-managed-instance-threat-detection.md).
- Tudjon meg többet [SQL biztonságú adatok](sql-advanced-threat-protection.md).
- Tudjon meg többet [Azure SQL Database naplózási szolgáltatásával](sql-database-auditing.md)
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Díjszabási információkért tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/)  
