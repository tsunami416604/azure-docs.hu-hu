---
title: "Veszélyforrások Detektálása - Azure SQL adatbázis |} Microsoft Docs"
description: "A fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket észleli, amelyek esetleges biztonsági fenyegetéseket jelezhetnek."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: shaik
editor: v-romcal
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 06/19/2017
ms.author: ronmat
ms.openlocfilehash: 889f65a796aee20d7902964b8c47af46dd9149cb
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="sql-database-threat-detection"></a>SQL adatbázis fenyegetések észlelése

SQL Fenyegetésészlelés észleli a rendellenes tevékenységek jelző szokatlan és potenciálisan káros kísérletek eléréséhez, vagy adatbázisok kihasználására.

## <a name="overview"></a>Áttekintés

A Fenyegetésészlelés SQL biztonsági, amelyek segítségével a felhasználók észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások a rendellenes tevékenységek reagáljon a lehetséges veszélyforrásokra egy új réteget biztosít.  Felhasználók a rendszer riasztást adatbázis gyanús tevékenységeket, a potenciális biztonsági réseket, és a SQL injektálási támadások, valamint rendellenes adatbázis memóriahozzáférési mintáitól. SQL Fenyegetésészlelés riasztásokat gyanús tevékenység részleteinek megadása, és vizsgálja meg, és a fenyegetések mérséklésére művelet javasolja. Felhasználók felfedezheti a gyanús eseményeket [SQL Database Auditing](sql-database-auditing.md) meghatározni, ha azok hozzáférést, megsértik a, vagy az adatbázis adatai kihasználásához kísérlet eredménye. A Fenyegetésészlelés egyszerűen potenciális fenyegetések ellen az adatbázisba történő szakértői biztonsági vagy speciális biztonsági rendszerek figyelése kezelése nélkül.

Például SQL-injektálás az egyik a közös webes alkalmazás biztonsági problémák elleni támadásra adatvezérelt alkalmazások, az interneten. A támadók előnyeit alkalmazás biztonsági rések rosszindulatú SQL-utasítások alkalmazás mezőkbe szúrjon megsértése vagy módosítása az adatbázis adatai.

SQL Fenyegetésészlelés integrálja riasztások [az Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/), és minden védett SQL adatbázis-kiszolgáló lesz számlázva, Azure Security Center szabványos rétegként $15/csomópont/hónappal, ahol minden védett SQL-adatbázis ugyanazon az áron kiszolgáló egy csomópont számítanak.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>A fenyegetésészlelés az adatbázis az Azure portálon beállítása
1. Indítsa el az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).
2. Keresse meg a figyelni kívánt SQL-adatbázis-konfiguráció oldalán. Válassza a beállítások lapon **naplózás és Fenyegetésészlelés**. 
    ![Navigációs ablaktábla][1]
3. Az a **naplózás és Fenyegetésészlelés** konfigurációs lapján kapcsolja **ON** naplózási, amelyek megjelenítik a fenyegetés észlelési beállítások.
  
    ![Navigációs ablaktábla][2]
4. Kapcsolja be **ON** veszélyforrások detektálása.
5. Konfigurálhatja az e-maileket a biztonsági riasztások adatbázist érintő rendellenes tevékenységeket észlelése listáját.
6. Kattintson a **mentése** a a **naplózási & Threat detection** lap az új vagy frissített naplózás és a fenyegetések észlelése beállításainak mentése.
       
    ![Navigációs ablaktábla][3]

## <a name="set-up-threat-detection-using-powershell"></a>Állítsa be a fenyegetésészlelés PowerShell használatával

Tekintse meg a parancsfájl például [konfigurálhatja a naplózás és a fenyegetések észlelésére, a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Fedezze fel az adatbázist érintő rendellenes tevékenységeket gyanús esemény észlelése
1. A adatbázist érintő rendellenes tevékenységeket észlelésekor e-mailben értesítést kapni. <br/>
   Az e-mailt a gyanús biztonsági esemény, például a rendellenes tevékenységek, adatbázisnév, kiszolgálónév, az alkalmazásnév és az esemény időpontja jellege tájékoztatást nyújt. Emellett az e-mailt lehetséges okok és a javasolt műveletek vizsgálatához és az adatbázis következő potenciális fenyegetések csökkentésében.<br/>
     
    ![Navigációs ablaktábla][4]
2. Az e-mail riasztások az SQL-napló mutató közvetlen hivatkozást tartalmaz. Erre a hivatkozásra kattintva elindítja az Azure-portálon, és megnyílik az SQL rekordjait a gyanús esemény környékén. Kattintson a további részletek megtekintéséhez a adatbázis gyanús tevékenységekről, így könnyebben végrehajtódtak SQL-utasítások található egy naplórekordot (ki fért, tevékenységük és mikor) és döntse el, ha az esemény jogos vagy rosszindulatú volt-e (pl. alkalmazások biztonsági rés az SQL-injektálás kihasználni, valaki megszegése bizalmas adatokat, stb.).<br/>
   ![Navigációs ablaktábla][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Megismerheti a figyelmeztetések az adatbázis az Azure-portálon

SQL-adatbázis Fenyegetésészlelés integrálja a riasztások [az Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/). Egy élő SQL biztonság csempe az Azure-portálon az adatbázis Page aktív fenyegetések állapotát követi nyomon. 

   ![Navigációs ablaktábla][6]
   
1. Ehhez kattintson a az SQL biztonsági csempe elindítja az Azure Security Center riasztások lapján, majd a rendszer észlelt az adatbázis aktív SQL fenyegetések áttekintése. 

  ![Navigációs ablaktábla][7]

2. Egy meghatározott riasztásra kattintva jelenít meg további adatokat, és vizsgálja az ilyen veszélyek kockázatát, és a jövőbeli fenyegetések szervizelés műveletek.

  ![Navigációs ablaktábla][8]


## <a name="next-steps"></a>További lépések

* További információ a Fenyegetésészlelés, látogasson el a [Azure blog](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* További információ [Azure SQL Database Auditing](sql-database-auditing.md)
* További információ [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Az árakkal kapcsolatos további részletekért lásd: a [lap SQL Database – díjszabás](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
* Tekintse meg a PowerShell-mintaparancsfájl [konfigurálhatja a naplózás és a fenyegetések észlelésére, a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


