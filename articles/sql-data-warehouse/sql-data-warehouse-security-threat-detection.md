---
title: "Ismerkedés az SQL Data Warehouse fenyegetések észlelése"
description: "A Fenyegetésészlelés az első lépések"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-threat-detection"></a>A fenyegetésészlelés az első lépései
> [!div class="op_single_selector"]
> * [Naplózás](sql-data-warehouse-auditing-overview.md)
> * [Fenyegetések észlelése](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Áttekintés
A Fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket, amelyek esetleges biztonsági fenyegetéseket jelezhetnek a észleli. A Fenyegetésészlelés preview, és az SQL Data Warehouse támogatja.

A Fenyegetésészlelés biztonsági, amelyek segítségével a felhasználók észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások a rendellenes tevékenységek reagáljon a lehetséges veszélyforrásokra egy új réteget biztosít. Felhasználók felfedezheti a gyanús eseményeket [Azure SQL Data Warehouse-naplózás](sql-data-warehouse-auditing-overview.md) meghatározni, ha azok hozzáférést, megszegi vagy az adatraktárban adatokat a biztonsági rések elleni kísérlet eredménye.
A Fenyegetésészlelés egyszerűen potenciális fenyegetések ellen az adatraktárba történő szakértői biztonsági vagy speciális biztonsági rendszerek figyelése kezelése nélkül.

A Fenyegetésészlelés például bizonyos adatbázist érintő rendellenes tevékenységeket utaló esetleges SQL injektálási kísérletek begyűjtésére észleli. SQL-injektálás az egyik a közös webes alkalmazás biztonsági problémák elleni támadásra adatvezérelt alkalmazások, az interneten. A támadók rosszindulatú SQL-utasítások mezőkbe alkalmazás bejegyzést, megsértése vagy az adatbázis adatai módosításához, szúrjon alkalmazás biztonsági előnyeit.

## <a name="set-up-threat-detection-for-your-database"></a>A fenyegetésészlelés az adatbázis beállítása
1. Indítsa el az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).
2. Nyissa meg a figyelni kívánt SQL-adatraktár tartozó konfigurációs panel. A beállítások panelen válassza ki a **naplózás és Fenyegetésészlelés**.
   
    ![Navigációs ablaktábla][1]
3. Az a **naplózás és Fenyegetésészlelés** konfigurációs panelen kapcsolja **ON** naplózás, amely megjeleníti a fenyegetés észlelési beállítások.
   
    ![Navigációs ablaktábla][2]
4. Kapcsolja be **ON** veszélyforrások detektálása.
5. Konfigurálhatja a listáját, amelyek megkapják a biztonsági riasztások adatraktár rendellenes tevékenységek észlelésekor e-maileket.
6. Kattintson a **mentése** a a **naplózási & Threat detection** konfigurációs panelt, és mentse az új vagy frissített naplózás és a fenyegetés szabályzat.
   
    ![Navigációs ablaktábla][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Megismerkedhet a rendellenes data warehouse tevékenységek egy gyanús esemény észlelése
1. A adatbázist érintő rendellenes tevékenységeket észlelésekor e-mailben értesítést fog kapni. <br/>
   Az e-mailt a gyanús biztonsági esemény, például a rendellenes tevékenységek, a adatbázis neve, a kiszolgáló nevét és az esemény időpontja jellege tájékoztatást fogunk adni. Emellett a lehetséges okok tájékoztatást fogunk adni, és javasolt műveletek vizsgálatához és az adatbázis következő potenciális fenyegetések csökkentésében.<br/>
   
    ![Navigációs ablaktábla][4]
2. Az e-mailt, kattintson a a **Azure SQL-naplózás napló** hivatkozás, amely indítsa el a klasszikus Azure portálon, és a megfelelő naplózási bejegyzések a gyanús esemény környékén megjelenítése.
   
    ![Navigációs ablaktábla][5]
3. Kattintson az auditálási rekordok további részleteket az adatbázis gyanús tevékenységek, például SQL-utasításban, a hiba okát, és az ügyfél IP.
   
    ![Navigációs ablaktábla][6]
4. A naplózási bejegyzések paneljén kattintson **az Excelben** nyissa meg az előre konfigurált excel-sablon importálása és futtatása a napló a gyanús esemény környékén mélyebb elemzése.<br/>
   **Megjegyzés:** Excel 2010 vagy újabb, gépi lekérdezés és a **gyors összevonás** beállításra akkor szükség
   
    ![Navigációs ablaktábla][7]
5. Konfigurálhatja a **gyors összevonás** beállítás - a a **POWER QUERY** menüszalag lapon jelölje be **beállítások** a beállítások párbeszédpanel megjelenítéséhez. Válassza ki az adatvédelmi szakaszt, és válassza a második lehetőség - "És a teljesítmény lehetséges javítása az adatvédelmi szintek figyelmen kívül":
   
    ![Navigációs ablaktábla][8]
6. A betöltés SQL naplókat, ügyeljen arra, hogy a beállítások lapon helyesen van beállítva, és válassza ki a "Data" szalagon, és kattintson a "Az összes frissítés" gombra a paraméterek.
   
    ![Navigációs ablaktábla][9]
7. Az eredmények jelennek meg a **SQL naplók** lap, amely lehetővé teszi a rendellenes tevékenységek talált mélyebb elemzésének futtatja, és csökkenteni a hatását a biztonsági események az alkalmazásban.

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
