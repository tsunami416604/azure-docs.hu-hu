---
title: Komplex veszélyforrások elleni védelem – Azure Portal – Azure Database for MySQL
description: Ismerje meg, hogyan konfigurálhatja a komplex veszélyforrások elleni védelmet a rendellenes adatbázis-tevékenységek észlelésére, amely az adatbázis lehetséges biztonsági fenyegetéseket jelez.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 11ac05c710f7daf709ffbc29aebff706aea704d5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765046"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Komplex veszélyforrások elleni védelem az Azure Database for MySQL-hez

Az Azure Database for MySQL-hez készült Komplex veszélyforrások elleni védelem észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket.

A komplex veszélyforrások elleni védelem a fejlett adatbiztonsági ajánlat része, amely egy egységes csomag a speciális biztonsági funkciókhoz. A komplex veszélyforrások elleni védelem a [Azure Portalon](https://portal.azure.com) keresztül érhető el és felügyelhető, és jelenleg előzetes verzióban érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció a következő Azure Government-és szuverén felhő-régiókban **nem** érhető el: US Gov Texas, US Gov Arizona, US gov Iowa, USA, gov Virginia, US DoD – keleti régió, US DoD – középső régió, Közép-németország, Észak-Németország, Kelet-Kína, Kelet-Kína 2. Tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) az általános termékek rendelkezésre állása érdekében.
>

> [!NOTE]
> Ez a funkció az Azure minden régiójában elérhető, ahol a Azure Database for MySQL általános célú és a memóriára optimalizált kiszolgálók esetében van telepítve.

## <a name="set-up-threat-detection"></a>Fenyegetés észlelésének beállítása
1. Indítsa el a Azure Portalt a [https://portal.azure.com](https://portal.azure.com).
2. Navigáljon a védelemmel ellátni kívánt Azure Database for MySQL-kiszolgáló konfigurációs lapjára. A biztonsági beállítások területen válassza a **speciális veszélyforrások elleni védelem (előzetes verzió)** lehetőséget.
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

SQL Database fenyegetés észlelése a riasztásokat a [Azure Security Centerával](https://azure.microsoft.com/services/security-center/)integrálja. Az élő SQL-veszélyforrások észlelése csempén nyomon követheti az aktív fenyegetések állapotát a Azure Portal adatbázisán és az SQL ATP-lapokon.

Kattintson a **veszélyforrások észlelése riasztásra** a Azure Security Center riasztások oldal elindításához, és tekintse át az adatbázisban észlelt aktív SQL-fenyegetéseket.

   ![Veszélyforrások észlelésének riasztása](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* A díjszabással kapcsolatos további információkért tekintse meg a [Azure Database for MySQL díjszabási oldalát](https://azure.microsoft.com/pricing/details/mysql/) .  
