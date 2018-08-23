---
title: Egy Contoso-alkalmazást egy Azure container és az Azure SQL Database újratervezése |} A Microsoft Docs
description: Ismerje meg, hogy a Contoso Azure Windows-tárolók és az Azure SQL Database az alkalmazás újratervezése.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 733a93d0fc80d86d28f13a9e1d32108b58893bf0
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42059465"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso áttelepítési: egy Azure container és az Azure SQL Database a helyszíni alkalmazás újratervezése

Ez a cikk bemutatja, hogyan Contoso áttelepíti, és az Azure-ban SmartHotel alkalmazás újratervezése. Az Azure Windows-tárolókat, és az alkalmazás-adatbázis egy Azure SQL Database-adatbázishoz az alkalmazás előtérbeli virtuális gép át azokat.

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a saját helyszíni erőforrásokat a Microsoft Azure felhőbe. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak, és idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso alkalmazás rendelkező virtuális gépek értékeli a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Egy alkalmazás Újratárolása az Azure virtuális gépek és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut egy lift-and-shift-migrálás az Azure-bA az SmartHotel alkalmazás. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányra, használja a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
[5. cikk: Egy alkalmazás Újratárolása az Azure virtuális gépek](contoso-migration-rehost-vm.md) | Bemutatja, hogyan a Contoso át a SmartHotel app virtuális gépek csak a Site Recovery használatával. | Elérhető
[A cikk 6: Egy alkalmazás Újratárolása az Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoport](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. Contoso számára, hogy az alkalmazás virtuális gépeit és a Database Migration service, az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használja. | Elérhető
[7. cikk: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso hajtja végre a Linux osTicket alkalmazás lift-and-shift áttelepítés Azure virtuális gépekre, a Site Recovery | Elérhető
[A cikk 8: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást egy Azure Web App és az Azure SQL Database-adatbázishoz](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a TFS-t a vsts-ben](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a saját helyi Team Foundation Server (TFS) központi, migrálás, a Visual Studio Team Services (VSTS) az Azure-ban.
A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Ez a cikk
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető

Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel alkalmazás VMware virtuális gépeken futó Azure-bA. Ha szeretné használni ezt az alkalmazást, nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik az üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és ennek eredményeképpen nincs a helyszíni rendszerek és infrastruktúra nyomás.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára.  Az üzleti igények informatikai gyorsan, és nem Hulladékmennyiség idő vagy költséget takaríthat meg, így gyorsabban továbbítása az ügyfelek igényei.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a versenyképes sikeres engedélyezése a módosításokat, képesnek kell lennie.  Azt nem a módon, vagy egy üzleti blocker válnak.
- **Méretezési csoport**: a vállalat növekedésével sikeres, a Contoso informatikai kell megadnia rendszerek, amelyek képesek a ugyanolyan ütemben nő.
- **Költségek**: a Contoso biztosítani szeretné a licencelési költségek csökkentése érdekében.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok a leginkább megfelelő áttelepítési módszer meghatározásához használt.

**Célok** | **Részletek**
--- | --- 
**Alkalmazás szükséges előfeltételeknek** | Az alkalmazás az Azure-ban marad, kritikus fontosságú, mivel még ma.<br/><br/> Teljesítmény ugyanazokat a lehetőségeket azonban jelenleg nem VMWare kell rendelkeznie.<br/><br/> A Windows Server 2008 R2, amelyen az alkalmazás jelenleg fut, és arra, hogy az alkalmazás be környezetei már nem szeretnének.<br/><br/> Szeretné egy modern PaaS-adatbázis platformon, ami minimalizálja a felügyeleti kell esniük az SQL Server 2008 R2.<br/><br/> Contoso szeretné kihasználni a befektetés az SQL Server-licenc és frissítési garanciával rendelkező, ahol csak lehetséges.<br/><br/> Szeretné tudni vertikális felskálázása az alkalmazás webes réteg.
**Korlátozások** | Az alkalmazás egy ASP.NET-alkalmazás és a egy ugyanazon a virtuális Gépen futó WCF szolgáltatás áll. Szeretné ez elosztja a két webalkalmazást az Azure App Service használatával. 
**Szükséges Azure-előfeltételeknek** | Szeretné áthelyezni az alkalmazás az Azure-ba, és futtassa azt az alkalmazás élettartamának bővítése egy tárolót. Nem szeretnének kezd teljesen az alkalmazás megvalósítása az Azure-ban. 

## <a name="solution-design"></a>Megoldásterv

Után rögzíthet a célokat és követelményeket állapította meg, a Contoso tervez és a egy üzembehelyezési megoldással tekintse át, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, amelyek az áttelepítés használatának mikéntjét.

### <a name="current-app"></a>Aktuális alkalmazás

- A SmartHotel helyszíni alkalmazás többszintű (WEBVM és SQLVM) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


### <a name="proposed-architecture"></a>Javasolt architektúra

- Az alkalmazás az adatbázisszinten, a Contoso Azure SQL Database képest az SQL Server használatával [Ez a cikk](https://docs.microsoft.com/azure/sql-database/sql-database-features). Azok a úgy döntött, hogy nyissa meg az Azure SQL Database néhány okok miatt:
    - Az Azure SQL Database felügyelt relációs adatbázis-szolgáltatás. Az adminisztrációt több szolgáltatási szinten kiszámítható teljesítményt kínál. Előny az állásidő, beépített intelligens optimalizálással, és globális méretezhetőség és rendelkezésre állás a dinamikus méretezhetőség.
    - A Data Migration Assistant (DMA) segítségével mérheti fel, és a helyszíni adatbázis migrálása az Azure SQL egyszerűsített használhatnak.
    - A kedvezményes díjszabás a egy SQL Database, az Azure Hybrid Benefit használatával az SQL Server meglévő licenceit frissítési garanciával működő, is váltanak. Ez akár 30 %-os megtakarítás nyújtani.
    - Az SQL Database számos olyan biztonsági funkciók, például mindig titkosított, dinamikus adatmaszkolást és a sorszintű biztonság/fenyegetések észlelése.
- Az alkalmazás webes réteg már döntöttek alakíthatja át a Windows-tárolót, a Visual Studio használatával.
    - Ezeket fogja az Azure Service Fabric-alkalmazás üzembe helyezése, és kérje le a Windows-tároló rendszerképét az Azure Container Registry (ACR).
    - Az alkalmazás hangulatelemzés kiterjesztése prototípusát egy másik szolgáltatás a Service Fabric segítségével a Cosmos DB-hez csatlakoztatott hajtja végre.  Ezzel kiolvasni az információt a Tweeteket, és az alkalmazás megjelenítéséhez.

    ![Forgatókönyv-architektúra](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Megoldás áttekintése
Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Szempontok** | **Részletek**
--- | ---
**Szakemberek számára** | Áttelepítés az Azure Service fabric módosítható a SmartHotel alkalmazáskód kell. Azonban részéről az erőfeszítés, minimális, a módosítások a Service Fabric SDK-eszközök használatával.<br/><br/> A Service Fabric a Váltás elindíthatják az alkalmazás eredeti kódbázis veszélyeztetése nélkül az idő múlásával gyorsan hozzá mikroszolgáltatásokat fejleszthet.<br/><br/> Windows-tárolók ugyanazokat az előnyöket tárolókként általában kínálnak. Javítják a rugalmasságot, a hordozhatóságot és a vezérlés.<br/><br/> A frissítési garanciával rendelkező SQL Server és a Windows Server az Azure Hybrid Benefit használatával beruházási használhatnak.<br/><br/> Az áttelepítés után azok többé nem kell támogatja a Windows Server 2008 R2. [További információk](https://support.microsoft.com/lifecycle).<br/><br/> Konfigurálhatják a webes szint, az alkalmazás több példányával, így már nem olyan hibaérzékeny pont.<br/><br/> Ezek már nem fogja az SQL Server 2008 R2 elévülési függ.<br/><br/> Az SQL Database támogatja a Contoso műszaki követelményeknek. Azok a helyszíni adatbázis, a Database Migration Assistant eszközzel értékelni, és kompatibilis található.<br/><br/> SQL-adatbázis, amely a Contoso beállítása nem kell beépített hibatűrő képességgel rendelkezik. Ez biztosítja, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | Tárolók összetettebbek, mint más áttelepítési lehetőségek. A tárolók elsajátítható contoso problémát okozhatja.  Egy új szint által biztosított nagy mennyiségű értéket a görbe érték összetettségi vezetnek.<br/><br/> Az üzemeltetési csapat, contoso megértéséhez, és az alkalmazás támogatja az Azure, tárolók és mikroszolgáltatások felpörög kell.<br/><br/> Ha használnak a Data Migration Assistant Data Migration Service helyett a saját adatbázis áttelepítése, a Contoso nem kell az infrastruktúra készen áll a migrálás adatbázisok ipari méretekben.



### <a name="migration-process"></a>Áttelepítési folyamat

1. Contoso az Azure service fabric-fürt kiépítése a Windows.
2. Az Azure SQL-példány üzembe helyezése, és a SmartHotel adatbázis át.
3. A Service Fabric SDK-eszközökkel egy Docker-tárolót, a webes szint virtuális gép átalakítása.
4. A service fabric-fürt és az ACR-REL, és az Azure service fabric-alkalmazás üzembe helyezése.

    ![Áttelepítési folyamat](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ezek segítségével DMA értékeli és észleli a kompatibilitási problémákat, amelyek hatással lehetnek a funkció az Azure-ban. A DMA értékeli a funkcióparitás SQL források és célok között, és a teljesítmény- és megbízhatóságbeli fejlesztéseket javasol. | Ez egy ingyenesen letölthető eszköz.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Egy intelligens, teljes körűen felügyelt felhőalapú relációsadatbázis-szolgáltatás. | Költség funkciók, az átviteli sebesség és a mérete alapján. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Az Azure Container Registrybe](https://azure.microsoft.com/services/container-registry/) | Store képek a tárolópéldányok összes típusára vonatkozóan. | A Funkciók, a storage és a használati időtartama alapján költsége. [További információk](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Folyamatosan elérhető, skálázható és elosztott alkalmazások készítése és üzemeltetése | Költség méretét, helyét és a számítási csomópontok időtartama alapján. [További információk](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Előfeltételek

Itt látható, milyen meg (és Contoso) kell futtatni az ebben a forgatókönyvben:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetést az értékelés az oktatóanyag-sorozatban az első cikkben végrehajtását. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.
**Fejlesztői Előfeltételek** | Contoso cégnek szüksége van a következő eszközök egy fejlesztői munkaállomáson:<br/><br/> - [A Visual Studio 2017 Community Edition: Verzió 15.5](https://www.visualstudio.com/)<br/><br/> Számítási feladatok, legyen az .NET engedélyezve van.<br/><br/> - [a git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0-s vagy újabb](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [A docker CE (Windows 10 esetén) vagy a Docker VM (Windows Server) –](https://docs.docker.com/docker-for-windows/install/) be a Windows-tárolók használatára.



## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Az Azure-ban egy SQL Database-példány üzembe helyezése**: Contoso kiosztja az SQL-példány, az Azure-ban. Ez az adatbázis után a virtuális gép egy Azure-tárolóba való áttelepítése előtér-webkiszolgáló, az alkalmazás webes előtérrendszer tárolópéldányt fog mutatni.
> * **2. lépés: Kiépítés az Azure Service Fabric**: azok a Service Fabric-fürt üzembe helyezése.
> * **4. lépés: A DMA-adatbázis áttelepítése**: azok a Database Migration Assistant alkalmazás adatbázis migrálása.
> * **5. lépés: Az alkalmazás átalakítása egy tároló**: Váltás az alkalmazás egy tárolóba, a Visual Studio és az SDK-eszközök használatával.
> * **6. lépés: Az alkalmazás közzététele**: tesznek közzé az alkalmazást, hogy az ACR-REL és a Service Fabric-fürt.
> * **7. lépés: Az alkalmazás kiterjesztése**: Miután az alkalmazás nyilvános, azok bővítheti Azure képességek előnyeit, és újra közzéteheti az Azure-bA.



## <a name="step-1-provision-an-azure-sql-database"></a>1. lépés: Az Azure SQL-adatbázis üzembe helyezése

1. Az SQL-adatbázis létrehozása az Azure-ban válassza ki. 

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Az adatbázis nevét, az adatbázis, a helyszíni virtuális gépen futó megfelelően kell adnia (**SmartHotel.Registration**). Ezek az adatbázis ContosoRG erőforráscsoporthoz tartozik, helyezze el. Ez az az éles erőforrásait az Azure-ban használt erőforráscsoport.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Ezek egy új SQL Server-példány beállítása (**sql-smarthotel-eus2**) az elsődleges régióba.

    ![SQL kiépítése](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Állítsa a tarifacsomagot felel meg a kiszolgáló és -adatbázist kell. És az Azure Hybrid Benefittel pénzt takaríthat meg, mert már van egy SQL Server-licencét kiválasztásával.
5. Méretezéshez, használja a v-Core-alapú vásárlási, és állítsa be a munkaórákra azok várt követelményeinek.

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


1. Contoso egy tároló-beállításjegyzéket az Azure Portalon hoz létre.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Akkor adja meg a beállításjegyzék nevét (**contosoacreus2**), és helyezze az elsődleges régióban, az infrastruktúra-erőforrások használata az erőforráscsoportban. Engedélyezze a hozzáférést a rendszergazda felhasználók számára, és prémium Termékváltozatokban állítania azt, hogy a georeplikáció használhatnak.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>3. lépés: Kiépítés az Azure Service Fabric

Az Azure Service Fabric Sluster a SmartHotel tárolót fog futni. Contoso hoz létre a Service Fabric-fürt a következő:

1. Az Azure Marketplace-ről a Service Fabric erőforrás létrehozása

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. A **alapszintű**, DS egyedi nevet a fürt és a helyszíni virtuális gép eléréséhez szükséges hitelesítő adatok biztosítanak. Azok az erőforrás helyezze el a termelési erőforráscsoportban (**ContosoRG**) az elsődleges régióban USA keleti RÉGIÓJA 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. A **csomóponttípus konfigurációja**, akkor adjon meg egy csomóponttípust, tartósság beállítások, Virtuálisgép-méretet és alkalmazás végpontok.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. A **kulcstartó létrehozása**, hozzon létre egy új kulcstartót, azok infrastruktúra erőforráscsoportban, a tanúsítvány üzemeltetheti.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. A **hozzáférési szabályzatok** , virtuális gépek üzembe helyezéséhez a key vault eanble hozzáférését.

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


## <a name="step-3-migrate-the-database-with-dma"></a>3. lépés: A DMA-adatbázis áttelepítése

Contoso telepítse át a DMA segítségével SmartHotel adatbázis.

### <a name="install-dma"></a>Telepítse a DMA

1. Töltse le az eszközt a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) , a helyszíni SQL Server rendszerű virtuális gép (**SQLVM**).
2. A virtuális gépen futnak a telepítés (DownloadMigrationAssistant.msi).
3. Az a **Befejezés** lapon válasszon **indítsa el a Microsoft Data Migration Assistant** a varázsló befejezése előtt.

### <a name="configure-the-firewall"></a>A tűzfal konfigurálása

Csatlakozás az Azure SQL Database, egy tűzfalszabályt van szükség.

1. Az a **tűzfal és virtuális hálózatok** tulajdonságok az adatbázis Azure-szolgáltatásokhoz való hozzáférés engedélyezése és az ügyfél IP-címét a helyszíni SQL Server rendszerű virtuális gép egy szabály hozzáadásához.
2. Egy kiszolgálószintű tűzfalszabályt.

    ![Tűzfal](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

További segítségre van szüksége?

[Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) létrehozása és az Azure SQL Database-tűzfalszabályok kezelése.

### <a name="migrate"></a>Migrate (Áttelepítés)

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



## <a name="step-4-convert-the-app-to-a-container"></a>4. lépés: Az alkalmazás átalakítása egy tárolóba

A helyszíni alkalmazás, egy hagyományos háromszintű alkalmazás:

- Fejlesztő és a egy SQL-kiszolgálóhoz való kapcsolódás WCF-szolgáltatást tartalmazza.
- Entity Framework használatával az adatokat az SQL Database, a verzióinformációk a WCF szolgáltatással integrálható.
- A fejlesztő alkalmazás kommunikál a WCF-szolgáltatást.

Contoso konvertálja az alkalmazás egy tároló használatával a Visual Studio és az SDK-eszközök, az alábbiak szerint:

1. Ezek klónozza az adattárat helyileg egy fejlesztői gépére:

    **git-klón https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. A Visual Studiót használja, akkor nyissa meg a megoldásfájlt (SmartHotel.Registration.sln) az a **SmartHotel360 – belső-foglalási-apps\src\Registration** könyvtárat a helyi tárház.  Két alkalmazások jelennek meg. A webes előtérrendszer SmartHotel.Registration.Web nad a WCF-alkalmazás SmartHotel.Registration.WCF szolgáltatás.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Kattintson a jobb gombbal a webalkalmazás > **Hozzáadás** > **Orchestrator Tárolótámogatás**.
4. A **hozzáadása Tárolótámogatás zenekar**, kiválasztják **Service Fabric**.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso alkalmazás SmartHotel.Registration.WCF megismétli a folyamatot.
6. Most a Contoso ellenőrzi a hogyan változott meg a megoldást.

    - Az új alkalmazás **SmartHotel.RegistrationApplication/**
    - Két szolgáltatást tartalmaz: **SmartHotel.Registration.WCF** és **SmartHotel.Registration.Web**.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. A Visual Studio a Docker-fájlban létrehozott, és helyileg lekért le a szükséges képeket, a fejlesztői gépen.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. A jegyzékfájlt (**ServiceManifest.xml**) létrejön és megnyílik a Visual Studióban. A fájl arra utasítja a Service Fabric a tároló konfigurálása, telepítése az Azure-bA.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Egy másik jegyzékfájlt (** ApplicationManifest.xml) a tárolókhoz a konfigurációs alkalmazásokat tartalmaz.

    ![Tároló](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>5. lépés: Az alkalmazás közzététele


Végül Contoso tehetnek közzé az alkalmazást az ACR-REL és a Service Fabric-fürt.

> [!NOTE]
> A Service Fabric-fürtöt néhány módosítás az SmartHotel alkalmazásban történtek. Letöltheti az eredeti és a modernebb alkalmazás kódban [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). A módosított fájl **AppliationModern/ApplicationParameters/Cloud.xml**.


1. A Visual Studióban, a frissítést az alkalmazás csatlakoztatása az Azure SQL Database, a kapcsolati karakterláncot. A kapcsolati karakterláncot az Azure Portalon az adatbázisban található.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso Visual Studio használatával a Service Fabric közzéteszi az alkalmazást. A Service Fabric-alkalmazás a jobb gombbal kattintson > **közzététel**.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Akkor válassza ki az előfizetés, a kapcsolati végpont és az ACR. Kattintson a **közzététel**.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. Az üzembe helyezés befejezése után fog SmartHotel most már futó Service Fabric.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Szeretne csatlakozni az alkalmazást, Contoso továbbítja a forgalmat az Azure load Balancer nyilvános IP-címet, a Service Fabric-csomópontokon Előrehozás.

    ![Közzététel](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>6. lépés: Az alkalmazás kiterjesztése, és tegye közzé újra

Ha a SmartHotel app és az adatbázis az Azure-ban futnak, a Contoso szeretné kiterjeszteni a az alkalmazás.

- Contoso-fejlesztők számára olyan prototípus-készítés új .NET Core-alkalmazást hozhat létre, amely a Service Fabric-fürtön fog futni.
- Az alkalmazás vélemények adatok lekérése a CosmosDB használható.
- Ezeket az adatokat, amelyek feldolgozása kiszolgáló nélküli Azure-függvény, és a Cognitive Services szövegelemzési API-JÁNAK használatával Tweetek formájában lesz.

### <a name="provision-azure-cosmos-db"></a>Az Azure Cosmos DB üzembe helyezése

Első lépésként a Contoso egy Azure Cosmos-adatbázis kiépítése.

1. Akkor hozzon létre egy Azure Cosmos DB erőforrást, az Azure Marketplace-ről.

    ![Bővíthető](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Ezek adjon meg egy adatbázisnevet (**contososmarthotel**), válassza ki az SQL API-t, és helyezze el az erőforrás éles erőforráscsoportban az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Bővíthető](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. A **bevezetés**, kiválasztják **adatkezelő**, és adjon hozzá egy új gyűjteményt.
4. A **gyűjtemény hozzáadása** azonosítóit adja meg, és a tárolási kapacitás és az átviteli sebesség beállítása.

    ![Bővíthető](./media/contoso-migration-rearchitect-container-sql/extend3.png)

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
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Bővíthető](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. A Cosmos DB-végpontként, valamint a hitelesítési kulcs találják. Ezek segítségével az alkalmazás csatlakozzon a gyűjteményhez. Az adatbázis kattintson **kulcsok**, és másolja az URI és primary key a Jegyzettömbbe.

    ![Bővíthető](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>A róluk szóló véleményeket alkalmazás frissítése

Az üzembe helyezett a Cosmos DB a Contoso konfigurálhatja az alkalmazásnak, hogy csatlakozzon hozzá.

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

Az alkalmazás kibővítése, után Contoso addig azt az Azure-bA.

1. A portálon, akkor kattintson a jobb gombbal a Service Fabric-alkalmazás > **közzététel**.

    ![Tegye közzé újra](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Akkor válassza ki az előfizetés, a kapcsolati végpont és az ACR. Kattintson a **közzététel**.

    ![Tegye közzé újra](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. Az üzembe helyezés befejezése után fog SmartHotel most már futó Service Fabric. A hogyan listázhatja Fabric felügyeleti konzol most már három szolgáltatások láthatók.

    ![Tegye közzé újra](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso végigkattinthat a látja, hogy a SentimentIntegration alkalmazás és -szolgáltatások

    ![Tegye közzé újra](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Áttelepítés után a Contoso cégnek szüksége van, karbantartása a lépések elvégzéséhez:  

- Távolítsa el a helyszíni virtuális gépek a vCenter-készlet.
- Távolítsa el a virtuális gépek helyi biztonsági mentési feladatok.
- Frissítse a belső dokumentációt, az új hely SmartHotel alkalmazás megjelenítése. Az Azure SQL database és az as a Service Fabricben az előtér-ban futó adatbázis-megjelenítése.
- Tekintse át az erőforrásokat, amelyek interakciót folytatni a leszerelt virtuális gépeket, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.


## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és tegye biztonságossá a új infrastruktúrára.

### <a name="security"></a>Biztonság

- Contoso kell ahhoz, hogy az új **SmartHotel-regisztrációs** adatbázis biztonságos. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Különösen azokat a tároló az SSL-tanúsítványokkal használatához frissítenie kell.
- Akkor érdemes megfontolni KeyVault titkos kulcsok a Service Fabric-alkalmazások védelme érdekében. [További információk](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Biztonsági másolatok

- Contoso cégnek szüksége van, tekintse át az Azure SQL Database biztonsági mentés követelményeinek. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Akkor érdemes megfontolni, regionális feladatátvételt biztosít az adatbázis feladatátvételi csoportok megvalósítását. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Az ACR prémium Termékváltozatokban georeplikációt használhatnak. [További információk](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso figyelembe kell vennie a központi telepítése a Web App, a fő USA keleti RÉGIÓJA 2 és az USA középső régiójában, amikor elérhetővé válik a Web App for containers szolgáltatásban. Feladatátvétel esetén a regionális üzemkimaradások utáni helyreállításon biztosítása érdekében a Traffic Manager beállíthat azokat.

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Erőforrások üzembe helyezését követően Contoso rendelje hozzá a az Azure címkék alapján saját [infrastruktúrák tervezése](contoso-migration-infrastructure.md#set-up-tagging).
- Az összes licencelése a költség, a Contoso fogyaszt PaaS-szolgáltatások be van építve. Ez a program levonja a nagyvállalati szerződés.
1. Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe. 

## <a name="conclusion"></a>Összegzés

Ebben a cikkben a Contoso refactored az alkalmazás előtérbeli virtuális gép áttelepítése a Service Fabric révén a SmartHotel alkalmazást az Azure-ban. Ezek át az alkalmazás-adatbázis egy Azure SQL Database-adatbázishoz.





