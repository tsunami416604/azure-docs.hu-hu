---
title: Komplex veszélyforrások elleni védelem – Azure portal – Azure-adatbázis a MySQL-hez
description: Ismerje meg, hogyan konfigurálhatja a komplex veszélyforrások elleni védelmet az adatbázispotenciális biztonsági fenyegetéseit jelző rendellenes adatbázis-tevékenységek észlelésére.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8eec40006a280b69387e14a5841360da65616ca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062335"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Komplex veszélyforrások elleni védelem a MySQL-hez készült Azure-adatbázishoz

Az Advanced Threat Protection for Azure Database for MySQL észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására irányuló szokatlan és potenciálisan rosszindulatú kísérletekre utalnak.

A komplex veszélyforrások elleni védelem része a speciális adatbiztonsági ajánlatnak, amely a fejlett biztonsági képességek egységes csomagja. A komplex veszélyforrások elleni védelem az [Azure Portalon](https://portal.azure.com) keresztül érhető el és kezelhető, és jelenleg előzetes verzióban érhető el.

> [!NOTE]
> Az Advanced Threat Protection funkció **nem** érhető el a következő Azure-kormányzat- és felhőrégiókban: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Kérjük, látogasson el [a termékek rendelkezésre álló régió általános](https://azure.microsoft.com/global-infrastructure/services/) termék elérhetőségét.
>

> [!NOTE]
> Ez a funkció az Azure minden olyan régiójában érhető el, ahol az Azure Database for MySQL általános célú és memóriaoptimalizált kiszolgálókra van telepítve.

## <a name="set-up-threat-detection"></a>Fenyegetésészlelés beállítása
1. Indítsa el az [https://portal.azure.com](https://portal.azure.com)Azure Portalt a helyen.
2. Keresse meg a védeni kívánt Azure Database for MySQL-kiszolgáló konfigurációs lapját. A biztonsági beállításokban válassza a **Komplex veszélyforrások elleni védelem (előzetes verzió)** lehetőséget.
3. A **Komplex veszélyforrások elleni védelem (előzetes verzió) konfigurációs** lapon:

   - Engedélyezze a komplex veszélyforrások elleni védelmet a kiszolgálón.
   - A **Komplex veszélyforrások elleni védelem beállításai**párbeszédpanelen a Riasztások küldése a **szövegbe** mezőben adja meg a biztonsági riasztások fogadásához a rendellenes adatbázis-tevékenységek észlelése kor érkező e-mailek listáját.
  
   ![Fenyegetésészlelés beállítása](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>A rendellenes adatbázis-tevékenységek felfedezése

A rendellenes adatbázis-tevékenységek észlelésekor e-mailben értesítést kap. Az e-mail a gyanús biztonsági eseményről nyújt információkat, beleértve a rendellenes tevékenységek jellegét, az adatbázis nevét, a kiszolgáló nevét, az alkalmazás nevét és az esemény idejét. Ezenkívül az e-mail tájékoztatást nyújt a lehetséges okok és ajánlott műveletek az adatbázis potenciális veszélyének kivizsgálására és csökkentésére.
 
1. Kattintson a **Legutóbbi riasztások megtekintése** hivatkozásra az e-mailben az Azure Portal elindításához és az Azure Security Center riasztások lapjának megjelenítéséhez, amely áttekintést nyújt az SQL-adatbázisban észlelt aktív fenyegetésekről.
    
    ![Rendellenes tevékenységjelentés](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Aktív fenyegetések megtekintése:

    ![Aktív fenyegetések](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kattintson egy adott riasztásra a fenyegetés kivizsgálásához és a jövőbeli fenyegetések elhárításához szükséges további részletek és műveletek leküzdése érdekében.
    
    ![Különleges riasztás](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Fenyegetésészlelési riasztások feltárása

Az SQL Database Threat Detection integrálja a riasztásokat az [Azure Security Centerrel.](https://azure.microsoft.com/services/security-center/) Egy élő SQL-fenyegetésészlelési csempe nyomon követi az aktív fenyegetések állapotát az adatbázisban és az SQL ATP-lapokon az Azure Portalon.

Kattintson **a fenyegetésészlelési riasztásra** az Azure Security Center riasztási lapjának elindításához, és az adatbázisban észlelt aktív SQL-fenyegetések áttekintése.

   ![Fenyegetésészlelési riasztás](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>További lépések

* További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Az árképzéssel kapcsolatos további információkért tekintse meg az [Azure Database for MySQL díjszabási oldalát.](https://azure.microsoft.com/pricing/details/mysql/)  
