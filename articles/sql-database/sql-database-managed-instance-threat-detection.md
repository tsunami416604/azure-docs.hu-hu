---
title: "Veszélyforrások Detektálása - Azure SQL adatbázis felügyelt példány |} Microsoft Docs"
description: "A fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket észleli, amelyek esetleges biztonsági fenyegetéseket jelezhetnek."
services: sql-database
author: rmatchoro
manager: cguyer
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: f8c08bc16d622516958b8bd182179d07edfa4891
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Az Azure SQL adatbázis felügyelt példány fenyegetések észlelése

A Fenyegetésészlelés SQL észleli, hogy szokatlan és potenciálisan káros rendellenes tevékenységek próbál hozzáférni, vagy egy Azure SQL adatbázis felügyelt példány adatbázisának (előzetes verzió) a biztonsági rések elleni.

## <a name="overview"></a>Áttekintés

A Fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket utaló esetleges biztonsági fenyegetéseket jelezhetnek felügyelt példány észleli. A Fenyegetésészlelés preview felügyelt példány van.

A Fenyegetésészlelés biztonsági, amelyek segítségével a felhasználók észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások az adatbázist érintő rendellenes tevékenységeket reagáljon a lehetséges veszélyforrásokra egy új réteget biztosít. A Fenyegetésészlelés egyszerűen potenciális fenyegetések ellen felügyelt példány nincs szükség szakértői biztonsági vagy speciális biztonsági rendszerek figyelése kezelése. A teljes vizsgálat használhatóság érdekében ajánlott naplózásának Azure felügyelt példánya, amely írja a naplózási események adatbázis jelentkezzen be az Azure storage-fiók. 

SQL Fenyegetésészlelés integrálja riasztások [az Azure Security Center](https://azure.microsoft.com/services/security-center/), és minden egyes védett felügyelt példányhoz lesz számlázva, Azure Security Center szabványos rétegként $15/csomópont/hónappal, ahol minden egyes védett felügyelt példány ugyanazon az áron egy csomópont számítanak.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>A Fenyegetésészlelés beállítása a felügyelt példányát az Azure-portálon
1. Indítsa el az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).
2. Keresse meg a védeni kívánt felügyelt példány lap. Az a **beállítások** lapon jelölje be **Fenyegetésészlelés**. 
3. A Fenyegetésészlelés konfigurálása lapon 
   - Kapcsolja be **ON** veszélyforrások detektálása.
   - Konfigurálja a **e-mailek listája** szeretné megkapni a biztonsági riasztásokat adatbázist érintő rendellenes tevékenységeket észlelése.
   - Válassza ki a **Azure storage-fiók** rendellenes fenyegetés auditálási rekordok menteni. 
4.  Kattintson a **mentése** menteni az új vagy frissített fenyegetések észlelése házirendet.

   ![A fenyegetésészlelés](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Megismerkedhet a rendellenes felügyelt példány tevékenységek egy gyanús esemény észlelése

1. A adatbázist érintő rendellenes tevékenységeket észlelésekor e-mailben értesítést kapni. 

   Az e-mailben tájékoztatást ad azokról a gyanús biztonsági esemény, például a rendellenes tevékenységek, az adatbázis neve, a kiszolgáló nevét és a az esemény időpontja jellegét. Ezenkívül információkat nyújt a lehetséges okok, és javasolt műveletek vizsgálatához és a felügyelt példányhoz potenciális fenyegetések csökkentésében.

   ![fenyegetések észlelése e-mail címe](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Kattintson a **legújabb SQL-riasztások megtekintése** az e-mailben, indítsa el az Azure-portálon, és az Azure Security Center riasztások lapon, amely aktív SQL fenyegetéseket észlelt áttekintést nyújt a felügyelt példány adatbázis megjelenítése hivatkozásra.

   ![aktív fenyegetések](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Kattintson a további részletek és a műveletek lekérése vizsgálja az ilyen veszélyek kockázatát, és a jövőbeli fenyegetések szervizelés egy meghatározott riasztásra.

   Például SQL-injektálás az egyik a közös webes alkalmazás biztonsági problémák az interneten. SQL-injektálás az adatvezérelt alkalmazások támadásokkal szolgál. A támadók előnyeit alkalmazás biztonsági rések rosszindulatú SQL-utasítások alkalmazás mezőkbe szúrjon megsértése vagy módosítása az adatbázis adatai. Az SQL-injektálás riasztásokat a riasztás részletei tartalmazza azokat az sebezhető SQL-utasítás kihasználni.

   ![SQL-injektálás](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Felügyelt példány Fenyegetésészlelés riasztások 

Felügyelt példány Fenyegetésészlelés észleli a rendellenes tevékenységek szokatlan és potenciálisan káros kísérletek eléréséhez, vagy a biztonsági rések elleni adatbázisok jelző, és azt is elindíthatja az alábbi riasztások:
- **Biztonsági rés az SQL-injektálás számára**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázisban. Ez az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:
 - Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
 - Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
- **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Azt jelenti, hogy a támadó megpróbálja szúrjon rosszindulatú SQL-utasításokat az sebezhető alkalmazáskód vagy tárolt eljárásokat.
- **Hozzáférés a szokatlan helyről**: Ez a riasztás lesz kiváltva, ha egy felügyelt példányához, ha valaki bejelentkezett felügyelt példány szokatlan földrajzi helyről hozzáférési mintázatát módosult. Bizonyos esetekben a riasztás észleli a jogos művelet (egy új alkalmazást vagy fejlesztői karbantartási műveletet.). Más esetekben a riasztás észlel egy rosszindulatú műveletet (korábbi alkalmazott, külső támadó, és így tovább).
- **A hozzáférést a szokatlan Azure-adatközpont**: Ez a riasztás lesz kiváltva, ha egy felügyelt példányához, ha valaki bejelentkezett felügyelt példány egy Azure-adatközpont, amely nem fér hozzá a felügyelt fordult elő a hozzáférési mintázatát módosult A legutóbbi időszakban példány. Bizonyos esetekben a riasztás észleli a jogos művelet (az új alkalmazást az Azure, a Power BI, Azure SQL lekérdezés-szerkesztő, és így tovább). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Ismeretlen egyszerű hozzáférést**: Ez a riasztás lesz kiváltva, ha egy felügyelt server-példány, ha valaki bejelentkezett a felügyelt példányhoz egy szokatlan egyszerű (SQL-felhasználó) használatával történő hozzáférési mintázatát módosult. Bizonyos esetekben a riasztás észleli a jogos művelet (új alkalmazás fejlesztője karbantartási műveletet.). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás találgatásos támadással észleli.

## <a name="next-steps"></a>További lépések

- Tudnivalók a Managed példány című [Mi az, hogy a felügyelt példánya](sql-database-managed-instance.md)
- További információ [példány naplózás kezelése](https://go.microsoft.com/fwlink/?linkid=869430) 
- További információ [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
