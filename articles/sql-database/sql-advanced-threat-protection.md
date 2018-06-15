---
title: Komplex veszélyforrások elleni védelem – az Azure SQL adatbázis |} Microsoft Docs
description: További információk a funkciókat felderítéséhez és bizalmas adatok osztályozásához, az adatbázis biztonsági rések kezelése és oka lehet az Azure SQL-adatbázis fenyegetést rendellenes tevékenységek észlelésekor.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305446"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Az Advanced Threat Protection az Azure SQL-adatbázis

SQL Advanced Threat Protection egyesített csomag egy speciális SQL biztonsági képességeinek. Ez magában foglalja a funkciókat felderítéséhez és bizalmas adatok osztályozásához, az adatbázis biztonsági rések kezelése és oka lehet az adatbázis fenyegetést rendellenes tevékenységek észlelésekor. Egyetlen nyissa meg a hely engedélyezése és kezelése ezeket a képességeket biztosítja. 

## <a name="overview"></a>Áttekintés

SQL speciális Threat Protection (ATP) tartalmaz egy speciális SQL biztonsági képességei, beleértve az adatok felderítési és a besorolás, a biztonsági réseinek értékelése és a fenyegetések észlelése. 

- [Felderítési adatok & besorolás](sql-database-data-discovery-and-classification.md) (jelenleg előzetes verzió) Azure SQL Database beépített a felderítése, zárolásának, címkézés és az adatbázisok a bizalmas adatok védelmének képességeket biztosít. Adja meg az adatbázis-besorolás állapota lássák, és a hozzáférés követésére bizalmas adatokat az adatbázisból, és határain használható.
- [Biztonsági réseinek értékelése](sql-vulnerability-assessment.md) egy könnyen konfigurálható felderítésére, nyomon követheti, és segítséget szolgáltatás szervizelése potenciális adatbázis biztonsági réseket. A biztonsági állapot betekintést biztosít, és biztonsági problémák megoldásához, és javíthatja az adatbázis fortifications végrehajtandó lépéseit tartalmazza.
- [Veszélyforrások Detektálása](sql-database-threat-detection.md) szokatlan és potenciálisan káros kísérletek eléréséhez, vagy az adatbázis a biztonsági rések elleni jelző rendellenes tevékenységeket észleli. Folyamatosan figyeli a gyanús tevékenységek az adatbázisba, és ez azonnali biztonsági riasztások a potenciális biztonsági réseket, SQL injektálási támadások és rendellenes adatbázis hozzáférési minták. Figyelmeztetések a gyanús tevékenység részleteinek megadása, és vizsgálja meg, és a fenyegetések mérséklésére művelet javasolja.

Engedélyezése SQL ATP, miután ahhoz, hogy mindegyik szolgáltatásokat tartalmazza. Egy kattintással engedélyezheti ATP a teljes adatbázis-kiszolgálón, a kiszolgálón lévő összes adatbázis alkalmazása. 

Az Azure Security Center standard csomagra $15/csomópont/hónappal, ahol minden védett SQL adatbázis-kiszolgáló egy csomópont számít ATP árképzési igazodik. Az első 60 nap után engedélyezése egy ingyenes próbaidőszak minősülnek, és nem. További információkért lásd: a [az Azure Security Center árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Ismerkedés a ATP 
A következő lépésekkel telepítheti be ATP elkezdésének. 

## <a name="1-enable-atp"></a>1. ATP engedélyezése

ATP engedélyezéséhez navigáljon **Advanced Threat Protection** alatt a **biztonsági** fejléc az Azure SQL Database ablaktáblán. A kiszolgálón lévő összes adatbázis ATP engedélyezéséhez kattintson **engedélyezése Advanced Threat Protection a kiszolgálón**.

![ATP engedélyezése](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> ATP költsége $15/csomópont/hónap, a teljes logikai SQL-kiszolgáló esetén a csomópont. Így fizet csak egyszer ATP a kiszolgálón lévő összes adatbázis védelmére. Az első hatvan minősülnek egy ingyenes próbaverzióra.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurálja a biztonsági réseinek értékelése

Biztonsági réseinek értékelése használatának megkezdéséhez szüksége a vizsgálati eredmények tároló storage-fiókok konfigurálása. Ehhez kattintson a biztonsági réseinek értékelése kártya.

![VA konfigurálása](./media/sql-advanced-protection/configure_va.png) 

Válassza ki, vagy hozzon létre egy tárfiókot a vizsgálati eredmények mentése. Rendszeres időközönként ismétlődő vizsgálatok biztonsági réseinek értékelése hetente egyszer automatikus vizsgálatok futtatásához konfigurálása is kapcsolhatja. Vizsgálati eredmény összefoglalását nem jut hozzá a e-mail címeket, akkor adja meg.

![VA beállításai](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Indítsa el az adatok zárolásának, figyelemmel kíséri, biztonsági rések, és fenyegetést a riasztások kivizsgálásának

Kattintson a **adatok felderítése és besorolása** megtekintéséhez bizalmas oszlopok osztályozására és állandó érzékenységi címkéket az adatok osztályozására szolgáló ajánlott. Kattintson a **biztonsági réseinek értékelése** biztonsági vizsgálatok és a jelentések megtekintéséhez és kezeléséhez, és a biztonsági stature nyomon követésére kártya. Ha a biztonsági riasztások fogadott, kattintson a **Fenyegetésészlelés** kártya megtekintéséhez a riasztások és egy konszolidált jelentés megtekintéséhez az Azure-előfizetéshez az Azure Security Center biztonsági riasztások lapon keresztül az összes riasztás a részletek.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Az SQL Server ATP beállításainak kezelése

Megtekintheti és kezelheti Advanced Threat Protection beállításait, lépjen **Advanced Threat Protection** alatt a **biztonsági** fejléc az SQL server panelen. Ezen a lapon engedélyezze vagy tiltsa le a ATP, és Fenyegetésészlelés beállításainak módosítása a teljes SQL-kiszolgálón.

![Kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. SQL-adatbázis ATP beállításainak kezelése

Egy adott adatbázis ATP Fenyegetésészlelés beállításainak felülbírálása, ellenőrizze a **engedélyezése Advanced Threat Protection az adatbázis szintjén** jelölőnégyzetet. Használja ezt a beállítást, csak ha egy adott követelmény külön figyelmeztetések az egyes-adatbázis helyett vagy annak kiegészítéseként a kiszolgálón lévő összes adatbázis érkezett riasztások fogadásához. 

Ha a jelölőnégyzet be van jelölve, kattintson **Fenyegetésészlelés beállítások** , majd konfigurálja az adatbázis vonatkozó beállítások.

![Adatbázis és a fenyegetések észlelési beállítások](./media/sql-advanced-protection/database_threat_detection_settings.png) 

A kiszolgáló speciális veszélyforrások elleni védelem beállításait is elérhető a ATP adatbázis panelről. Kattintson a **beállítások** a fő ATP ablaktáblán, majd **nézet Advanced Threat Protection kiszolgálóbeállítások**. 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- További információ [adatok felderítési & besorolás](sql-database-data-discovery-and-classification.md) 
- További információ [biztonsági réseinek értékelése](sql-vulnerability-assessment.md) 
- További információ [fenyegetések észlelése](sql-database-threat-detection.md)
- További információ [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
