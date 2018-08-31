---
title: Fenyegetések észlelése – Azure SQL Data Warehouse |} A Microsoft Docs
description: Fenyegetésészlelés konfigurálása, és ismerje meg az Azure SQL Data Warehouse gyanús eseményeket.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 7ff23235e9681301984e13e346b23f277662bb5c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300934"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse fenyegetésészlelése
Fenyegetésészlelés konfigurálása, és ismerje meg az Azure SQL Data Warehouse gyanús eseményeket.

## <a name="what-is-threat-detection"></a>Mi az a fenyegetésészlelés
Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz. 

A fenyegetésészlelés új biztonsági réteget jelent, amely a rendellenes tevékenységekre adott riasztásokkal teszi lehetővé, hogy az ügyfelek bekövetkezésük pillanatában észlelhessék a vélhető fenyegetéseket, és reagálhassanak azokra. Felhasználók a gyanús események használatával megvizsgálhatja [Azure SQL Data Warehouse-naplózás](sql-data-warehouse-auditing-overview.md) meghatározni, ha azok eredmény eléréséhez, illetéktelen behatolás vagy biztonsági rés kiaknázása elleni adatokat az adattárház tett kísérlet.
A Fenyegetésészlelés is egyszerűvé teszi cím potenciális fenyegetések, anélkül, hogy szakértői biztonsági szakértelem vagy fejlett biztonsági kellene az adatraktárba.

A fenyegetésészlelés például egyes, az adatbázist érintő rendellenes tevékenységeket észlel, amelyek lehetséges SQL-injektálási kísérleteket jelezhetnek. Az SQL-injektálás az egyik leggyakoribb webalkalmazás-biztonsági probléma az interneten, a használatával adatvezérelt alkalmazásokat támadnak meg. Az alkalmazások biztonsági réseinek kihasználásával a támadók rosszindulatú SQL-utasításokat injektálhatnak az alkalmazás beviteli mezőibe az adatbázisban található adatokkal való visszaéléshez vagy azok módosításához.

## <a name="set-up-threat-detection-for-your-database"></a>A fenyegetésészlelés, az adatbázis beállítása
1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a figyelni kívánt, az SQL Data Warehouse konfigurációs paneljére. A Beállítások panelen válassza a **Naplózás és fenyegetésészlelés** elemet.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. Az a **naplózás és Fenyegetésészlelés** konfigurációs panelen kapcsolja **ON** naplózás, amely megjeleníti a Fenyegetésészlelési beállításokat.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Kapcsolja be **ON** fenyegetések észlelése.
5. Konfigurálhatja a listáját, amely az adatraktár rendellenes tevékenységek észlelésekor biztonsági riasztást fog kapni e-maileket.
6. Kattintson a **mentése** a a **naplózás és Fenyegetésészlelés** mentse az új vagy frissített naplózási és fenyegetésészlelési szabályzat konfigurálása panel.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Ismerkedés a rendellenes az adatraktár tevékenységeket a gyanús esemény észlelése
1. Rendellenes adatbázis-tevékenységek észlelésekor e-mail-értesítést fog kapni. <br/>
   Az e-mail információkat tartalmaz a gyanús biztonsági eseményről, beleértve a rendellenes tevékenységek jellegét, az adatbázis és a kiszolgáló nevét, valamint az esemény időpontját. Az e-mail ezen kívül ismerteti a lehetséges okokat, illetve a lehetséges adatbázis-fenyegetések kivizsgálására és elhárítására javasolt műveleteket is.<br/>
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. Az e-mailt, kattintson a **Azure SQL-napló** hivatkozás, amely az Azure-portál indításához és a gyanús esemény bekövetkezésének időpontja környékén rögzített kapcsolódó naplózási rekordok megjelenítéséhez.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Kattintson a naplórekordokra kattintva további részleteket a gyanús adatbázis-tevékenységekről, például az SQL-utasítással, a hiba okáról és az ügyfél IP.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. Az Auditing Records (Naplózási rekordok) panelen kattintson a **Megnyitás Excelben** lehetőségre egy előre konfigurált Excel-sablon megnyitásához a gyanús esemény bekövetkezésének időpontja környékén rögzített naplók importálásához és részletesebb elemzéséhez.<br/>
   **Megjegyzés:** az Excel 2010 vagy újabb Power Query és a **gyors összevonás** beállítás megadása kötelező
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. A **Gyors összevonás** beállítás konfigurálása – a **POWER QUERY** menüszalagján válassza a **Beállítások** elemet a Beállítások párbeszédpanel megjelenítéséhez. Válassza az Adatvédelem szakaszt, majd válassza a második lehetőséget – „A teljesítmény lehetséges javítása az adatvédelmi szintek figyelmen kívül hagyásával”:
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Az SQL-naplók betöltéséhez ellenőrizze, hogy megfelelően állította-e be a Beállítások lap paramétereit, majd válassza ki az „Adatok” menüszalagot, és kattintson „Az összes frissítése” elemre.
   
    ![Navigációs ablaktábla](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Az eredmények az **SQL Audit Logs** (SQL-naplók) lapon jelennek meg, ahol részletesebb elemzésnek vetheti alá az észlelt rendellenes tevékenységeket, és csökkentheti a biztonsági események hatását az alkalmazásban.

## <a name="next-steps"></a>További lépések
Biztonsági kapcsolatos további információkért lásd: [egy data warehouse adatraktár biztonságossá tétele](sql-data-warehouse-overview-manage-security.md).
