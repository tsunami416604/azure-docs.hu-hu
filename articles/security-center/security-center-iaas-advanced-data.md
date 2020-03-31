---
title: Az IaaS speciális adatbiztonsága az Azure Security Centerben | Microsoft dokumentumok
description: " Ismerje meg, hogyan engedélyezheti az IaaS speciális adatbiztonságát az Azure Security Centerben. "
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
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282730"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Speciális adatbiztonság az Azure virtuális gépekEN lévő SQL-kiszolgálók számára (előzetes verzió)
Az Azure virtuális gépek SQL-kiszolgálóinak speciális adatbiztonsága egy egységes csomag a fejlett SQL biztonsági képességekhez. Ez az előzetes verzió funkció az adatbázis esetleges biztonsági rései azonosítását és mérséklését, valamint az adatbázist fenyegető veszélyeket jelző rendellenes tevékenységek észlelését teszi lehetővé. 

Ez az Azure Virtuális gép SQL-kiszolgálókra vonatkozó biztonsági ajánlat az [Azure SQL Database Advanced Data Security csomagban](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)használt alapvető technológián alapul.


## <a name="overview"></a>Áttekintés

A fejlett adatbiztonság fejlett SQL biztonsági képességeket biztosít, amelyek a biztonsági rések felméréséből és a komplex veszélyforrások elleni védelemből állnak.

* [A biztonsági rés felmérése](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) könnyen konfigurálható szolgáltatás, amely képes felderíteni, nyomon követni és segíteni az adatbázis esetleges biztonsági réseinek elhárításában. Betekintést nyújt a biztonsági állapotba, és tartalmazza a biztonsági problémák megoldásához és az adatbázis-erődítmények javításához szükséges lépéseket.
* [A komplex veszélyforrások elleni védelem](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros kísérleteket jeleznek az SQL-kiszolgáló elérésére vagy kihasználására. Folyamatosan figyeli az adatbázis gyanús tevékenységeket, és művelet-orientált biztonsági riasztásokat biztosít a rendellenes adatbázis-hozzáférési mintákról. Ezek a riasztások biztosítják a gyanús tevékenység részleteit és ajánlott műveleteket a fenyegetés kivizsgálására és csökkentésére.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Első lépések az SQL speciális adatbiztonsága azure-beli virtuális gépeken

A következő lépések az Azure-beli virtuális gépek nyilvános előzetes verziójának irányított adatbiztonsága című csomaggal kezdődnek.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Speciális adatbiztonság beállítása az SQL számára az Azure virtuális gépein

Az SQL-kiszolgálók speciális adatbiztonságának engedélyezése az előfizetés/munkaterület szintjén:
 
1. A Biztonsági központ oldalsávján nyissa meg a **Díjszabási & beállítások** lapot.

1. Válassza ki azt az előfizetést vagy munkaterületet, amelyhez engedélyezni szeretné az SQL speciális adatbiztonságát az Azure virtuális gépeken.

1. A virtuális gép **(előzetes verzió) SQL-kiszolgálóinak** beállításának be- és bekapcsolására van kiváltása. 

    (A kibontáshoz kattintson a képernyőképre)

    [![A Biztonsági központ javaslatai és figyelmeztetései a Windows Felügyeleti központban láthatómódon](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Az SQL-kiszolgálók speciális adatbiztonsága minden olyan SQL-kiszolgálón engedélyezve lesz, amely a kijelölt munkaterülethez vagy a kijelölt előfizetés alapértelmezett munkaterületéhez csatlakozik.

    >[!NOTE]
    > A megoldás az SQL Server első újraindítása után teljesen aktív lesz. 

Új munkaterület létrehozásához kövesse a [Log Analytics-munkaterület létrehozása című](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)részben található utasításokat.

Ha az SQL Server állomását munkaterülethez szeretné csatlakoztatni, kövesse a [Windows-számítógépek csatlakoztatása az Azure Monitorhoz](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)című útmutató utasításait.


## <a name="set-up-email-notification-for-security-alerts"></a>E-mail értesítés beállítása biztonsági riasztásokhoz 

Beállíthatja, hogy a címzettek listáját e-mailben értesítsék a Security Center-riasztások létrehozásakor. Az e-mail tartalmaz egy közvetlen linket a riasztás az Azure Security Center az összes vonatkozó részleteket tartalmaz. 

1. Nyissa meg a **Security Center** > **díjszabási & beállításait,** és kattintson a megfelelő előfizetésre

    ![Előfizetés beállításai](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. A Beállítások menüben kattintson az **E-mail értesítések parancsra.** 
1. Az **E-mail cím** mezőbe írja be az e-mail címeket az értesítések fogadásához. Az e-mail címek vesszővel (,) való elválasztásával több e-mail címet is megadhat.  Például admin1@mycompany.comadmin2@mycompany.com, ,admin3@mycompany.com

    ![E-mail-beállítások](./media/security-center-advanced-iaas-data/email-settings.png)

1. Az **E-mail értesítés beállításai** között adja meg a következő beállításokat:
  
    * **Küldjön e-mail értesítést a magas súlyosságú riasztások:** Ahelyett, hogy e-maileket küld az összes riasztást, küldjön csak a súlyos riasztások.
    * **Is küldjön e-mail értesítéseket előfizetés tulajdonosok:** Értesítésekküldése az előfizetések tulajdonosai is.

1. Az **E-mail értesítések** képernyő tetején kattintson a **Mentés gombra.**

  > [!NOTE]
  > Ügyeljen arra, hogy az ablak bezárása előtt kattintson a **Mentés** gombra, különben az új **e-mail értesítési** beállítások nem lesznek mentve.

## <a name="explore-vulnerability-assessment-reports"></a>Fedezze fel a biztonsági résértékelési jelentéseket

A biztonsági rés felmérési irányítópultja áttekintést nyújt az összes adatbázis értékelési eredményeiről. Megtekintheti az adatbázisok terjesztését az SQL Server verziójának megfelelően, valamint a sikertelen és az elhaladó adatbázisok összegzését, valamint a nem megfelelő ellenőrzések általános összegzését a kockázatelosztás nak megfelelően.

A biztonsági rés értékelésének eredményeit közvetlenül a Security Centerből tekintheti meg.

1. A Biztonsági központ oldalsávján az ERŐFORRÁS-biztonság higiénia a **Data & Storage (Adattárolás & csoportban**válassza a Data & Storage (Adattárolás & lehetőséget).

1. Válassza ki a javaslat **biztonsági rések az SQL-adatbázisok virtuális gépek en kell kikell újult (Preview)**. További információt a [Biztonsági központ ajánlásai című témakörben talál.](security-center-recommendations.md) 

    [![**A virtuális gépek SQL-adatbázisainak biztonsági réseit ki kell újítani (Előzetes verzió)** javaslat](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Megjelenik az ajánlás részletes nézete.

    [![A virtuális gépek SQL-adatbázisainak **biztonsági réseit részletesen ki kell újítani (Előzetes verzió)** javaslat](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. További részletek részletezése:

    * A beolvasott erőforrások (adatbázisok) és a tesztelt biztonsági ellenőrzések listájának áttekintéséhez kattintson az érdeklődésre számot tartó kiszolgálóra.
    [![Az SQL-kiszolgáló által csoportosított biztonsági rések](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Egy adott SQL-adatbázis által csoportosított biztonsági rések áttekintéséhez kattintson az érdeklődésre számot tartó adatbázisra.
    [![Az SQL-kiszolgáló által csoportosított biztonsági rések](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    A biztonsági ellenőrzések minden nézetben **súlyosság**szerint vannak rendezve. Kattintson egy adott biztonsági ellenőrzésre a **Leírás** **Remediate** , javítási lehetőséggel és egyéb kapcsolódó információkkal , például **az Impact** vagy **a Benchmark**programmal ellátott részleteket tartalmazó ablaktáblán.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Komplex veszélyforrások elleni védelem az Azure virtuális gépein lévő SQL-kiszolgálók számára
A riasztásokat szokatlan és potenciálisan káros SQL-kiszolgálók elérésére vagy kihasználására irányuló kísérletek generálják. Ezek az események a következő riasztásokat indíthatják el:

### <a name="anomalous-access-pattern-alerts-preview"></a>Rendellenes hozzáférési minta riasztások (Előzetes verzió)

* **Hozzáférés szokatlan helyről:** Ez a riasztás akkor aktiválódik, ha az SQL-kiszolgáló hozzáférési mintájában megváltozik, ahol valaki szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Lehetséges okok:
    * A támadó vagy korábbi rosszindulatú alkalmazás hozzáfért az SQL Server kiszolgálóhoz.
    * Egy megbízható felhasználó új helyről fért hozzá az SQL Server kiszolgálóhoz.
* **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Lehetséges okok:
    * A támadó megpróbálja áttörni az SQL segítségével közös támadási eszközök.
    * Törvényes behatolási teszt akcióban.
* **Hozzáférés szokatlan résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan résztvevő (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Lehetséges okok:
    * A támadó vagy korábbi rosszindulatú alkalmazás hozzáfért az SQL Server kiszolgálóhoz. 
    * Egy megbízható felhasználó egy új főtaggal fért hozzá az SQL Server kiszolgálóhoz.
* **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Lehetséges okok:
    * A támadó megpróbálja áttörni a SQL segítségével nyers erő.
    * Törvényes behatolási teszt akcióban.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Lehetséges SQL-injektálási támadások (az SQL Server 2019 támogatja)

* **Sql-injektálás biztonsági rése**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás hibás SQL-utasítást hoz létre az adatbázisban. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. Lehetséges okok:
    * Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
    * Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
* **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Nem biztonságos parancs (az SQL Server 2019 támogatja)

* **Potenciálisan nem biztonságos művelet:** Ez a riasztás akkor aktiválódik, ha egy magas szintű jogosultsággal rendelkező és potenciálisan nem biztonságos parancsot hajt végre. Lehetséges okok:
    * A jobb biztonsági testtartás érdekében letiltást ajánlott parancs engedélyezve van.
    * Az SQL-hozzáférést kihasználni vagy a jogosultságok eszkalálódását próbáló támadó.   


## <a name="explore-and-investigate-security-alerts"></a>Biztonsági riasztások feltárása és vizsgálata

Az adatbiztonsági riasztások a Security Center riasztások lapján, az erőforrás biztonsági lapján vagy a riasztási e-mailekben található közvetlen hivatkozáson keresztül érhetők el.

1. Riasztások megtekintése:

    * A Biztonsági központban – Kattintson a **Biztonsági riasztások elemre** az oldalsávról, és válasszon ki egy riasztást.
    * Az erőforráshatókörben – Nyissa meg a megfelelő erőforráslapot, és az oldalsávon kattintson a **Biztonság**gombra. 

1. A riasztásokat úgy tervezték, hogy önállóak legyenek, és mindegyikben részletes javítási lépéseket és vizsgálati információkat tartalmazzanak. Az Azure Security Center és az Azure Sentinel más funkcióival további vizsgálatokat tehet a szélesebb nézetben:

    * Engedélyezze az SQL Server naplózási szolgáltatását további vizsgálatokhoz. Ha Ön Azure Sentinel-felhasználó, feltöltheti az SQL naplózási naplókat a Windows biztonsági napló eseményeiből a Sentinelbe, és élvezheti a teljes körű vizsgálati élményt.
    * A biztonsági állapot javítása érdekében használja a Security Center az egyes riasztásokban jelzett gazdagépre vonatkozó javaslatait. Ez csökkenti a jövőbeni támadások kockázatát. 


## <a name="next-steps"></a>További lépések

A kapcsolódó anyagokat lásd a következő cikkben:

- [Ajánlások kijavítása](security-center-remediate-recommendations.md)