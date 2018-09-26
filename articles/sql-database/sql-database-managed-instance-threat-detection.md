---
title: Fenyegetések észlelése – az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések a felügyelt példány az adatbázishoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 7f05bd6c96a8e22b1e039c3edcec491b7c079d54
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162409"
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Az Azure SQL Database felügyelt példány Fenyegetésészlelési

Az SQL Fenyegetésészlelés észleli az Azure SQL Database felügyelt példánya az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek.

## <a name="overview"></a>Áttekintés

Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések felügyelt példányon. A Fenyegetésészlelés már felügyelt példány előzetes verzióban érhető el.

A fenyegetésészlelés egy új réteget jelent, amely lehetővé teszi az ügyfelek és azok felmerülésekor, rendellenes adatbázis-tevékenységek a biztonsági riasztások révén reagáljon a lehetséges veszélyforrásokra. A Fenyegetésészlelés egyszerűen a lehetséges veszélyforrásokra cím a felügyelt példánynak nincs szükség a szakértői biztonsági szakértelem vagy fejlett biztonsági. Teljes vizsgálat biztosítása érdekében ajánlott az Azure által felügyelt példány a naplózás engedélyezéséhez, amely adatbázissal kapcsolatos események egy naplófájlba, jelentkezzen be az Azure storage-fiókjába ír. 

Az SQL Fenyegetésészlelési riasztásokat az integrálható [az Azure Security Center](https://azure.microsoft.com/services/security-center/), és minden védett felügyelt példány díját ugyanaz, mint az Azure Security Center Standard csomagja, $15/csomópont/hó, ahol minden egyes védett felügyelt példányt: egyetlen csomópontnak számítanak.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Az Azure Portalon a felügyelt példány Fenyegetésészlelési beállítása
1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg a felügyelt példány számára védelmet kíván konfiguráció lapján. Az a **beállítások** lapon jelölje be **Fenyegetésészlelés**. 
3. A Fenyegetésészlelés konfigurációs lapja 
   - Kapcsolja be **ON** fenyegetések észlelése.
   - Konfigurálja a **e-mailek listájának** a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld.
   - Válassza ki a **Azure storage-fiók** rendellenes threat naplózási bejegyzések mentési helye. 
4.  Kattintson a **mentése** menteni az új vagy frissített fenyegetésészlelési szabályzatát.

   ![Fenyegetések észlelése](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Fedezze fel a gyanús esemény észlelése rendellenes Felügyeltpéldány-tevékenységek

1. Rendellenes adatbázis-tevékenységek észlelésekor e-mail-értesítést kap. 

   Az e-mailt a gyanús biztonsági eseményről, beleértve az esetleges rendellenes tevékenységekről, adatbázisnevet, kiszolgáló nevét és az esemény időpontját jellege információkat biztosít. Ezenkívül információkat biztosít a lehetséges okokat, és javasolt műveletek vizsgálata és enyhítése érdekében a felügyelt példánynak a potenciális fenyegetést.

   ![fenyegetések észlelése – e-mail](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Kattintson a **SQL legutóbbi riasztás megtekintése** hivatkozásra az e-mailben, megnyílik az Azure portal és a az Azure Security Center riasztások oldal, amely aktív SQL fenyegetéseket észlel áttekintést nyújt a felügyelt példány adatbázison megjelenítése.

   ![aktív fenyegetések](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Kattintson egy adott riasztás kivizsgálása a fenyegetés és a jövőbeli fenyegetésekkel szembeni szervizelését további részletek és műveletek beolvasásához.

   Ha például SQL-injektálás az egyik a gyakori webes biztonsági probléma az interneten. SQL-injektálás számára a támadásokkal szemben az adatvezérelt alkalmazások szolgál. A támadók előnyeit alkalmazások biztonsági réseinek rosszindulatú SQL-utasításokat injektálhatnak alkalmazás beviteli mezőibe visszaéléshez vagy azok az adatbázisban található adatok módosításához. SQL-injektálás riasztások a riasztás részleteit tartalmazza a sebezhető kihasználni SQL-utasítás.

   ![SQL-injektálás](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Felügyelt példány Fenyegetésészlelési riasztásokat 

Felügyelt példány Fenyegetésészlelési észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek, és azt is aktiválhatja a következő riasztásokat:
- **Biztonsági rés az SQL-injektálás számára**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázisban. Ez az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:
 - Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
 - Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
- **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbálja beszúrása rosszindulatú SQL-utasításokat a sebezhető alkalmazáskód vagy tárolt eljárásokat.
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, amikor megváltozik a hozzáférési mintájában, egy felügyelt példányra, amikor valaki jelentkezett be a felügyelt példánynak egy szokatlan földrajzi helyről. Bizonyos esetekben a riasztás jogszerű műveleteket (egy új alkalmazást vagy fejlesztői karbantartási művelet) észlel. Más esetekben a Riasztás kártékony műveleteket (korábbi alkalmazott, külső támadó és így tovább) észlel.
- **Hozzáférés szokatlan Azure-beli adat központból**: Ez a riasztás akkor aktiválódik, ha módosítják a felügyelt példány, amikor valaki jelentkezett be a felügyelt példánynak nem látott fér hozzá a felügyelt Azure-adatközpontok a hozzáférési mintájában A példány a legutóbbi időszakban. Bizonyos esetekben a riasztás jogszerű műveleteket (az új alkalmazás az Azure, a Power BI, az Azure SQL-Lekérdezésszerkesztő és így tovább) észlel. Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha a változás a hozzáférési mintájában felügyelt kiszolgálópéldányt, amikor valaki jelentkezett be a felügyelt példánynak egy szokatlan résztvevő (SQL-felhasználó) használatával történik. Bizonyos esetekben a riasztás jogszerű műveleteket (új alkalmazás fejlesztője karbantartási művelet) észlel. Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>További lépések

- Felügyelt példánnyal kapcsolatos további tudnivalókat a [mit jelent a felügyelt példány](sql-database-managed-instance.md)
- Tudjon meg többet [felügyelt példány naplózása](https://go.microsoft.com/fwlink/?linkid=869430) 
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
