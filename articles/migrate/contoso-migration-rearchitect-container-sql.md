---
title: Egy Contoso-alkalmazást egy Azure container és az Azure SQL Database újratervezése |} A Microsoft Docs
description: Ismerje meg, hogy a Contoso Azure Windows-tárolók és az Azure SQL Database az alkalmazás újratervezése.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 9fd83124585e3a0eb19c43e278eeeacb6ec4409c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094105"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso áttelepítési: egy Azure container és az Azure SQL Database a helyszíni alkalmazás újratervezése

Ez a cikk bemutatja, hogyan Contoso áttelepíti, és az Azure-ban a SmartHotel360 az alkalmazás újratervezése. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép az Azure Windows-tárolókat, és az alkalmazás-adatbázis egy Azure SQL Database-adatbázishoz.

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak. További cikkek hozzáadása is várható.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető   
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[Cikk 6: Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazások Újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel360 alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Elérhető 
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek Azure Site Recovery használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejezése | Elérhető
[A cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Azure Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure Web Apps és az Azure SQL database](contoso-migration-refactor-web-app-sql.md) | A Contoso a SmartHotel360 alkalmazást áttelepíti az Azure Web Apps és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány, a Database Migration Assistant | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Elérhető 
[11. cikk: Újrabontás a TFS-t az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Ez a cikk
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető 
[Cikk 14: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető

Ebben a cikkben a Contoso áttelepíti a kétrétegű Windows WPF, a XAML SmartHotel360 űrlapalkalmazásokat VMware virtuális gépeken futó Azure-bA. Ha szeretné használni ezt az alkalmazást, nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai

A Contoso informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és ennek eredményeképpen nincs a helyszíni rendszerek és infrastruktúra nyomás.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára.  Az üzleti igények informatikai gyorsan, és nem Hulladékmennyiség idő vagy költséget takaríthat meg, így gyorsabban továbbítása az ügyfelek igényei.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a versenyképes sikeres engedélyezése a módosításokat, képesnek kell lennie.  Azt nem a módon, vagy egy üzleti blocker válnak.
- **Méretezési csoport**: a vállalat növekedésével sikeres, a Contoso informatikai kell megadnia rendszerek, amelyek képesek a ugyanolyan ütemben nő.
- **Költségek**: a Contoso biztosítani szeretné a licencelési költségek csökkentése érdekében.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok a leginkább megfelelő áttelepítési módszer meghatározásához használt.

**Célok** | **Részletek**
--- | --- 
**Alkalmazás szükséges előfeltételeknek** | Az alkalmazás az Azure-ban marad, kritikus fontosságú, mivel még ma.<br/><br/> Teljesítmény ugyanazokat a lehetőségeket azonban jelenleg nem VMWare kell rendelkeznie.<br/><br/> A Contoso biztosítani szeretné a Windows Server 2008 R2, amelyen az alkalmazás jelenleg fut, és arra, hogy az alkalmazás be környezetei már nem.<br/><br/> A Contoso biztosítani szeretné egy modern PaaS-adatbázis platformon, ami minimalizálja a felügyeleti kell esniük az SQL Server 2008 R2.<br/><br/> Contoso szeretné kihasználni a lévő SQL Server-licenc és frissítési garanciával rendelkező, ahol csak lehetséges.<br/><br/> A Contoso biztosítani szeretné lehet majd vertikális felskálázni az alkalmazás webes réteg.
**Korlátozások** | Az alkalmazás egy ASP.NET-alkalmazás és a egy ugyanazon a virtuális Gépen futó WCF szolgáltatás áll. A Contoso biztosítani szeretné ez elosztja a két webalkalmazást az Azure App Service használatával. 
**Szükséges Azure-előfeltételeknek** | A Contoso biztosítani szeretné az alkalmazás áthelyezése az Azure-ba, és futtassa azt az alkalmazás élettartamának bővítése egy tárolót. Azt szeretné kezd teljesen az alkalmazás megvalósítása az Azure-ban. 
**Fejlesztés és üzemeltetés** | A Contoso biztosítani szeretné egy fejlesztési és üzemeltetési modell az Azure DevOps-szolgáltatásokkal a kód létrehozza és kibocsátásában.

## <a name="solution-design"></a>Megoldásterv

Után rögzíthet célokat és követelményeket állapította meg, a Contoso tervez és a egy üzembehelyezési megoldással tekintse át, és azonosítja az áttelepítési folyamat, beleértve a Contoso az áttelepítés által használt Azure-szolgáltatások.

### <a name="current-app"></a>Aktuális alkalmazás

- A SmartHotel360 helyszíni alkalmazás többszintű (WEBVM és SQLVM) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


### <a name="proposed-architecture"></a>Javasolt architektúra

- Az alkalmazás az adatbázisszinten, a Contoso Azure SQL Database képest az SQL Server használatával [Ez a cikk](https://docs.microsoft.com/azure/sql-database/sql-database-features). Ezt úgy döntött, hogy nyissa meg az Azure SQL Database néhány okok miatt:
    - Az Azure SQL Database felügyelt relációs adatbázis-szolgáltatás. Az adminisztrációt több szolgáltatási szinten kiszámítható teljesítményt kínál. Előny az állásidő, beépített intelligens optimalizálással, és globális méretezhetőség és rendelkezésre állás a dinamikus méretezhetőség.
    - Contoso kihasználja az egyszerűsített Data Migration Assistant (DMA) segítségével mérheti fel, és a helyszíni adatbázis migrálása az Azure SQL.
    - Frissítési garanciával működő a Contoso a kedvezményes díjszabás a egy SQL Database, az Azure Hybrid Benefit használatával az SQL Server meglévő licenceinek tudjon cserélni. Ez akár 30 %-os megtakarítás nyújtani.
    - Az SQL Database számos olyan biztonsági funkciók, például mindig titkosított, dinamikus adatmaszkolást és a sorszintű biztonság/fenyegetések észlelése.
- Az alkalmazás webes réteg Contoso határozott alakíthatja át a Windows-tároló használata az Azure DevOps-szolgáltatásokkal.
    - Contoso az Azure Service Fabric-alkalmazás üzembe helyezése, és kérje le a Windows-tároló rendszerképét az Azure Container Registry (ACR).
    - Az alkalmazás hangulatelemzés kiterjesztése prototípusát egy másik szolgáltatás a Service Fabric segítségével a Cosmos DB-hez csatlakoztatott hajtja végre.  Ezzel kiolvasni az információt a Tweeteket, és az alkalmazás megjelenítéséhez.
- DevOps-folyamat implementálásához, Contoso forrás kód Management (SCM), a Git-tárház fogja használni az Azure DevOps.  Automatizált buildekig és kiadások kód felépítéséhez használt, és hogyan telepítheti az Azure Container Registry és Azure Service Fabric.

    ![Forgatókönyv-architektúra](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Megoldás áttekintése
Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Szempontok** | **Részletek**
--- | ---
**Szakemberek számára** | A SmartHotel360 alkalmazáskód kell módosítani az Azure Service fabric az áttelepítéshez. Azonban részéről az erőfeszítés, minimális, a módosítások a Service Fabric SDK-eszközök használatával.<br/><br/> A Service Fabric váltás, a Contoso elindíthatja, az alkalmazás eredeti kódbázis veszélyeztetése nélkül az idő múlásával gyorsan hozzá mikroszolgáltatásokat fejleszthet.<br/><br/> Windows-tárolók ugyanazokat az előnyöket tárolókként általában kínálnak. Javítják a rugalmasságot, a hordozhatóságot és a vezérlés.<br/><br/> Contoso kihasználhatja a frissítési garanciával rendelkező SQL Server és a Windows Server az Azure Hybrid Benefit használatával befektetése jelenti.<br/><br/> Az áttelepítés után azt nem kell többé a Windows Server 2008 R2 támogatása. [További információk](https://support.microsoft.com/lifecycle).<br/><br/> Contoso is konfigurálhatja a webes szint, az alkalmazás több példánya, így már nem olyan hibaérzékeny pont.<br/><br/> Már nem lesz az SQL Server 2008 R2 elévülési függ.<br/><br/> Az SQL Database támogatja a Contoso műszaki követelményeknek. Contoso-rendszergazdák a helyszíni adatbázis, a Database Migration Assistant eszközzel értékelni, és kompatibilis található.<br/><br/> SQL-adatbázis, amely a Contoso nem kell beállítania beépített hibatűrő képességgel rendelkezik. Ez biztosítja, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | Tárolók összetettebbek, mint más áttelepítési lehetőségek. A tárolók elsajátítható contoso problémát okozhatja.  Egy új szint által biztosított nagy mennyiségű értéket a görbe érték összetettségi vezetnek.<br/><br/> Az üzemeltetési csapat, contoso megértéséhez, és az alkalmazás támogatja az Azure, tárolók és mikroszolgáltatások felpörög kell.<br/><br/> Ha a Contoso Data Migration Assistant helyett Data Migration Service-t használ az adatbázis áttelepítése, azt nem kell az infrastruktúra készen áll a migrálás adatbázisok ipari méretekben.



### <a name="migration-process"></a>Áttelepítési folyamat

1. Contoso látja el a Windows Azure service fabric-fürtöt.
2. Egy Azure SQL-példánnyal látja, és áttelepíti a a SmartHotel360 adatbázis.
3. Contoso alakítja át a webes szint virtuális gép egy Docker-tárolót, a Service Fabric SDK-eszközökkel.
4. A service fabric-fürt és az ACR-REL, és telepíti az alkalmazást az Azure service fabric használatával.

    ![Áttelepítési folyamat](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Értékeli és észleli a kompatibilitási problémákat, amelyek hatással lehet a funkció az Azure-ban. A DMA értékeli a funkcióparitás SQL források és célok között, és a teljesítmény- és megbízhatóságbeli fejlesztéseket javasol. | Ez egy ingyenesen letölthető eszköz.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Itt egy intelligens, teljes körűen felügyelt felhőalapú relációsadatbázis-szolgáltatás. | Költség funkciók, az átviteli sebesség és a mérete alapján. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Az Azure Container Registrybe](https://azure.microsoft.com/services/container-registry/) | Tárolja a lemezképeket a tárolópéldányok összes típusára vonatkozóan. | A Funkciók, a storage és a használati időtartama alapján költsége. [További információk](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Hozza létre, és folyamatosan elérhető, skálázható és elosztott alkalmazások üzemeltetése | Költség méretét, helyét és a számítási csomópontok időtartama alapján. [További információk](https://azure.microsoft.com/pricing/details/service-fabric/).
[Az Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Folyamatos integráció és készregyártás (CI/CD) folyamatot biztosít az alkalmazások fejlesztéséhez. A folyamat egy Git-tárház kódját, egy kibocsátáskezelési rendszert, csomagok és más build-összetevőket és egy rendszert üzembe helyezéséhez a változások fejlesztési, tesztelési és éles környezetek kezelése kezdődik.

## <a name="prerequisites"></a>Előfeltételek

Itt látható a Contoso kell futtatni az ebben a forgatókönyvben:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | A Contoso korábban létrehozott előfizetések Ez a cikk a sorozat. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso korábban már beállított egy Azure-infrastruktúra.
**Fejlesztői Előfeltételek** | Contoso cégnek szüksége van a következő eszközök egy fejlesztői munkaállomáson:<br/><br/> - [A Visual Studio 2017 Community Edition: Verzió 15.5](https://www.visualstudio.com/)<br/><br/> Számítási feladatok, legyen az .NET engedélyezve van.<br/><br/> - [a git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0-s vagy újabb](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [A docker CE (Windows 10 esetén) vagy a Docker VM (Windows Server) –](https://docs.docker.com/docker-for-windows/install/) be a Windows-tárolók használatára.



## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fut-e az áttelepítés:

> [!div class="checklist"]
> * **1. lépés: Az Azure-ban egy SQL Database-példány üzembe helyezése**: Contoso kiosztja az SQL-példány, az Azure-ban. Ez az adatbázis után a virtuális gép egy Azure-tárolóba való áttelepítése előtér-webkiszolgáló, az alkalmazás webes előtérrendszer tárolópéldányt fog mutatni.
> * **2. lépés: Hozzon létre egy Azure Container Registry (ACR)**: Contoso látja el egy vállalati container registryt docker-tárolórendszerképekhez.
> * **3. lépés: Kiépítés az Azure Service Fabric**: egy Service Fabric-fürtön helyezi üzembe.
> * **4. lépés: A service fabric-tanúsítványok kezelése**: Contoso állítja be a tanúsítványok a fürt az Azure DevOps-szolgáltatások eléréséhez.
> * **5. lépés: A DMA-adatbázis áttelepítése**:, az a Database Migration Assistant alkalmazás adatbázis áttelepítése.
> * **6. lépés: Állítsa be az Azure DevOps-szolgáltatásokkal**: Contoso állít be egy új projekt az Azure DevOps-szolgáltatásokkal, és a kód importálja a Git-tárház.
> * **7. lépés: Az alkalmazás átalakítása**: Contoso konvertálja az alkalmazás egy tárolót az Azure DevOps és az SDK-eszközök használatával.
> * **8. lépés: Állítsa be a build és kiadás**: Contoso állítja be a buildelési és kiadási folyamatok létrehozásához, és az alkalmazás közzététele az ACR-REL és a Service Fabric-fürt.
> * **9. lépés: Az alkalmazás kiterjesztése**: Miután az alkalmazás nyilvános, Contoso terjeszti ki, hogy az Azure képességek előnyeit, és azt az Azure-ban a folyamat addig.



## <a name="step-1-provision-an-azure-sql-database"></a>1. lépés: Az Azure SQL-adatbázis üzembe helyezése

Contoso rendszergazdák üzembe helyezése egy Azure SQL database.

1. Kiválasztásával hozzon létre egy **SQL Database** az Azure-ban. 

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Az adatbázis nevét, az adatbázis, a helyszíni virtuális gépen futó megfelelően kell adnia (**SmartHotel.Registration**). Ezek az adatbázis ContosoRG erőforráscsoporthoz tartozik, helyezze el. Ez az az éles erőforrásait az Azure-ban használt erőforráscsoport.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Ezek egy új SQL Server-példány beállítása (**sql-smarthotel-eus2**) az elsődleges régióba.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Állítsa a tarifacsomagot, kiszolgáló és az adatbázis igényeinek megfelelően. És az Azure Hybrid Benefittel pénzt takaríthat meg, mert már van egy SQL Server-licencét kiválasztásával.
5. Méretezéshez, használja a v-Core-alapú vásárlási, és állítsa be a munkaórákra az elvárt követelményeknek.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Ezután, hozzon létre az adatbázispéldányhoz.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. A példány létrehozása után, nyissa meg az adatbázist, és jegyezze fel a szükségük van a Database Migration Assistant használata a migrálás részletei.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**További segítségre van szüksége?**

- [Segítség kérése](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) SQL-adatbázis kiépítése.
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) magok erőforráskorlátok.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>2. lépés: Hozzon létre egy ACR-t, és üzembe helyezése egy Azure-tárolóba

Az Azure container jön létre a webkiszolgáló virtuális gépről az exportált fájlok használatával. A tároló az Azure Container Registry (ACR) részeként.


1. Contoso rendszergazdák tároló-beállításjegyzék létrehozása az Azure Portalon.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Akkor adja meg a beállításjegyzék nevét (**contosoacreus2**), és helyezze az elsődleges régióban, az infrastruktúra-erőforrások használata az erőforráscsoportban. Engedélyezze a hozzáférést a rendszergazda felhasználók számára, és prémium Termékváltozatokban állítania azt, hogy a georeplikáció használhatnak.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>3. lépés: Kiépítés az Azure Service Fabric

A SmartHotel360 tárolót az Azure Service Fabric Sluster fog futni. Contoso-rendszergazdák a Service Fabric-fürt a következőképpen hozhat létre:

1. Az Azure Marketplace-ről a Service Fabric erőforrás létrehozása

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. A **alapjai**, DS egyedi nevet a fürt és a helyszíni virtuális gép eléréséhez szükséges hitelesítő adatok biztosítanak. Azok az erőforrás helyezze el a termelési erőforráscsoportban (**ContosoRG**) az elsődleges régióban USA keleti RÉGIÓJA 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. A **csomóponttípus konfigurációja**, akkor adjon meg egy csomóponttípust, tartósság beállítások, Virtuálisgép-méretet és alkalmazás végpontok.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. A **kulcstartó létrehozása**, hozzon létre egy új kulcstartót, azok infrastruktúra erőforráscsoportban, a tanúsítvány üzemeltetheti.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. A **hozzáférési szabályzatok** lehetővé teszik a virtuális gépek üzembe helyezéséhez a key vault elérését.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Akkor adja meg a tanúsítvány nevét.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Az összefoglalás lapon másolja azokat a hivatkozást, amellyel a tanúsítvány letöltése. Ez a Service Fabric-fürthöz való kapcsolódáshoz van szükségük.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Ellenőrzés elvégzése után, a fürt üzembe helyezése.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. A Tanúsítványimportáló varázsló fejlesztői gépek a letöltött tanúsítvány importálja azokat. A tanúsítvány segítségével a fürtön.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Miután a fürt ki van építve, azok csatlakozni a Service Fabric-fürt Explorert.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Válassza ki a megfelelő tanúsítvány van szükségük.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. A Service Fabric Explorer terhelés és a Contoso rendszergazdája, kezelheti a fürt.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>4. lépés: A Service Fabric-tanúsítványok kezelése

Contoso fürttanúsítványok a fürt az Azure DevOps-szolgáltatások hozzáférésének engedélyezéséhez szükséges. Contoso-rendszergazdák beállítására.

1. Ezek az Azure portal megnyitásához, és keresse meg a KeyVault.
2. Nyissa meg a tanúsítványokat, és másolja a kiépítési folyamat során létrehozott tanúsítvány ujjlenyomatával.

    ![Másolja az ujjlenyomatot](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. Ezek másolja egy szövegfájlba későbbi felhasználás céljából.
4. Most adnak hozzá egy ügyféltanúsítvány, amely egy rendszergazdai ügyfél tanúsítványa a fürtön fog válni. Ez lehetővé teszi az alkalmazás központi telepítést, a kiadási folyamathoz a fürthöz való csatlakozáshoz az Azure DevOps-szolgáltatásokkal. Ezek Ehhez nyissa meg a portálon KeyVault és válassza ki **tanúsítványok** > **létrehozás/importálás**.

    ![Ügyféltanúsítvány létrehozása](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. Adja meg a tanúsítvány nevét, és adjon meg egy X.509 megkülönböztető nevet a **tulajdonos**.

     ![Tanúsítvány neve](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. Ha a tanúsítvány létrejött, letöltik helyileg PFX formátumban.

     ![Tanúsítvány letöltése](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. Most, lépjen vissza a KeyVault tanúsítványok listájában, és másolja az ujjlenyomatot, az imént létrejött tanúsítványt. Mentse a szövegfájlt.

     ![Ügyfél-tanúsítvány ujjlenyomata](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. Az Azure DevOps-szolgáltatásokkal üzembe helyezéshez meg kell határoznia annak a tanúsítvány Base64 értékét. Erre a helyi fejlesztői munkaállomáson PowerShell használatával. Ezek illessze be a kimenetet egy szövegfájlba későbbi használatra.

    ```
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Base64 érték](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Végül adnak hozzá az új tanúsítványt a Service Fabric-fürthöz. Ehhez nyissa meg a fürtöt, majd kattintson a portál **biztonsági**.

     ![Ügyféltanúsítvány hozzáadása](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. Kattintanak **Hozzáadás** > **rendszergazdai ügyfél**, és illessze be az új ügyfél tanúsítvány ujjlenyomatával. Ezután kattintanak **Hozzáadás**. Ez akár 15 percet is igénybe vehet.

     ![Ügyféltanúsítvány hozzáadása](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>5. lépés: A DMA-adatbázis áttelepítése

Contoso rendszergazdák most már migrálhatja a SmartHotel360 adatbázis DMA segítségével.

### <a name="install-dma"></a>Telepítse a DMA

1. Töltse le az eszközt a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) , a helyszíni SQL Server rendszerű virtuális gép (**SQLVM**).
2. A virtuális gépen futnak a telepítés (DownloadMigrationAssistant.msi).
3. Az a **Befejezés** lapon válasszon **indítsa el a Microsoft Data Migration Assistant** a varázsló befejezése előtt.

### <a name="configure-the-firewall"></a>A tűzfal konfigurálása

Szeretne csatlakozni az Azure SQL Database, a Contoso rendszergazdák való hozzáférés engedélyezése egy tűzfalszabály beállításához.

1. Az a **tűzfal és virtuális hálózatok** tulajdonságok az adatbázis Azure-szolgáltatásokhoz való hozzáférés engedélyezése és az ügyfél IP-címét a helyszíni SQL Server rendszerű virtuális gép egy szabály hozzáadásához.
2. Egy kiszolgálószintű tűzfalszabályt.

    ![Tűzfal](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

További segítségre van szüksége?

[Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) létrehozása és az Azure SQL Database-tűzfalszabályok kezelése.

### <a name="migrate"></a>Migrate (Áttelepítés)

Contoso rendszergazdák mostantól át az adatbázist.

1. Hozzon létre egy új projektet a DMA (**SmartHotelDB**), és válassza ki **áttelepítése** 
2. Akkor válassza ki a forráskiszolgáló típusa szerint **SQL Server**, és a cél, **Azure SQL Database**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Az áttelepítési adatokat hozzáadása **SQLVM** a forráskiszolgáló és a **SmartHotel.Registration** adatbázis. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Egy hiba, amely úgy tűnik, hogy a hitelesítéshez társított kapnak. Azonban Miután megvizsgálta, a probléma az adatbázis nevét a pont (.). Áthidaló megoldásként, úgy döntött, hogy a név használatával új SQL-adatbázis kiépítése **SmartHotel-regisztrációs**, a probléma megoldásához. DMA újra futnak, amikor azok kijelölheti a **SmartHotel-regisztrációs**, és a varázsló folytatásához.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. A **objektumok kijelölése**, válassza ki az adatbázistáblák, és hozzon létre egy SQL-parancsfájl.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. A DMS a parancsfájl által létrehozott, kattintanak **Deploy schema**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA megerősíti, hogy az üzembe helyezés sikeres volt.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Most már az áttelepítés indítása.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Az áttelepítés befejezését követően a Contoso ellenőrizheti, hogy az adatbázis működik-e az Azure SQL-példányon.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Ezek a további SQL-adatbázis törlése **SmartHotel.Registration** az Azure Portalon.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-azure-devops-services"></a>6. lépés: Állítsa be az Azure DevOps-szolgáltatásokkal

Contoso cégnek szüksége van, fejlesztési és üzemeltetési infrastruktúra és az alkalmazás folyamatokat hozhat létre.  Ehhez Contoso rendszergazdák hozzon létre egy új Azure DevOps-projektet, importálja a kódra összpontosítsanak, és ezután létrehozhatja és folyamatok felszabadítása.

1.   A Contoso Azure DevOps-fiókban lévő új projekt létrehozása (**ContosoSmartHotelRearchitect**), és válassza ki **Git** verziókezeléshez.
![Új projekt](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. A Git-adattár, amely jelenleg rendelkezik az alkalmazás kódját importálja azokat. Van egy [nyilvános adattár](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) és töltheti le.

    ![Alkalmazáskód letöltése](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. A kód az importálása után, a Visual Studio csatlakozni az adattárhoz, és klónozza a kód Team Explorer használatával.

4. A-adattárat a fejlesztői gépen való klónozták, miután az alkalmazás a megoldás fájl megnyitásakor. A web app és a wcf service rendelkező külön projekt a fájlon belül.

    ![Megoldásfájl](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>7. lépés: Az alkalmazás átalakítása egy tárolóba

A helyszíni alkalmazás, egy hagyományos háromszintű alkalmazás:

- Fejlesztő és a egy SQL-kiszolgálóhoz való kapcsolódás WCF-szolgáltatást tartalmazza.
- Entity Framework használatával az adatokat az SQL Database, a verzióinformációk a WCF szolgáltatással integrálható.
- A fejlesztő alkalmazás kommunikál a WCF-szolgáltatást.

Contoso rendszergazdák konvertálja az alkalmazás egy tároló használatával a Visual Studio és az SDK-eszközök, az alábbiak szerint:


1. A Visual Studiót használja, akkor tekintse meg a megnyitott megoldást fájlt (SmartHotel.Registration.sln) az a **SmartHotel360 – belső-foglalási-apps\src\Registration** könyvtárat a helyi tárház.  Két alkalmazások jelennek meg. A webes előtérrendszer SmartHotel.Registration.Web és a WCF szolgáltatás – alkalmazás SmartHotel.Registration.WCF.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. Kattintson a jobb gombbal a webalkalmazás > **Hozzáadás** > **Orchestrator Tárolótámogatás**.
3. A **hozzáadása Tárolótámogatás zenekar**, kiválasztják **Service Fabric**.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. Ezek ismételje meg a folyamatot SmartHotel.Registration.WCF alkalmazást.
5. Most akkor ellenőrizze, hogyan változott meg a megoldás.

    - Az új alkalmazás **SmartHotel.RegistrationApplication/**
    - Két szolgáltatást tartalmaz: **SmartHotel.Registration.WCF** és **SmartHotel.Registration.Web**.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. A Visual Studio a Docker-fájlban létrehozott, és helyileg lekért le a szükséges képeket, a fejlesztői gépen.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. A jegyzékfájlt (**ServiceManifest.xml**) létrejön és megnyílik a Visual Studióban. A fájl arra utasítja a Service Fabric a tároló konfigurálása, telepítése az Azure-bA.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. Egy másik jegyzékfájlt (** ApplicationManifest.xml) a tárolókhoz a konfigurációs alkalmazásokat tartalmaz.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. Megnyitja a **ApplicationParameters/Cloud.xml** fájlt, és frissítse a kapcsolati karakterláncot, az alkalmazás csatlakoztatása az Azure SQL Database-adatbázishoz. A kapcsolati karakterláncot az adatbázist az Azure Portalon található.

    ![Kapcsolati sztring](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. Ezek a frissített kód véglegesítése és leküldése az Azure DevOps-szolgáltatásokkal.

    ![Véglegesítés](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>8. lépés: Hozhat létre, és az Azure DevOps-szolgáltatásokkal folyamatok felszabadítása

Contoso-rendszergazdák mostantól konfigurálásához hajtsa végre a build és kiadás művelet folyamata a fejlesztési és üzemeltetési eljárások az Azure DevOps-szolgáltatásokkal.

1. Az Azure DevOps-szolgáltatásokkal, kattintson **készítése és kiadása** > **új adatcsatorna**.

    ![Új adatcsatorna](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. Kiválasztják **Azure fejlesztési és üzemeltetési szolgáltatás Git** és a megfelelő tárházban.

    ![A Git és a tárház](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. A **válasszon ki egy sablont**, fabric Docker-támogatás és kiválasztásával.

     ![Háló és a docker használatával](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. A művelet címke képek módosítását **állítson össze egy rendszerképet**, és konfigurálja a feladatot a kiépített ACR használata.

     ![Beállításjegyzék](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. Az a **rendszerképeket** feladat, akkor konfigurálja a rendszerképet kell leküldeni, az ACR-REL, és válassza ki a legújabb címke tartalmazza.
6. A **eseményindítók**, engedélyezze a folyamatos integrációt, és adja hozzá a master ágról.

    ![Eseményindítók](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. Kattintanak **mentéséhez és a várólistára** build elindításához.
8. Miután a build sikeres, a kiadási folyamathoz mehet azokat. Az Azure DevOps-szolgáltatásokkal kattintson **kiadásokban** > **új adatcsatorna**.

    ![Kibocsátási folyamatok](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. Akkor válassza ki a **Azure Service Fabric deployment** sablont, és a fázis neve (**SmartHotelSF**).

    ![Környezet](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Akkor adja meg a folyamat nevét (**ContosoSmartHotel360Rearchitect**). A szakaszban kattintson **1 feladat, 1. feladat** a Service Fabric központi telepítésnek a konfigurálásához.

    ![Fázis és tevékenység](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. Most kattintson **új** új fürtkapcsolat hozzáadásához.

    ![Új kapcsolat](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. A **kapcsolat hozzáadása a Service Fabric szolgáltatással**, konfigurálja a kapcsolatot, és az alkalmazás üzembe helyezése az Azure DevOps-szolgáltatások által használt hitelesítési beállításokat. Az Azure Portalon található a fürt azon végpontján, és megnövelik **tcp: / /** előtagjaként.
13. A tanúsítvány adatait gyűjti azok input **kiszolgálói tanúsítvány-ujjlenyomatát** és **ügyféltanúsítvány**.

    ![Tanúsítvány](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. A folyamat kattintanak > **egy összetevő hozzáadása**.

     ![Összetevő](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. Akkor válassza ki a projektet, és folyamatát, a legújabb verzió.

     ![Felépítés](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Vegye figyelembe, hogy az az összetevő a Villám jelölőnégyzet be van jelölve.

     ![Összetevő-állapot](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Ezenkívül vegye figyelembe, hogy engedélyezve van-e a folyamatos készregyártás eseményindítója.

   ![Engedélyezve van a folyamatos üzembe helyezés](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. Kattintanak **mentése** > **hozzon létre egy kiadási**.

    ![Kiadás](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. Az üzembe helyezés befejezése után fog SmartHotel360 most már futó Service Fabric.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. Szeretne csatlakozni az alkalmazást, azok közvetlen forgalom a Service Fabric csomópontjaival előtt az Azure load balancer nyilvános IP-címét.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>9. lépés: Az alkalmazás kiterjesztése, és tegye közzé újra

Ha a SmartHotel360 app és az adatbázis az Azure-ban futnak, a Contoso szeretné kiterjeszteni a az alkalmazás.

- Contoso-fejlesztők számára olyan prototípus-készítés új .NET Core-alkalmazást hozhat létre, amely a Service Fabric-fürtön fog futni.
- Az alkalmazás vélemények adatok lekérése a CosmosDB használható.
- Ezeket az adatokat, amelyek feldolgozása kiszolgáló nélküli Azure-függvény, és a Cognitive Services szövegelemzési API-JÁNAK használatával Tweetek formájában lesz.

### <a name="provision-azure-cosmos-db"></a>Az Azure Cosmos DB üzembe helyezése

Első lépésként a Contoso rendszergazdák üzembe egy Azure Cosmos database.

1. Akkor hozzon létre egy Azure Cosmos DB erőforrást, az Azure Marketplace-ről.

    ![Kiterjesztés](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Ezek adjon meg egy adatbázisnevet (**contososmarthotel**), válassza ki az SQL API-t, és helyezze el az erőforrás éles erőforráscsoportban az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Kiterjesztés](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. A **bevezetés**, kiválasztják **adatkezelő**, és adjon hozzá egy új gyűjteményt.
4. A **gyűjtemény hozzáadása** azonosítóit adja meg, és a tárolási kapacitás és az átviteli sebesség beállítása.

    ![Kiterjesztés](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. A portál megnyitja az új adatbázis > **gyűjtemény** > **dokumentumok** kattintson **új dokumentum**.
6. Ezek illessze be a következő JSON-kódot a dokumentum ablakban. Ez a mintaadatok egyetlen tweet formájában.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Kiterjesztés](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. A Cosmos DB-végpontként, valamint a hitelesítési kulcs találják. Ezek segítségével az alkalmazás csatlakozzon a gyűjteményhez. Az adatbázis kattintson **kulcsok**, és másolja az URI és primary key a Jegyzettömbbe.

    ![Kiterjesztés](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>A róluk szóló véleményeket alkalmazás frissítése

Az a Cosmos DB üzembe helyezett a Contoso-rendszergazdák konfigurálhatják az alkalmazásnak, hogy csatlakozzon hozzá.

1. A Visual Studióban a Solution Explorer ApplicationModern\ApplicationParameters\cloud.xml fájl megnyitásakor.

    ![Hangulatelemző alkalmazás](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Akkor adja meg a következő két paramétert:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Hangulatelemző alkalmazás](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Tegye közzé újra az alkalmazást

Az alkalmazás kibővítése, után Contoso rendszergazdák újból közzé kell tennie az Azure-ban a folyamatot.

1. Véglegesítik és kódját az Azure DevOps-szolgáltatásokkal. Ez elindít a buildelési és kiadási folyamatok.

2. Miután a buildelési és üzembe helyezés végeztével SmartHotel360 fog most már futnia Service Fabric. A hogyan listázhatja Fabric felügyeleti konzol most már három szolgáltatások láthatók.

    ![Tegye közzé újra](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. Most már kattintva törölheti az, hogy a SentimentIntegration alkalmazás működik és szolgáltatásokon keresztül

    ![Tegye közzé újra](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Áttelepítés után a Contoso cégnek szüksége van, karbantartása a lépések elvégzéséhez:  

- Távolítsa el a helyszíni virtuális gépek a vCenter-készlet.
- Távolítsa el a virtuális gépek helyi biztonsági mentési feladatok.
- Frissítse a belső dokumentációjában az új helyeket a SmartHotel360 alkalmazás megjelenítéséhez. Az Azure SQL database és az as a Service Fabricben az előtér-ban futó adatbázis-megjelenítése.
- Tekintse át az erőforrásokat, amelyek interakciót folytatni a leszerelt virtuális gépeket, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.


## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és tegye biztonságossá a új infrastruktúrára.

### <a name="security"></a>Biztonság

- Contoso-rendszergazdáknak kell ahhoz, hogy az új **SmartHotel-regisztrációs** adatbázis biztonságos. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Különösen azokat a tároló az SSL-tanúsítványokkal használatához frissítenie kell.
- Akkor érdemes megfontolni KeyVault titkos kulcsok a Service Fabric-alkalmazások védelme érdekében. [További információk](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Biztonsági másolatok

- Contoso cégnek szüksége van, tekintse át az Azure SQL Database biztonsági mentés követelményeinek. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso rendszergazdák kell fontolóra feladatátvételi csoportok regionális feladatátvételt biztosít az adatbázishoz. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Az ACR prémium Termékváltozatokban georeplikációt használhatnak. [További információk](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso figyelembe kell vennie a központi telepítése a Web App, a fő USA keleti RÉGIÓJA 2 és az USA középső régiójában, amikor elérhetővé válik a Web App for containers szolgáltatásban. Contoso rendszergazdák sikerült konfigurálni a Traffic Manager feladatátvétel esetén a regionális üzemkimaradások utáni helyreállításon biztosítása érdekében.
- A cosmos DB automatikusan menti. Contoso [olvashat](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) további a folyamat.

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Erőforrások üzembe helyezését követően Contoso rendelje hozzá a az Azure címkék alapján [infrastruktúrák tervezése](contoso-migration-infrastructure.md#set-up-tagging).
- Az összes licencelése a költség, a Contoso fogyaszt PaaS-szolgáltatások be van építve. Ez a program levonja a nagyvállalati szerződés.
- Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.

## <a name="conclusion"></a>Összegzés

Ez a cikk a Contoso refactored az alkalmazás előtérbeli virtuális gép áttelepítése a Service Fabric révén a SmartHotel360 alkalmazást az Azure-ban. Az alkalmazás-adatbázis lett migrálva az Azure SQL-adatbázis.





