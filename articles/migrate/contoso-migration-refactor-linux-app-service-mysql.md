---
title: Az Azure App Service és az Azure-beli MySQL-Contoso Linux service ügyfélszolgálati alkalmazás újrabontása |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso refactors telepítse át a helyszíni Linuxos app, Azure App Service használatával a GitHub webes réteg és az Azure SQL Database.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 60bd3aef20e39a9f451f54164ef3fb8ef682bc3e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809688"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Contoso áttelepítése: Egy Contoso Linux-szolgáltatás desk több régióban az Azure App Service-ben, a Traffic Manager és az Azure-beli MySQL alkalmazás újrabontása

Ez a cikk bemutatja, hogyan Contoso refactors telepítse át a helyszíni kétrétegű Linux szolgáltatás desk alkalmazás (osTicket), a GitHub-integráció az Azure App Service és az Azure-beli MySQL.

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait. A sorozat tartalmazza a háttér-információkat, és a egy áttelepítési infrastruktúra beállítását, és futtassa a különböző típusú migrálások bemutató forgatókönyvek. Forgatókönyvek egyre összetettebbé válnak. Idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető   
[5. cikk: Az Azure virtuális gépein egy alkalmazás újratárolása](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel360 alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Elérhető 
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek Azure Site Recovery használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejezése | Elérhető
[8. cikk: Egy Azure virtuális gépek és az Azure MySQL Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Azure Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Az Azure Web Apps és az Azure SQL database alkalmazás újrabontása](contoso-migration-refactor-web-app-sql.md) | A Contoso a SmartHotel360 alkalmazást áttelepíti az Azure Web Apps és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány, a Database Migration Assistant | Elérhető
10. cikk: Az Azure Web Apps és az Azure MySQL egy Linux-alkalmazás újrabontása | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Ez a cikk
[11. cikk: Refaktorovat a TFS-t az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel360 alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető

Ebben a cikkben a Contoso kétrétegű Linuxos Apache MySQL PHP (LAMP-) szolgáltatás desk alkalmazás (osTicket) áttelepíti az Azure-bA. Ha szeretné a nyílt forráskódú alkalmazás használja, letöltheti azt [GitHub](https://github.com/osTicket/osTicket).


## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének eléréséhez:

- **Üzleti növekedés cím**: Contoso növekvő, és új piacokra áthelyezése. További vásárlói szolgáltatási ügynökök szükséges. 
- **Méretezési csoport**: A megoldás, hogy a Contoso adhat hozzá további felhasználói szolgáltatási ügynökök a üzleti skálázását követve rugalmasan méretezhető kell építeni.
- **Ellenálló-képesség növelésére**:  A rendszer az elmúlt kérdéseket az érintett kizárólag belső használatra. Az új üzleti modell a külső felhasználók hatással lesz, és minden alkalommal Contoso kell az alkalmazást, majd futtassa.

## <a name="migration-goals"></a>Migrálási célok

A Contoso felhőalapú csapat rendelkezik rögzített az áttelepítés célok le annak érdekében, hogy a legmegfelelőbb migrálási módszer:

- Az alkalmazás horizontális túl az aktuális helyi kapacitás és teljesítmény.  Contoso mozgatása kihasználásához az Azure igény szerinti méretezés az alkalmazás.
- Contoso szeretne áthelyezni az alkalmazás kódbázis folyamatos teljesítési folyamatot.  Alkalmazás módosításai akkor lesznek leküldve, a GitHub, Contoso szeretne üzembe helyezése nélkül munkatársak feladatai ezeket a módosításokat.
- Az alkalmazás a növekedési és feladatátvételi képességeket rugalmasnak kell lennie. Contoso szeretné telepíteni az alkalmazást két különböző Azure-régióban, és állítsa be az automatikus méretezését.
- A Contoso biztosítani szeretné az alkalmazást a felhőbe való áthelyezése után minimalizálása érdekében a adatbázis-rendszergazdai feladatokat.

## <a name="solution-design"></a>Megoldásterv
Után rögzíthet a célokat és követelményeket állapította meg, a Contoso tervez és egy üzembehelyezési megoldással áttekinti, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, az áttelepítés használandó.


## <a name="current-architecture"></a>Aktuális architektúra

- Az alkalmazás többszintű (OSTICKETWEB és OSTICKETMYSQL) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió).
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).

![Aktuális architektúra](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Javasolt architektúra

Íme a javasolt architektúra:

- A webalkalmazás szint a OSTICKETWEB létrehozásával az Azure App Service a két Azure-régióban lesz migrálva. Az Azure App Service Linux rendszeren a PHP 7.0 Docker-tároló használatával hajtja végre.
- Az alkalmazáskód GitHub kerül, és Azure-webalkalmazás folyamatos készregyártás a GitHub használatára lesz konfigurálva.
- Az Azure App kiszolgálók lesz telepítve, az elsődleges (USA keleti RÉGIÓJA 2) és a másodlagos (USA középső RÉGIÓJA) régióban.
- A TRAFFIC Manager hoznak létre a két Azure Web Apps elé mindkét régióban.
- A TRAFFIC Manager kényszerítése a forgalom – USA keleti RÉGIÓJA 2 prioritás módban lesznek konfigurálva.
- Az USA 2. keleti régiója, az Azure App kiszolgáló offline állapotba kerül, ha a felhasználók a kapcsolaton keresztül érheti alkalmazás az USA középső Régiójában.
- Az alkalmazás-adatbázis telepíti át a rendszer az Azure MySQL PaaS-szolgáltatás a MySQL Workbench-eszközökkel. A helyszíni adatbázis lesz biztonsági mentése helyileg, és közvetlenül az Azure MySQL visszaállítva.
- Az adatbázis helyezkednek el az elsődleges régióban USA keleti RÉGIÓJA 2, az adatbázis-alhálózat (ÉLES-DB-EUS2) az éles hálózaton (VNET-ÉLES-EUS2):
- Ezek migráláshoz olyan éles környezetbeli számítási feladatokra, mivel az alkalmazás Azure-erőforrások helyezkednek el az éles erőforráscsoportban **ContosoRG**.
- A Traffic Manager-erőforrás lesz telepítve, a Contoso infrastruktúra erőforráscsoport **ContosoInfraRG**.
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


![Forgatókönyv-architektúra](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Áttelepítési folyamat

Contoso befejezi az áttelepítési folyamat a következő:

1. Első lépésként a Contoso rendszergazdák beállítása az Azure-infrastruktúrát, beleértve a kiépítés az Azure App Services, állítja be a Traffic Managert és kiépítése az Azure MySQL-példányt.
2. Után az Azure előkészítése, azok az adatbázisban a MySQL Workbench használatával telepítse át. 
3. Után az adatbázis fut-e az Azure-ban, azokat egy privát GitHub-adattárat, az Azure App Service folyamatos készregyártással a mentést, és töltse be a osTicket alkalmazással.
4. Az Azure Portalon, töltse be az alkalmazást a Githubról, a Docker-tárolóban futó Azure App Service-ben. 
5. A Teljesítménybeállítások DNS-beállításokat, és az alkalmazás automatikus skálázásának konfigurálása.

![Áttelepítési folyamat](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service/) | A szolgáltatás fut, és méretezhető alkalmazások az Azure PaaS szolgáltatással a webhelyekhez.  | Díjszabás mérete a példányok és a szükséges szolgáltatások alapul. [További információk](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | Egy terheléselosztót, amely az Azure-ban vagy külső webhelyek és szolgáltatások közvetlen felhasználók számára a DNS-használja. | Díjszabás és a DNS-lekérdezések száma, a figyelt végpontok száma alapul. | [További információk](https://azure.microsoft.com/pricing/details/traffic-manager/).
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Az adatbázis a nyílt forráskódú MySQL-kiszolgáló-összetevőjére épül. Biztosít egy teljes körűen felügyelt, nagyvállalati szintű közösségi MySQL-adatbázis szolgáltatás alkalmazások fejlesztéséhez és üzembe helyezéséhez. | A számítási, tárolási és biztonsági mentés követelményeinek díjszabás alapján. [További információk](https://azure.microsoft.com/pricing/details/mysql/).

 
## <a name="prerequisites"></a>Előfeltételek

Íme a Contoso kell futtatni az ebben a forgatókönyvben.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | A Contoso korábban létrehozott előfizetések Ez a cikk a sorozat. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne. 
**Azure-infrastruktúra** | A Contoso beállítása az Azure-infrastruktúra leírtak szerint [áttelepítése az Azure-infrastruktúra](contoso-migration-infrastructure.md).



## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso az áttelepítés elvégzéséhez:

> [!div class="checklist"]
> * **1. lépés: Az Azure App Services kiépítése**: Contoso-rendszergazdák is üzembe helyezi a Web Apps, az elsődleges és másodlagos régióban.
> * **2. lépés: Állítsa be a Traffic Managert**: Állítsa be Traffic Managert elé a Web Apps, a forgalom-útválasztási és teherelosztási.
> * **3. lépés: MySQL kiépítése**: Az Azure-ban akkor az Azure MySQL-adatbázis egy példányának kiosztása.
> * **4. lépés: Az adatbázis migrálása**: Akkor telepítse át az adatbázist a MySQL Workbench használatával. 
> * **5. lépés: Állítsa be a Githubot**: Azok az alkalmazás webhelyek/kódot egy helyi GitHub-adattár beállítása.
> * **6. lépés: A webes alkalmazások üzembe helyezése**: A web apps, a Githubról azokat üzembe.




## <a name="step-1-provision-azure-app-services"></a>1. lépés: Provision Azure App Services

Contoso rendszergazdák két Web apps (egy, az egyes régiókban) üzembe helyezése az Azure App Services használatával.

1. Akkor hozzon létre egy webalkalmazás erőforrást, az elsődleges régióban USA keleti RÉGIÓJA 2 (**osticket-eus2**) az Azure Marketplace-ről.
2. Az erőforrás, helyezze az éles erőforráscsoport **ContosoRG**.

    ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. Akkor hozzon létre egy új App Service-csomag, az elsődleges régióban (**APP-ÜKtgE-EUS2**), a standard szintű mérettel.

     ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Válassza ki a Linux operációs rendszer, a PHP 7.0 futtatókörnyezeti verem, amely egy Docker-tárolót.

    ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. Akkor hozzon létre egy második webalkalmazás (**osticket-cus**), és az App service-csomag esetében az USA középső régiójában.

    ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**További segítségre van szüksége?**

- Ismerje meg [az Azure App Service Web apps](https://docs.microsoft.com/azure/app-service/overview).
- Ismerje meg [Linuxon futó Azure App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>2. lépés: Állítsa be a Traffic Managert

Contoso rendszergazdák be Traffic Manager számára a bejövő webes kéréseket a Web Apps a osTicket webes réteg futó be.

1. Hoznak létre, egy Traffic Manager-erőforrás (**osticket.trafficmanager.net**) az Azure Marketplace-ről. Prioritás, hogy az USA keleti RÉGIÓJA 2, az az elsődleges hely útválasztás használata. Azok az erőforrás helyezze el az infrastruktúra erőforráscsoportban (**ContosoInfraRG**). Vegye figyelembe, hogy a Traffic Manager globális és a egy adott helyen nincs kötve

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. Most, akkor a Traffic Manager konfigurálása végpontokkal. East US 2 webalkalmazás adnak hozzá az elsődleges hely (**osticket-eus2**), és az USA középső Régiójában, a másodlagos (**osticket-cus**).

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. Miután hozzáadta a végpontok, azok figyelheti őket.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**További segítségre van szüksége?**

- Ismerje meg [a Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- Ismerje meg [irányítaná a forgalmat magas prioritású végpontra](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>3. lépés: MySQL-hez készült Azure-adatbázis kiépítése

Contoso rendszergazdák egy MySQL-adatbázispéldányban az USA keleti RÉGIÓJA 2 elsődleges régióban üzembe helyezheti.

1. Az Azure Portalon, egy Azure Database for MySQL erőforrás létrehozásához. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Adnak hozzá a név **contosoosticket** az Azure-adatbázisban. Vegye fel az adatbázist az éles erőforráscsoport **ContosoRG**, és a hitelesítő adatok megadása.
3. A helyi MySQL-adatbázis verziója 5.7, így azok válassza ki a jelenlegi kompatibilitási verziót. Az alapértelmezett méretek, amely az adatbázis esetén is megbízhatóan használnak.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. A **biztonsági másolat Redundanciabeállításai**, akkor válassza ki a használandó **Georedundáns**. Ez a beállítás lehetővé teszi őket, állítsa vissza az adatbázist a másodlagos régióban USA középső RÉGIÓJA, kimaradás esetén. Ezt a beállítást csak akkor konfigurálható, az adatbázis kiépítésekor.

    ![Redundancia](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Ezek a kapcsolat biztonságának beállítását. Az adatbázis > **kapcsolatbiztonság**, azok tűzfalszabályok beállítása, hogy az adatbázis az Azure-szolgáltatásokhoz való hozzáférést.
5. A kezdő és záró IP-címeket adnak hozzá a helyi munkaállomáson ügyfél IP-címét. Ez lehetővé teszi a webalkalmazások, a MySQL-adatbázishoz, valamint az adatbázis-ügyfél, amely az áttelepítés eléréséhez.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>4. lépés: Az adatbázis migrálása

Contoso rendszergazdák telepítse át az adatbázis biztonsági mentése és visszaállítása, MySQL eszközök használatával. Ezek a MySQL Workbench telepítése, az adatbázis biztonsági mentése a OSTICKETMYSQL és majd állítsa vissza az Azure Database for MySQL-kiszolgálót.

### <a name="install-mysql-workbench"></a>A MySQL Workbench telepítése

1. Ellenőrizze, hogy a [Előfeltételek és a letöltéseket a MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Windows-szolgáltatást a MySQL Workbench telepítése a [telepítési utasításokat](https://dev.mysql.com/doc/workbench/en/wb-installing.html). A gép, amelyen telepíteni a OSTICKETMYSQL virtuális Gépet, és az interneten keresztül az Azure elérhetőnek kell lennie.
3. A MySQL Workbench akkor hozzon létre egy MySQL kapcsolatot OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. Az adatbázis-kivitel **osticket**, önálló helyi fájlba.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. Után az adatbázis biztonsági helyileg, azok hozzon létre egy kapcsolatot az Azure Database for MySQL-példányt.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. Most azokat is importálja (helyreállíthatja) a az Azure-beli MySQL-példányát, az önálló adatbázis fájlt. A példány létrejön egy új sémát (osticket).

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. Adatok visszaállítása után Workbench használatával kérhető le, és megjelenik az Azure Portalon.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Végül akkor frissítenie kell az adatbázis-információ a web Apps. A MySQL-példányt, akkor nyissa meg **kapcsolati karakterláncok**. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. Karakterláncok listájában, keresse meg a webalkalmazás beállításait, és kattintson a másolást.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. Azok a Jegyzettömb ablak megnyitása és illessze be a karakterláncot egy új fájlba, és frissítse úgy, hogy megfeleljen a osticket adatbázis, a MySQL-példányt és a hitelesítő adatok beállítások.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Ellenőrzik a kiszolgáló nevét és a bejelentkezési **áttekintése** a MySQL-példányban, az Azure Portalon.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>5. lépés: Állítsa be a Githubot

Contoso-rendszergazdák az Azure MySQL-ben hozzon létre egy új, privát GitHub-adattárat és a egy kapcsolatot a osTicket adatbázis sets. Ezt követően töltődnek be az Azure Web App-e az alkalmazással.  

1.  Tallózással keresse meg a OsTicket szoftver nyilvános GitHub-adattárat, és elágaztatását, a Contoso GitHub-fiókhoz.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. Ezzel, akkor keresse meg a **tartalmaznak** mappát, és keresse meg a **ost-config.php** fájlt.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. A fájl megnyílik a böngészőben, és azok szerkeszthetik.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. A szerkesztőben, adatbázis adatainak frissítése, kifejezetten **DBHOST** és **DBUSER**. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Ezután ezek a módosítások véglegesítéséhez.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. Minden webalkalmazás (**osticket-eus2** és **osticket-cus**), módosíthatják a **Alkalmazásbeállítások** az Azure Portalon.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. A kapcsolati karakterlánc nevét adja meg, azok **osticket**, és másolja be a Jegyzettömbben a karakterlánc a **terület érték**. Kiválasztják **MySQL** mellett a karakterláncot, és mentse a beállításokat a legördülő listában.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>6. lépés: A webalkalmazások konfigurálása

Az áttelepítési folyamat utolsó lépéseként Contoso rendszergazdák a webalkalmazások konfigurálása a osTicket webhelyek szolgáltatással.



1. Az elsődleges web app alkalmazásban (**osticket-eus2**) megnyitják **üzembe helyezési lehetőség** , és állítsa a forrás **GitHub**.

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. Akkor válassza ki az üzembe helyezési beállítások.

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. Miután beállította a beállításokat, a konfiguráció azt mutatja be, függőben lévő az Azure Portalon.

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. Miután a konfiguráció frissítésekor, és a osTicket webalkalmazás be lett töltve a Githubról a Docket tárolóba az Azure App Service szolgáltatásban futó, a hely aktív jeleníti meg.

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Ezek a fenti lépések ismételje meg a másodlagos webalkalmazás (**osticket-cus**).
6. A hely konfigurálása után érhető el a Traffic Manager-profil használatával. A DNS-nevet az új helyet a osTicket alkalmazás. [További információk](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. A Contoso biztosítani szeretné egy könnyen megjegyezhető DNS-név. Akkor hozzon létre egy alias (CNAME) rekord **osticket.contoso.com** mutat a Traffic Manager neve, a DNS-ben a tartományvezérlőkön, amelyek.

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Mindkét konfigurálják a **osticket-eus2** és **osticket-cus** webes alkalmazásokat, hogy az egyéni gazdaneveket.

    ![Alkalmazás konfigurálása](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Az automatikus skálázás beállítása

Végül, állítsa be az alkalmazás automatikus skálázást. Ez biztosítja, hogy ügynökök az alkalmazást használja, az alkalmazáspéldány növelheti és csökkentheti az üzleti igényeinek megfelelően. 

1. Az App Service-ben **APP-SRV-EUS2**, megnyitják **skálázási egység**.
2. Egy szabályt, amely növeli a példányok száma eggyel, ha a Processzorhasználat (%) az aktuális példány 70 % fölé emelkedik 10 percig, konfigurálja egy új automatikus skálázási beállítás.

    ![Automatikus méretezés](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. Ugyanazt a beállítást, konfigurálhatja a **APP-SRV-CUS** annak érdekében, hogy ugyanez a viselkedés érvényes, ha az alkalmazás átadja a feladatokat a másodlagos régióba. Az egyetlen különbség, hogy azok a példány-korlátjának beállítása 1 mivel ez csak a feladatátvételek használható.

   ![Automatikus méretezés](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött a osTicket app van újratervezhetők futnia az Azure-webalkalmazások folyamatos készregyártással, privát GitHub-tárház használatával. Az alkalmazás fut, a nagyobb rugalmasság két régióban. A osTicket adatbázis fut. az Azure database for MySQL-hez az áttelepítés után a PaaS-platformra történő

Távolítsa el, Contoso kell tegye a következőket: 
- Távolítsa el a VMware virtuális gépeket a vCenter-készlet.
- Távolítsa el a helyszíni virtuális gépek helyi biztonsági mentési feladatok.
- Frissítés belső dokumentáció megjelenítése az új helyek és IP-címeket. 
- Tekintse át az olyan erőforrások, a helyszíni virtuális gépek kommunikáljanak, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.
- Konfigurálja újra a figyelése, mutasson a osticket-trafficmanager.net URL-címen, nyomon követéséhez, hogy az alkalmazás működik és fut.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az alkalmazás most már fut a Contoso kell teljesen üzembe helyezése és biztonságos meg új infrastruktúrára.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapat az alkalmazás minden olyan biztonsági problémák meghatározása érdekében tekintse át. Azonosítottak, hogy a osTicket app és a MySQL-adatbázispéldányban közötti kommunikáció SSL-hez nincs konfigurálva. Akkor kell, hogy, hogy a nem lesznek-e az adatbázis-forgalom. [További információk](https://docs.microsoft.com/azure/mysql/howto-configure-ssl).

### <a name="backups"></a>Biztonsági másolatok

- A osTicket web apps a rendszerállapot-adatok nem tartalmaznak, és így nem kell biztonsági mentést kell készíteni.
- Nem szükséges az adatbázis biztonsági mentésének konfigurálásához. Azure Database for MySQL-hez a kiszolgáló biztonsági mentése automatikusan létrehozza és tárolja. Kiválasztották, a georedundáns tárolás használata az adatbázis, így rugalmas és éles használatra kész. Biztonsági másolatokat állíthatja vissza a kiszolgáló egy-időponthoz használható. [További információk](https://docs.microsoft.com/azure/mysql/concepts-backup).


### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Nem tartoznak licencelési problémák a PaaS üzemelő példány számára.
- Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.



