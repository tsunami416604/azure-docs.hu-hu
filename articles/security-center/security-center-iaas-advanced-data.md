---
title: Speciális adatbiztonság a IaaS-ben a Azure Security Centerban | Microsoft Docs
description: " Megtudhatja, hogyan engedélyezheti a speciális adatbiztonságot a IaaS a Azure Security Centerban. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: 068fb9f61b7dcb3948e4f03c284ddfa680522c85
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907046"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Speciális adatbiztonság az Azure-beli SQL-kiszolgálókon Virtual Machines (előzetes verzió)
Az Azure Virtual Machines-beli SQL serverek speciális adatbiztonsága egy egységes csomag a fejlett SQL-alapú biztonsági funkciókhoz. Ez az előzetes verziójú funkció a lehetséges adatbázis-sebezhetőségek azonosítására és enyhítésére, valamint a rendellenes tevékenységek észlelésére, amelyek az adatbázis fenyegetéseit jelezhetik. 

Az Azure-beli virtuális gépek SQL Server-kiszolgálókkal kapcsolatos biztonsági ajánlata ugyanazokon az alapvető technológiákon alapul, mint a [Azure SQL Database Advanced Security csomagban](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Áttekintés

A speciális adatbiztonság számos fejlett SQL biztonsági képességet biztosít, amelyek a sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet foglalják magukban.

* A [sebezhetőségi felmérés](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) egy könnyen konfigurálható szolgáltatás, amely felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. Biztosítja a biztonsági állapot láthatóságát, és tartalmazza a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges lépéseket.
* A komplex [veszélyforrások elleni védelem](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az SQL Server eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és a rendellenes adatbázis-hozzáférési mintákon biztosít műveleti alapú biztonsági riasztásokat. Ezek a riasztások biztosítják a gyanús tevékenység részleteit, valamint az ajánlott műveleteket a fenyegetés kivizsgálásához és enyhítéséhez.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Ismerkedés az SQL Azure-beli virtuális gépeken futó speciális adatbiztonságával

Az alábbi lépések az SQL Azure-beli virtuális gépek nyilvános előzetes verziójában elérhető speciális adatbiztonsági funkcióit ismertetjük.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Fejlett adatbiztonság beállítása az SQL-hez Azure-beli virtuális gépeken

A speciális adatbiztonság engedélyezése az SQL-kiszolgálókon Virtual Machines az előfizetés/munkaterület szintjén:
 
1. A Security Center oldalsávján nyissa meg a **díjszabási & beállítások** lapot.

1. Válassza ki azt az előfizetést vagy munkaterületet, amelyhez engedélyezni kívánja az SQL Azure-beli virtuális gépeken elérhető speciális adatvédelmet.

1. A **virtuális gépen (előzetes verzió) lévő SQL Server-kiszolgálók** engedélyezéséhez kapcsolja be a beállítást. 

    (Kattintson a képernyőképre a kibontásához)

    [![Security Center javaslatok és riasztások a Windows felügyeleti központban látható módon](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Az SQL-kiszolgálók speciális adatbiztonsága engedélyezve lesz a kijelölt munkaterülethez csatlakoztatott összes SQL-kiszolgálón vagy a kijelölt előfizetés alapértelmezett munkaterületén.

    >[!NOTE]
    > A megoldás teljes mértékben aktív lesz a SQL Server első újraindítása után. 

Új munkaterület létrehozásához kövesse az [log Analytics-munkaterület létrehozása](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)című témakör utasításait.

A SQL Server gazdagép munkaterülethez való összekapcsolásához kövesse a [Windows rendszerű számítógépek összekötése a Azure monitorhoz](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)című témakör utasításait.


## <a name="set-up-email-notification-for-atp-alerts"></a>E-mail-értesítés beállítása ATP-riasztásokhoz 

Megadhatja a címzettek listáját, amely e-mailben értesítést kap a Security Center riasztások létrehozásakor. Az e-mail tartalmaz egy közvetlen hivatkozást a riasztáshoz Azure Security Center az összes releváns részletet. 

1. Lépjen a **Security Center** > **díjszabás & a beállítások** elemre, és kattintson a megfelelő előfizetésre.

    ![Előfizetés beállításai](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. A beállítások menüben kattintson az **e-mail-értesítések**lehetőségre. 
1. Az **e-mail cím** szövegmezőbe írja be az e-mail-címeket az értesítések fogadásához. Több e-mail-címet is megadhat az e-mail-címek vesszővel (,) való elválasztásával.  Például admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

    ![E-mail-beállítások](./media/security-center-advanced-iaas-data/email-settings.png)

1. Az **e-mail értesítések** beállításainál adja meg a következő beállításokat:
  
    * **E-mail-értesítések küldése a nagy súlyosságú riasztásokhoz**: az e-mailek küldése helyett az összes riasztásra, csak a nagy súlyosságú riasztások küldésére.
    * **E-mail-értesítések küldése az előfizetés tulajdonosainak**: értesítések küldése az előfizetések tulajdonosainak is.

1. Az **e-mail értesítések** képernyő felső részén kattintson a **Mentés**gombra.

  > [!NOTE]
  > Ügyeljen rá, hogy az ablak bezárása előtt kattintson a **Mentés** gombra, vagy az új **e-mail értesítési** beállítások nem lesznek mentve.

## <a name="explore-vulnerability-assessment-reports"></a>A sebezhetőségi felmérési jelentések megismerése

A sebezhetőségi felmérés irányítópultja áttekintést nyújt az összes adatbázisa értékelésének eredményeiről. Az adatbázisok eloszlását a SQL Server verziója alapján tekintheti meg, valamint összefoglalhatja a sikertelen és az átadott adatbázisok összefoglalását, valamint az ellenőrzések sikertelen végrehajtásának általános összegzését a kockázati eloszlás alapján.

A sebezhetőségi felmérés eredményei közvetlenül a Security Center tekinthetők meg.

1. Az Security Center oldalsávján az erőforrás-biztonsági higiénia területen válassza az **adatok & Storage**lehetőséget.

1. Válassza ki a **virtuális gépeken található SQL-adatbázisokra vonatkozó ajánlási biztonsági réseket (előzetes verzió)** . További információ: [Security Center javaslatok](security-center-recommendations.md). 

    [![* * a virtuális gépeken található SQL-adatbázisokra vonatkozó biztonsági réseket szervizelni kell (előzetes verzió) * * javaslat](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Megjelenik a javaslat részletes nézete.

    [a * * biztonsági rések a virtuális gépeken található SQL-adatbázisokban történő részletes megtekintését (előzetes verzió) * * javaslat ![](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. További részletek a részletezéshez:

    * A beolvasott erőforrások (adatbázisok) és a tesztelt biztonsági ellenőrzések listájának áttekintéséhez kattintson az Önt érdeklő kiszolgálóra.
    [![biztonsági rések SQL Server szerint csoportosítva](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Az adott SQL-adatbázis által csoportosított biztonsági rések áttekintéséhez kattintson az érdekes adatbázisra.
    [![biztonsági rések SQL Server szerint csoportosítva](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    Az egyes nézetekben a biztonsági ellenőrzések **Súlyosság**szerint rendezve jelennek meg. Egy adott biztonsági vizsgálatra kattintva megtekintheti a részleteket tartalmazó ablaktáblát **, amely** **leírja, hogyan**javíthatja, és egyéb kapcsolódó információkat, például a **hatást** vagy a **teljesítménytesztet**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Komplex veszélyforrások elleni védelem az Azure-beli virtuális gépeken futó SQL Server-riasztásokhoz
A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják az SQL Server-kiszolgálók eléréséhez vagy kiaknázásához. Ezek az események a következő riasztásokat indíthatják el:

### <a name="anomalous-access-pattern-alerts-preview"></a>Rendellenes hozzáférési minta értesítései (előzetes verzió)

* **Hozzáférés szokatlan helyről:** Ez a riasztás akkor aktiválódik, ha módosul az SQL Serverhez való hozzáférési minta, ahol valaki szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Lehetséges okok:
    * Egy támadó vagy korábbi rosszindulatú alkalmazás hozzáfért a SQL Server.
    * Egy megbízható felhasználó új helyről fér hozzá a SQL Server.
* **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Lehetséges okok:
    * Egy támadó megpróbálta megszegni az SQL-t az általános támadási eszközök használatával.
    * A működés közbeni jogszerű behatolás tesztelése.
* **Hozzáférés szokatlan résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan résztvevő (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Lehetséges okok:
    * Egy támadó vagy korábbi rosszindulatú alkalmazás hozzáfért a SQL Server. 
    * Egy megbízható felhasználó egy új rendszerbiztonsági tag számára fér hozzá a SQL Server.
* **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Lehetséges okok:
    * Egy támadó megpróbálta megszegni az SQL-t a találgatásos támadással.
    * A működés közbeni jogszerű behatolás tesztelése.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Lehetséges SQL-injektálási támadások (SQL Server 2019-es verzióban támogatott)

* **Biztonsági rés az SQL-injektáláshoz**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás hibás SQL-utasítást hoz létre az adatbázisban. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. Lehetséges okok:
    * Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
    * Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
* **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Nem biztonságos parancs (SQL Server 2019-es verzióban támogatott)

* **Potenciálisan nem biztonságos művelet**: Ez a riasztás akkor aktiválódik, ha egy magas jogosultsági szintű és potenciálisan nem biztonságos parancsot hajt végre. Lehetséges okok:
    * A jobb biztonsági testhelyzethez ajánlott letiltani kívánt parancs engedélyezve van.
    * Egy támadó megpróbál kihasználni SQL-hozzáférést vagy kiterjesztési jogosultságokat.   


## <a name="explore-and-investigate-security-alerts"></a>Biztonsági riasztások feltárása és kivizsgálása

Az adatbiztonsági riasztások a Security Center riasztások lapján, az erőforrás Biztonság lapján vagy a riasztási e-mailek közvetlen hivatkozásán keresztül érhetők el.

1. Riasztások megtekintése:

    * A Security Center kattintson a **biztonsági riasztások** elemre az oldalsávon, és válassza ki a riasztást.
    * Az erőforrás hatókörében – nyissa meg a megfelelő erőforrás lapot, majd az oldalsávon kattintson a **Biztonság**elemre. 

1. A riasztásokat önállóan, részletes szervizelési lépésekkel és vizsgálati információkkal tervezték meg. Részletesebben is megvizsgálhat további Azure Security Center és Azure Sentinel-képességeket a szélesebb körű nézet érdekében:

    * A további vizsgálatok érdekében engedélyezze SQL Server naplózási funkcióját. Ha Ön Azure Sentinel-felhasználó, feltöltheti az SQL-naplózási naplókat a Windows biztonsági napló eseményeiből a Sentinel szolgáltatásba, és élvezheti a részletes nyomozást.
    * A biztonsági helyzet javításához használja Security Center javaslatait az egyes riasztásokban jelzett gazdagéphez. Ez csökkenti a jövőbeli támadások kockázatát. 


## <a name="next-steps"></a>Következő lépések

Kapcsolódó anyagok esetében tekintse meg a következő cikket:

- [Javaslatok szervizelése](security-center-remediate-recommendations.md)