---
title: Komplex veszélyforrások elleni védelem – Azure Database for MariaDB | Microsoft Docs
description: A fenyegetések elleni védelem rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: f77d9f105d5c2ff10753f2b4b1ecc962c84eee6e
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869650"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Komplex veszélyforrások elleni védelem Azure Database for MariaDB

A Azure Database for MariaDB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A komplex veszélyforrások elleni védelem a fejlett adatbiztonsági ajánlat része, amely egy egységes csomag a speciális biztonsági funkciókhoz. A komplex veszélyforrások elleni védelem a [Azure Portal](https://portal.azure.com)keresztül érhető el és kezelhető.

> [!IMPORTANT]
> A komplex veszélyforrások elleni védelem nyilvános előzetes verzióban érhető el. Ez a funkció az Azure minden régiójában elérhető, ahol a Azure Database for MariaDB általános célú és a memóriára optimalizált kiszolgálók esetében van telepítve.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció a következő Azure Government és szuverén Felhőbeli régiókban **nem** érhető el: US Gov Texas, US Gov Arizona, US Gov Iowa, USA, gov Virginia, US DoD – keleti régió, US DoD – középső régió, Közép-Németország, Észak-Németország, Kelet-Kína, Kelet-Kína 2. Tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) az általános termékek rendelkezésre állása érdekében.

## <a name="set-up-threat-detection"></a>Fenyegetés észlelésének beállítása
1. Indítsa el a Azure Portal [https://portal.azure.com](https://portal.azure.com)a következő helyen:.
2. Navigáljon a védelemmel ellátni kívánt Azure Database for MariaDB-kiszolgáló konfigurációs lapjára. A biztonsági beállítások területen válassza a **speciális veszélyforrások elleni védelem (előzetes verzió)** lehetőséget.
3. A **speciális veszélyforrások elleni védelem (előzetes verzió)** konfigurációs lapján:

   - Az összetett veszélyforrások elleni védelem engedélyezése a kiszolgálón.
   - Az **összetett veszélyforrások elleni védelem beállításaiban**a **riasztások küldése** a szövegmezőbe mezőbe írja be azoknak az e-maileknek a listáját, amelyek biztonsági riasztásokat kapnak a rendellenes adatbázis-tevékenységek észlelése után.
  
   ![Fenyegetés észlelésének beállítása](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>A rendellenes adatbázis-tevékenységek megismerése

A rendellenes adatbázis-tevékenységek észlelése után e-mailben értesítést kap. Az e-mail információt nyújt a gyanús biztonsági eseményekről, beleértve a rendellenes tevékenységek jellegét, az adatbázis nevét, a kiszolgáló nevét, az alkalmazás nevét és az esemény időpontját. Emellett az e-mail-cím a lehetséges okokkal és a javasolt műveletekkel kapcsolatos információkat nyújt az adatbázis lehetséges fenyegetésének kivizsgálásához és enyhítéséhez.
 
1. Kattintson a **legutóbbi riasztások megtekintése** hivatkozásra az e-mailben a Azure Portal elindításához és a Azure Security Center riasztások oldal megjelenítéséhez, amely áttekintést nyújt az SQL Database-ben észlelt aktív fenyegetésekről.
    
    ![Rendellenes tevékenység jelentés](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Aktív fenyegetések megtekintése:

    ![Aktív fenyegetések](./media/howto-database-threat-protection-portal/active-threats.png)

2. Egy adott riasztásra kattintva további részleteket és műveleteket kaphat a fenyegetés kivizsgálásához és a jövőbeli fenyegetések szervizelését.
    
    ![Adott riasztás](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Fenyegetések észlelésével kapcsolatos riasztások megismerése

SQL Database fenyegetés észlelése a riasztásokat [](https://azure.microsoft.com/services/security-center/)a Azure Security Centerával integrálja. Az SQL-veszélyforrások észlelése az adatbázison belül, a Azure Portal található SQL ATP-pengék pedig nyomon követik az aktív fenyegetések állapotát.

Kattintson a **veszélyforrások észlelése riasztásra** a Azure Security Center riasztások oldal elindításához, és tekintse át az adatbázisban észlelt aktív SQL-fenyegetéseket.

   ![Veszélyforrások észlelésének riasztása](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>További lépések

* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* A díjszabással kapcsolatos további információkért tekintse meg a [Azure Database for MariaDB díjszabási oldalát](https://azure.microsoft.com/pricing/details/mariadb/) .  
