---
title: Speciális adatbiztonság a IaaS-ben a Azure Security Centerban | Microsoft Docs
description: " Megtudhatja, hogyan engedélyezheti a speciális adatbiztonságot a IaaS a Azure Security Centerban. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295755"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Speciális adatbiztonság az Azure-beli SQL-kiszolgálókon Virtual Machines (nyilvános előzetes verzió)
Az Azure Virtual Machines-beli SQL serverek speciális adatbiztonsága egy egységes csomag a fejlett SQL-alapú biztonsági funkciókhoz. Jelenleg (nyilvános előzetes verzióban) magában foglalja a felszínre és a lehetséges adatbázis-sebezhetőségek enyhítésére szolgáló funkciókat, valamint a rendellenes tevékenységek észlelését, amelyek fenyegetést jelenthetnek az adatbázisra. 

Az Azure-beli virtuális gépek SQL Server-kiszolgálókkal kapcsolatos biztonsági ajánlata ugyanazokon az alapvető technológiákon alapul, mint a [Azure SQL Database Advanced Security csomagban](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Áttekintés

A speciális adatbiztonság számos fejlett SQL biztonsági képességet biztosít, amelyek a sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet foglalják magukban.

* A [sebezhetőségi felmérés](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) egy könnyen konfigurálható szolgáltatás, amely felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. Biztosítja a biztonsági állapot láthatóságát, és tartalmazza a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges lépéseket.
* A komplex [veszélyforrások elleni védelem](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az SQL Server eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és a rendellenes adatbázis-hozzáférési mintákon biztosít műveleti alapú biztonsági riasztásokat. Ezek a riasztások biztosítják a gyanús tevékenység részleteit, valamint az ajánlott műveleteket a fenyegetés kivizsgálásához és enyhítéséhez.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Ismerkedés az SQL Azure-beli virtuális gépeken futó speciális adatbiztonságával

Az alábbi lépések az SQL Azure-beli virtuális gépek nyilvános előzetes verziójában elérhető speciális adatbiztonsági funkcióit ismertetjük.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Fejlett adatbiztonság beállítása az SQL-hez Azure-beli virtuális gépeken

**Kezdés előtt**: Az elemzett biztonsági naplók tárolására Log Analytics munkaterületre van szükség. Ha még nem rendelkezik ilyennel, egyszerűen létrehozhatja a [log Analytics munkaterület létrehozása a Azure Portalban](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)című részben leírtak szerint.

1. Kapcsolódjon az SQL Servert futtató virtuális géphez a Log Analytics munkaterülethez. Útmutatásért lásd: [Windows rendszerű számítógépek Összekapcsolásának Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Az Azure Marketplace-en lépjen az [SQL Advanced Security](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)-megoldásra.
(A Piactéri keresési lehetőség használatával a következő képen látható módon találhatja meg.) Megnyílik az **SQL speciális adatbiztonság** lapja.

    ![Speciális adatbiztonság a IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Kattintson a **Create** (Létrehozás) gombra. Megjelennek a munkaterületek.

    ![Speciális adatbiztonság létrehozása](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Válassza ki a használni kívánt munkaterületet, és kattintson a **Létrehozás**gombra.

   ![Munkaterület kiválasztása](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Indítsa újra a [virtuális gép SQL serverjét](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Biztonsági riasztások feltárása és kivizsgálása

Az aktuális biztonsági riasztásokat megtekintheti és kezelheti.

1. Kattintson **Security Center** > **biztonsági riasztások**elemre, majd kattintson a riasztásra.

    ![Riasztás keresése](./media/security-center-advanced-iaas-data/find-alert.png)

1. A megtámadott **erőforrás** oszlopban kattintson a megtámadott erőforrásra.

1. Ha meg szeretné tekinteni a riasztás részleteit és a jelenlegi fenyegetés kivizsgálására és a jövőbeli fenyegetések kezelésére vonatkozó műveleteket, görgessen le az **általános információk** oldalra, és a javítási **lépések** szakaszban kattintson a **vizsgálat lépései** hivatkozásra.

    ![Szervizelési lépések](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. A riasztás indításával társított naplók megtekintéséhez lépjen a **log Analytics** -munkaterületek elemre, és hajtsa végre a következő lépéseket:

     > [!NOTE]
     > Ha a **log Analytics** -munkaterületek nem jelennek meg a bal oldali menüben, kattintson a **minden szolgáltatás**elemre, és keressen rá a **log Analytics**-munkaterületekre.

    1. Győződjön meg arról, hogy az oszlopok az **árképzési szintet** és a **munkaterület azonosítója** oszlopokat jelenítik meg. (**Log Analytics** > -munkaterületek:**Oszlopok szerkesztése**, **díjszabási szintek** és **munkaterület azonosítója**hozzáadása)

     ![Oszlopok szerkesztése](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Kattintson arra a munkaterületre, amelyen a riasztási naplók szerepelnek.

    1. Az **általános** menüben kattintson a **naplók** elemre.

    1. Kattintson a **SQLAdvancedThreatProtection** tábla melletti szemre. Megjelennek a naplók.

     ![Naplók megtekintése](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>E-mail-értesítés beállítása ATP-riasztásokhoz 

Megadhatja a címzettek listáját, amely e-mailben értesítést kap az ASC-riasztások létrehozásakor. Az e-mail tartalmaz egy közvetlen hivatkozást a riasztáshoz Azure Security Center az összes releváns részletet. 

1. Lépjen a **Security Center** > **díjszabás & beállítások** elemre, és kattintson a megfelelő előfizetésre.

    ![Előfizetés beállításai](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. A **Beállítások** menüben kattintson az **e-mail-értesítések**lehetőségre. 
1. Az **e-mail cím** szövegmezőbe írja be az e-mail-címeket az értesítések fogadásához. Több e-mail-címet is megadhat az e-mail-címek vesszővel (,) való elválasztásával.  admin1@mycompany.comPéldáuladmin2@mycompany.com:,admin3@mycompany.com

      ![E-mail-beállítások](./media/security-center-advanced-iaas-data/email-settings.png)

1. Az **e-mail értesítések** beállításainál adja meg a következő beállításokat:
  
    * **E-mail értesítések küldése a nagy súlyosságú riasztásokhoz**: Az e-mailek küldése helyett az összes riasztásra vonatkozóan csak a nagy súlyosságú riasztásokat küldje el.
    * **E-mail-értesítések küldése az előfizetés tulajdonosainak**:  Értesítéseket küldhet az előfizetések tulajdonosainak.

1. Az **e-mail értesítések** képernyő felső részén kattintson a **Mentés**gombra.

  > [!NOTE]
  > Ügyeljen rá, hogy az ablak bezárása előtt kattintson a **Mentés** gombra, vagy az új **e-mail értesítési** beállítások nem lesznek mentve.

## <a name="explore-vulnerability-assessment-reports"></a>A sebezhetőségi felmérési jelentések megismerése

A sebezhetőségi felmérés irányítópultja áttekintést nyújt az összes adatbázisa értékelésének eredményeiről. Az adatbázisok eloszlását a SQL Server verziója alapján tekintheti meg, valamint összefoglalhatja a sikertelen és az átadott adatbázisok összefoglalását, valamint az ellenőrzések sikertelen végrehajtásának általános összegzését a kockázati eloszlás alapján.

A sebezhetőségi felmérés eredményeit és jelentéseit közvetlenül a Log Analytics tekintheti meg.

1. Navigáljon a Log Analytics munkaterületére a speciális adatbiztonsági megoldással.
1. Navigáljon  a megoldásokhoz, és válassza ki az **SQL sebezhetőség** -felmérési megoldást.
1. Az **Összefoglalás** ablaktáblán kattintson az **Összefoglalás megtekintése** elemre, és válassza ki az **SQL sebezhetőség-értékelő jelentést**.

    ![SQL Assessment jelentés](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    A jelentés irányítópultja betöltődik. Győződjön meg arról, hogy az időablak legalább az **elmúlt 7 napban** van beállítva, mivel a sebezhetőségi felmérések vizsgálatait a rendszer 7 naponta egyszer, rögzített ütemterv szerint futtatja az adatbázisain.

    ![Az elmúlt 7 nap beállítása](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. További részletekért kattintson az irányítópult egyik elemére. Példa:

   1. A **sikertelen ellenőrzések összegzése** szakaszban a sebezhetőségi ellenőrzés lehetőségre kattintva megtekintheti az összes adatbázisra vonatkozóan az ellenőrzés eredményét log Analytics táblázatot. Az eredményekkel rendelkezők elsőként vannak felsorolva.

   1. Ezután kattintson az átmenő elemre az egyes biztonsági rések részleteinek megtekintéséhez, beleértve a sebezhetőség leírását, a hatás, az állapot, a kapcsolódó kockázat és a tényleges eredmények megjelenítését ezen az adatbázison. Az ellenőrzés elvégzéséhez futtatott tényleges lekérdezést, valamint a biztonsági rés megoldásához szükséges szervizelési információkat is megtekintheti.

    ![Munkaterület kiválasztása](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Munkaterület kiválasztása](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. A sebezhetőségi felmérés eredményeire vonatkozó összes Log Analytics lekérdezést futtathatja, hogy az igényeinek megfelelően feldarabolja és felszámítsa az adatkockát.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Komplex veszélyforrások elleni védelem az Azure-beli virtuális gépeken futó SQL Server-riasztásokhoz
A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják az SQL Server-kiszolgálók eléréséhez vagy kiaknázásához. Ezek az események a következő riasztásokat indíthatják el:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Rendellenes hozzáférési minta értesítései (nyilvános előzetes verzióban támogatott)

* **Hozzáférés szokatlan helyről:** Ez a riasztás akkor aktiválódik, ha módosul az SQL Serverhez való hozzáférési minta, ahol valaki szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Lehetséges okok:
     * Egy támadó vagy korábbi rosszindulatú alkalmazás hozzáfért a SQL Server.
     * Egy megbízható felhasználó új helyről fér hozzá a SQL Server.
* **Hozzáférés egy potenciálisan ártalmas alkalmazáshoz**: a riasztás akkor aktiválódik, ha egy potenciálisan ártalmas alkalmazás használja az adatbázis elérését. Lehetséges okok:
     * Egy támadó megpróbálta megszegni az SQL-t az általános támadási eszközök használatával.
     * A működés közbeni jogszerű behatolás tesztelése.
* **Hozzáférés ismeretlen rendszerbiztonsági tag felől**: Ez a riasztás akkor aktiválódik, ha módosul az SQL Server hozzáférési mintája, ahol valaki szokatlan rendszerbiztonsági tag (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Lehetséges okok:
     * Egy támadó vagy korábbi rosszindulatú alkalmazás hozzáfért a SQL Server. 
     * Egy megbízható felhasználó egy új rendszerbiztonsági tag számára fér hozzá a SQL Server.
* **Találgatásos kényszerített SQL-hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha a különböző hitelesítő adatokkal rendelkező sikertelen bejelentkezések rendellenesen magas száma. Lehetséges okok:
     * Egy támadó megpróbálta megszegni az SQL-t a találgatásos támadással.
     * A működés közbeni jogszerű behatolás tesztelése.

### <a name="potential-sql-injection-attacks-coming"></a>Lehetséges SQL-injektálási támadások (Coming)

* **Biztonsági rés az SQL-injektáláshoz**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás hibás SQL-utasítást hoz létre az adatbázisban. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. Lehetséges okok:
     * Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
     * Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
* **Lehetséges SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha az SQL-injektálás során egy azonosított alkalmazás biztonsági résen történik egy aktív probléma. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
