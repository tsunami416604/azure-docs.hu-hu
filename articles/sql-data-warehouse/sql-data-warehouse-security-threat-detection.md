---
title: Veszélyforrások detektálása - Azure SQL Data Warehouse |} Microsoft Docs
description: A fenyegetésészlelés konfigurálása, és vizsgálja meg az Azure SQL Data Warehouse gyanús események.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 8dc1ef0432536c6bfd4fe069406cd057ca069ea2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523897"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse fenyegetések észlelése
A fenyegetésészlelés konfigurálása, és vizsgálja meg az Azure SQL Data Warehouse gyanús események.

## <a name="what-is-threat-detection"></a>Mi az a fenyegetésészlelés
A fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket észleli, amelyek esetleges biztonsági fenyegetéseket jelezhetnek. 

A fenyegetésészlelés új biztonsági réteget jelent, amely a rendellenes tevékenységekre adott riasztásokkal teszi lehetővé, hogy az ügyfelek bekövetkezésük pillanatában észlelhessék a vélhető fenyegetéseket, és reagálhassanak azokra. Felhasználók felfedezheti a gyanús eseményeket [Azure SQL Data Warehouse-naplózás](sql-data-warehouse-auditing-overview.md) meghatározni, ha azok hozzáférést, megszegi vagy az adatraktárban adatokat a biztonsági rések elleni kísérlet eredménye.
A Fenyegetésészlelés egyszerűen potenciális fenyegetések ellen az adatraktárba történő szakértői biztonsági vagy speciális biztonsági rendszerek figyelése kezelése nélkül.

A fenyegetésészlelés például egyes, az adatbázist érintő rendellenes tevékenységeket észlel, amelyek lehetséges SQL-injektálási kísérleteket jelezhetnek. Az SQL-injektálás az egyik leggyakoribb webalkalmazás-biztonsági probléma az interneten, a használatával adatvezérelt alkalmazásokat támadnak meg. Az alkalmazások biztonsági réseinek kihasználásával a támadók rosszindulatú SQL-utasításokat injektálhatnak az alkalmazás beviteli mezőibe az adatbázisban található adatokkal való visszaéléshez vagy azok módosításához.

## <a name="set-up-threat-detection-for-your-database"></a>A fenyegetésészlelés az adatbázis beállítása
1. Indítsa el az Azure portálon, a [ https://portal.azure.com ](https://portal.azure.com).
2. Nyissa meg a figyelni kívánt SQL-adatraktár tartozó konfigurációs panel. A Beállítások panelen válassza a **Naplózás és fenyegetésészlelés** elemet.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. Az a **naplózás és Fenyegetésészlelés** konfigurációs panelen kapcsolja **ON** naplózás, amely megjeleníti a fenyegetés észlelési beállítások.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Kapcsolja be **ON** veszélyforrások detektálása.
5. Konfigurálhatja a listáját, amelyek megkapják a biztonsági riasztások adatraktár rendellenes tevékenységek észlelésekor e-maileket.
6. Kattintson a **mentése** a a **naplózási & Threat detection** konfigurációs panelt, és mentse az új vagy frissített naplózás és a fenyegetés szabályzat.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Megismerkedhet a rendellenes data warehouse tevékenységek egy gyanús esemény észlelése
1. A adatbázist érintő rendellenes tevékenységeket észlelésekor e-mailben értesítést fog kapni. <br/>
   Az e-mail információkat tartalmaz a gyanús biztonsági eseményről, beleértve a rendellenes tevékenységek jellegét, az adatbázis és a kiszolgáló nevét, valamint az esemény időpontját. Az e-mail ezen kívül ismerteti a lehetséges okokat, illetve a lehetséges adatbázis-fenyegetések kivizsgálására és elhárítására javasolt műveleteket is.<br/>
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. Az e-mailt, kattintson a a **Azure SQL-naplózás napló** hivatkozás, amely az Azure-portálon elindul, és a megfelelő naplózási bejegyzések a gyanús esemény környékén megjelenítése.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Kattintson az auditálási rekordok további részleteket az adatbázis gyanús tevékenységek, például SQL-utasításban, a hiba okát, és az ügyfél IP.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. Az Auditing Records (Naplózási rekordok) panelen kattintson a **Megnyitás Excelben** lehetőségre egy előre konfigurált Excel-sablon megnyitásához a gyanús esemény bekövetkezésének időpontja környékén rögzített naplók importálásához és részletesebb elemzéséhez.<br/>
   **Megjegyzés:** Excel 2010 vagy újabb, gépi lekérdezés és a **gyors összevonás** beállításra akkor szükség
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. A **Gyors összevonás** beállítás konfigurálása – a **POWER QUERY** menüszalagján válassza a **Beállítások** elemet a Beállítások párbeszédpanel megjelenítéséhez. Válassza az Adatvédelem szakaszt, majd válassza a második lehetőséget – „A teljesítmény lehetséges javítása az adatvédelmi szintek figyelmen kívül hagyásával”:
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Az SQL-naplók betöltéséhez ellenőrizze, hogy megfelelően állította-e be a Beállítások lap paramétereit, majd válassza ki az „Adatok” menüszalagot, és kattintson „Az összes frissítése” elemre.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Az eredmények az **SQL Audit Logs** (SQL-naplók) lapon jelennek meg, ahol részletesebb elemzésnek vetheti alá az észlelt rendellenes tevékenységeket, és csökkentheti a biztonsági események hatását az alkalmazásban.

## <a name="next-steps"></a>További lépések
Biztonsági kapcsolatos további információkért lásd: [adatraktár biztonságos](sql-data-warehouse-overview-manage-security.md).
