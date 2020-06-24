---
title: Speciális adatbiztonság a IaaS-ben a Azure Security Centerban | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a speciális adatbiztonságot a Azure Security Centerban található SQL-gépekhez.
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
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 1c269ad13072ae4dcff9caba892ccc0643647e5c
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254180"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Fejlett adatbiztonság SQL-gépekhez (előzetes verzió)

Az SQL-gépek speciális adatbiztonsága védi az Azure-ban üzemeltetett SQL-kiszolgálókat, más felhőalapú környezetekben, vagy akár helyszíni gépeken is. Azure Security Center Ez kibővíti az Azure-natív SQL-kiszolgálók védelmét, hogy teljes mértékben támogassa a hibrid környezeteket.

Ez az előzetes verziójú szolgáltatás a lehetséges adatbázis-sebezhetőségek azonosítására és enyhítésére, valamint az adatbázis fenyegetéseit jelző rendellenes tevékenységek észlelésére szolgáló funkciókat tartalmaz. 

* **Sebezhetőségi felmérés** – a szkennelési szolgáltatás felderíti, nyomon követheti és segítheti a lehetséges adatbázis-rések szervizelését. Az értékelési vizsgálatok áttekintést nyújtanak az SQL-gépek biztonsági állapotáról, valamint a biztonsági eredmények részleteiről.

* Komplex [veszélyforrások elleni védelem](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) – az észlelési szolgáltatás, amely folyamatosan FIGYELI az SQL-kiszolgálókat olyan fenyegetésekkel szemben, mint például az SQL-injektálás, a találgatásos támadás és a jogosultságokkal való visszaélés. Ez a szolgáltatás lehetővé teszi, hogy a rendszer részletes biztonsági riasztásokat biztosítson Azure Security Center a gyanús tevékenység részleteit, útmutatást nyújt a fenyegetések enyhítéséhez, valamint az Azure Sentinel használatával folytatott vizsgálatok folytatásának lehetőségeiről.

>[!TIP]
> Az SQL Machines speciális adatbiztonsága Azure Security Center [fejlett adatbiztonsági csomagja](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security), amely már elérhető az Azure SQL Database, a szinapszis és az SQL által felügyelt példányok számára.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Fejlett adatbiztonság beállítása SQL-gépekhez 

Az Azure Security Center az SQL Machines szolgáltatáshoz készült speciális adatbiztonságának beállítása két lépést tesz szükségessé:

* A Log Analytics ügynök kiépítése az SQL Server gazdagépén. Ez biztosítja az Azure-hoz való kapcsolódást.

* Engedélyezze a választható csomagot Security Center díjszabási és beállítási lapján.

A fentiek mindegyikét alább találja.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>1. lépés A Log Analytics-ügynök kiépítése az SQL Server gazdagépén:

- **SQL Server Azure** -beli virtuális gépen – ha az SQL-gépe Azure-beli virtuális gépen üzemel, akkor [automatikusan kiépítheti a log Analytics-ügynököt](security-center-enable-data-collection.md#workspace-configuration). Azt is megteheti, hogy az Azure-beli [virtuális gép hozzáadásának](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)manuális eljárását is követheti.

- **SQL Server az Azure arc** -ban – ha a SQL Server egy [Azure arc](https://docs.microsoft.com/azure/azure-arc/) -gépen üzemel, akkor a log Analytics-ügynököt a Security Center javaslat használatával telepítheti: "log Analytics ügynökének telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)". Azt is megteheti, hogy az [Azure arc dokumentációjában](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)követheti a manuális eljárást.

- **Helyszíni SQL Server** – ha a SQL Server Azure arc nélküli helyszíni Windows-gépen üzemel, két lehetőség közül választhat az Azure-hoz való csatlakozáshoz:
    
    - Az **Azure arc üzembe helyezése** – bármely Windows-gépet összekapcsolhat Security Centerhoz. Az Azure arc azonban mélyebb integrációt tesz lehetővé az *összes* Azure-környezetben. Ha beállítja az Azure-ívet, a portálon megjelenik a **SQL Server – Azure arc** oldal, és a biztonsági riasztások az oldal dedikált **Biztonság** lapján jelennek meg. Ezért az első és ajánlott lehetőség az [Azure arc beállítása a gazdagépen](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) , és kövesse az Azure-beli **SQL Server vonatkozó**utasításokat.
        
    - **A Windows rendszerű gép összekapcsolása az Azure arc nélkül** – ha úgy dönt, hogy a Windows-gépen futó SQL Servert az Azure arc használata nélkül kívánja összekapcsolni, kövesse a Windows rendszerű [gépek összekapcsolása a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>2. lépés Engedélyezze a választható csomagot Security Center díjszabási és beállítási lapján:

1. A Security Center oldalsávján nyissa meg a **díjszabási & beállítások** lapot.

    - Ha **Azure Security Center alapértelmezett munkaterületét** használja ("alapértelmezettmunkaterület-[előfizetési azonosító]-[régió]"), válassza ki a megfelelő **előfizetést**.

    - Ha **nem alapértelmezett munkaterületet**használ, válassza ki a megfelelő **munkaterületet** (ha szükséges, adja meg a munkaterület nevét a szűrőben):

        ![cím](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. A (z) rendszerű **SQL Server-kiszolgálók** beállításának engedélyezése az engedélyezéshez. 

    [![Security Center díjszabási oldal opcionális csomagokkal](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    A számítógépeken futó SQL-kiszolgálók speciális adatbiztonsága a kiválasztott munkaterülethez csatlakoztatott összes SQL-kiszolgálón engedélyezve lesz. A védelem teljes mértékben aktív lesz a SQL Server első újraindítása után. 

    >[!TIP] 
    > Új munkaterület létrehozásához kövesse az [log Analytics-munkaterület létrehozása](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)című témakör utasításait.


1. Szükség esetén az e-mail-értesítések konfigurálása a biztonsági riasztásokhoz. 
    Megadhatja a címzettek listáját, amely e-mailben értesítést kap a Security Center riasztások létrehozásakor. Az e-mail tartalmaz egy közvetlen hivatkozást a riasztáshoz Azure Security Center az összes releváns részletet. További információ: [e-mail-értesítések beállítása biztonsági riasztásokhoz](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>A sebezhetőségi felmérési jelentések megismerése

A sebezhetőség-felmérési szolgáltatás hetente egyszer vizsgálja meg az adatbázisokat. A vizsgálatok a hét azon napján futnak, amelyen engedélyezte a szolgáltatást.

A sebezhetőségi felmérés irányítópultja áttekintést nyújt az összes adatbázisra vonatkozó értékelési eredményekről, valamint az egészséges és a nem kifogástalan adatbázisok összefoglalásáról, valamint a kockázatos eloszlások szerinti sikertelen ellenőrzések általános összegzéséről.

A sebezhetőségi felmérés eredményei közvetlenül a Security Center tekinthetők meg.

1. A Security Center oldalsávján nyissa meg a **javaslatok** lapot, és válassza ki az **SQL Database-kiszolgálókon a gépeken lévő ajánlott biztonsági réseket (előzetes verzió)**. További információ: [Security Center javaslatok](security-center-recommendations.md). 


    [![* * A gépeken futó SQL-adatbázisok biztonsági réseit szervizelni kell (előzetes verzió) * * javaslat](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Megjelenik a javaslat részletes nézete.

    [![A * * biztonsági rések a gépeken lévő SQL-adatbázisokra vonatkozó részletes nézetét szervizelni kell (előzetes verzió) * * javaslat](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. További részleteket a következő részletezésben talál:

    * A beolvasott erőforrások (adatbázisok) és a tesztelt biztonsági ellenőrzések listájának áttekintéséhez válassza ki az Önt érdeklő kiszolgálót.

    * Az adott SQL-adatbázis által csoportosított biztonsági rések áttekintéséhez válassza ki az Önt érdeklő adatbázist.

    Az egyes nézetekben a biztonsági ellenőrzések **Súlyosság**szerint rendezve jelennek meg. Egy adott biztonsági vizsgálatra kattintva megtekintheti a részleteket tartalmazó ablaktáblát **, amely** **leírja, hogyan**javíthatja, és egyéb kapcsolódó információkat, például a **hatást** vagy a **teljesítménytesztet**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Összetett veszélyforrások elleni védelem az SQL-kiszolgálókon a gépeken riasztások
A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják az SQL-gépek eléréséhez vagy kiaknázásához. Ezek az események riasztásokat indíthatnak a riasztások [SQL Database és SQL Data Warehouse szakaszban](alerts-reference.md#alerts-sql-db-and-warehouse), a riasztások hivatkozása lapon.



## <a name="explore-and-investigate-security-alerts"></a>Biztonsági riasztások feltárása és kivizsgálása

A biztonsági riasztások a Security Center riasztások lapján, az erőforrás Biztonság lapján vagy a riasztási e-mailek közvetlen hivatkozásán keresztül érhetők el.

1. A riasztások megtekintéséhez válassza a **biztonsági riasztások** elemet Security Center oldalsávján, és válassza ki a riasztást.

1. A riasztásokat önállóan, részletes szervizelési lépésekkel és vizsgálati információkkal tervezték meg. Részletesebben is megvizsgálhat további Azure Security Center és Azure Sentinel-képességeket a szélesebb körű nézet érdekében:

    * A további vizsgálatok érdekében engedélyezze SQL Server naplózási funkcióját. Ha Ön Azure Sentinel-felhasználó, feltöltheti az SQL-naplózási naplókat a Windows biztonsági napló eseményeiből a Sentinel szolgáltatásba, és élvezheti a részletes nyomozást. [További információ a SQL Server naplózásról](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * A biztonsági helyzet javításához használja Security Center javaslatait az egyes riasztásokban jelzett gazdagéphez. Ez csökkenti a jövőbeli támadások kockázatát. 

    [További információ a riasztások kezeléséről és megválaszolásáról](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>További lépések

Kapcsolódó anyagok esetében tekintse meg a következő cikket:

- [SQL Database és SQL Data Warehouse biztonsági riasztásai](alerts-reference.md#alerts-sql-db-and-warehouse)
- [E-mail-értesítések beállítása biztonsági riasztásokhoz](security-center-provide-security-contact-details.md)
- [További információ az Azure Sentinelről](https://docs.microsoft.com/azure/sentinel/)
- [Az Azure Security Center speciális adatbiztonsági csomagja](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)