---
title: Egy Contoso alkalmazás újrabontása migrálással, az Azure virtuális gépek és az SQL Server AlwaysOn rendelkezésre állási csoporthoz |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso egy helyszíni alkalmazás újratárolása, hogy egy Azure Container Web App és a egy Azure SQL Server-adatbázis áttelepítése révén.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005190"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso áttelepítési: Újrabontás a helyszíni alkalmazások Azure Web App és az Azure SQL Database-adatbázishoz

Ez a cikk bemutatja, hogyan a Contoso refactors SmartHotel alkalmazás az Azure-ban. Az Azure Web Apps és az alkalmazás-adatbázis egy Azure SQL Database-adatbázishoz az alkalmazás előtérbeli virtuális gép át azokat.

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
9. cikk: Újrabontás egy alkalmazást egy Azure Web App és az Azure SQL Database-adatbázishoz | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Ez a cikk
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a TFS-t a vsts-ben](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a saját helyi Team Foundation Server (TFS) központi, migrálás, a Visual Studio Team Services (VSTS) az Azure-ban. | Elérhető
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető


Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel alkalmazás VMware virtuális gépeken futó Azure-bA. Ha szeretné használni ezt az alkalmazást, nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik az üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és a helyszíni rendszerek és infrastruktúra nyomás.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára.  Az üzleti igények informatikai gyorsan, és nem Hulladékmennyiség idő vagy költséget takaríthat meg, így gyorsabban továbbítása az ügyfelek igényei.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a versenyképes sikeres engedélyezése a módosításokat, képesnek kell lennie.  Azt nem a módon, vagy egy üzleti blocker válnak.
- **Méretezési csoport**: a vállalat növekedésével sikeres, a Contoso informatikai kell megadnia rendszerek, amelyek képesek a ugyanolyan ütemben nő.
- **Költségek**: a Contoso biztosítani szeretné a licencelési költségek csökkentése érdekében.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok a leginkább megfelelő áttelepítési módszer meghatározásához használt.

**Követelmények** | **Részletek**
--- | --- 
**Alkalmazás** | Az alkalmazás az Azure-ban marad, kritikus fontosságú, mivel még ma.<br/><br/> Teljesítmény ugyanazokat a lehetőségeket azonban jelenleg nem VMWare kell rendelkeznie.<br/><br/> Nem szeretnének az alkalmazás be. Most egyszerűen szeretnék biztonságosan áthelyezése a felhőbe.<br/><br/> A Windows Server 2008 R2, amelyen az alkalmazás jelenleg fut környezetei már nem szeretnének.<br/><br/> Szeretné egy modern PaaS-adatbázis platformon, ami minimalizálja a felügyeleti kell esniük az SQL Server 2008 R2.<br/><br/> Contoso szeretné kihasználni a befektetés az SQL Server-licenc és frissítési garanciával rendelkező, ahol csak lehetséges.<br/><br/> Emellett Contoso szeretne csökkentése érdekében a webes szint a hibaérzékeny pont.
**Korlátozások** | Az alkalmazás egy ASP.NET-alkalmazás és a egy ugyanazon a virtuális Gépen futó WCF szolgáltatás áll. Szeretné ez elosztja a két webalkalmazást az Azure App Service használatával. 
**Azure** | Szeretne áthelyezni az alkalmazás az Azure-ba, de nem szeretnének a virtuális gépek futtatásához. Szeretné kihasználni az Azure PaaS-szolgáltatások a webes és az adatokat. 

## <a name="solution-design"></a>Megoldásterv

Után rögzíthet a célokat és követelményeket állapította meg, a Contoso tervez és a egy üzembehelyezési megoldással tekintse át, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, amelyek az áttelepítés használatának mikéntjét.

### <a name="current-app"></a>Aktuális alkalmazás

- A SmartHotel helyszíni alkalmazás többszintű (WEBVM és SQLVM) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


### <a name="proposed-solution"></a>Javasolt megoldás

- Az alkalmazás az adatbázisszinten, a Contoso Azure SQL Database képest az SQL Server használatával [Ez a cikk](https://docs.microsoft.com/azure/sql-database/sql-database-features). Azok a úgy döntött, hogy nyissa meg az Azure SQL Database néhány okok miatt:
    - Az Azure SQL Database felügyelt relációs adatbázis-szolgáltatás. Az adminisztrációt több szolgáltatási szinten kiszámítható teljesítményt kínál. Előny az állásidő, beépített intelligens optimalizálással, és globális méretezhetőség és rendelkezésre állás a dinamikus méretezhetőség.
    - A Data Migration Assistant (DMA) segítségével mérheti fel, és a helyszíni adatbázis migrálása az Azure SQL egyszerűsített használhatnak.
    - A kedvezményes díjszabás a egy SQL Database, az Azure Hybrid Benefit használatával az SQL Server meglévő licenceit frissítési garanciával működő, is váltanak. Ez akár 30 %-os megtakarítás nyújtani.
    - Az SQL Database számos olyan biztonsági funkciók, például mindig titkosított, dinamikus adatmaszkolást és a sorszintű biztonság/fenyegetések észlelése.
- Az alkalmazás webes réteg azokat korábban úgy döntött, hogy az Azure App Service használata. A PaaS-szolgáltatás lehetővé teszi, hogy telepítse az alkalmazást néhány konfigurációs módosítás. Ezeket fogja módosítsa a Visual Studio használatával, és a két webes alkalmazások üzembe helyezése. Egy a webhelyhez, és egy, a WCF-szolgáltatás.
  
### <a name="solution-review"></a>Megoldás áttekintése
Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Szempontok** | **Részletek**
--- | ---
**Szakemberek számára** | A SmartHotel alkalmazáskód nem kell módosítani az Azure-ba való migrálásra.<br/><br/> A frissítési garanciával rendelkező SQL Server és a Windows Server az Azure Hybrid Benefit használatával beruházási használhatnak.<br/><br/> Az áttelepítés után azok többé nem kell támogatja a Windows Server 2008 R2. [További információk](https://support.microsoft.com/lifecycle).<br/><br/> Konfigurálhatják a webes szint, az alkalmazás több példányával, így már nem olyan hibaérzékeny pont.<br/><br/> Ezek már nem fogja az SQL Server 2008 R2 elévülési függ.<br/><br/> Az SQL Database támogatja a Contoso műszaki követelményeknek. Azok a helyszíni adatbázis, a Database Migration Assistant eszközzel értékelni, és kompatibilis található.<br/><br/> SQL-adatbázis, amely a Contoso beállítása nem kell beépített hibatűrő képességgel rendelkezik. Ez biztosítja, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | Az Azure App Services csak egy alkalmazás központi telepítési támogatja minden webes alkalmazáshoz. Ez azt jelenti, hogy a két webalkalmazást üzembe helyezett (egyet a webhelyhez) és a WCF-szolgáltatást kell lennie.<br/><br/> Ha használnak a Data Migration Assistant Data Migration Service helyett a saját adatbázis áttelepítése, a Contoso nem kell az infrastruktúra készen áll a migrálás adatbázisok ipari méretekben. Meg kell, ha az elsődleges régióban nem érhető el, győződjön meg arról, hogy feladatátvételt egy másik régióban hozhat létre.

## <a name="proposed-architecture"></a>Javasolt architektúra

![Forgatókönyv-architektúra](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Áttelepítési folyamat

1. A Contoso Azure SQL-példány üzembe helyezése és a SmartHotel adatbázis át.
2. Azok kiépítése és webes alkalmazások konfigurálása, és azokat a SmartHotel alkalmazás üzembe helyezése.

    ![Áttelepítési folyamat](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ezek segítségével DMA értékeli és észleli a kompatibilitási problémákat, amelyek hatással lehetnek a funkció az Azure-ban. A DMA értékeli a funkcióparitás SQL források és célok között, és a teljesítmény- és megbízhatóságbeli fejlesztéseket javasol. | Ez egy ingyenesen letölthető eszköz.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Egy intelligens, teljes körűen felügyelt felhőalapú relációsadatbázis-szolgáltatás. | Költség funkciók, az átviteli sebesség és a mérete alapján. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Az Azure App Services - webalkalmazások](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Hozzon létre sokoldalú felhőalapú alkalmazásokat egy teljes körűen felügyelt platform használatával | Költség méretét, helyét és használat alapján. [További információk](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Előfeltételek

Itt látható, milyen meg (és Contoso) kell futtatni az ebben a forgatókönyvben:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetést az értékelés az oktatóanyag-sorozatban az első cikkben végrehajtását. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Az Azure-ban egy SQL Database-példány üzembe helyezése**: Contoso kiosztja az SQL-példány, az Azure-ban. Miután az alkalmazás webhelye migrálása az Azure-ba, a WCF-szolgáltatás webes alkalmazás ezt a példányt fog mutatni.
> * **2. lépés: A DMA-adatbázis áttelepítése**: azok a Database Migration Assistant alkalmazás adatbázis migrálása.
> * **3. lépés: Kiépítése Web Apps**: azok a két webes alkalmazásokat telepíteni.
> * **4. lépés: Kapcsolati karakterláncok konfigurálása**: kapcsolati karakterláncok konfigurálása, hogy a webes szint web app, a WCF service web app és az SQL-példány közötti kommunikációhoz.
> * **5. lépés: Webes alkalmazások közzététele**: utolsó lépésként Contoso tesz közzé az alkalmazásokat az Azure-bA.


## <a name="step-1-provision-an-azure-sql-database"></a>1. lépés: Az Azure SQL-adatbázis üzembe helyezése

1. Az SQL-adatbázis létrehozása az Azure-ban válassza ki. 

    ![SQL kiépítése](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Az adatbázis nevét, az adatbázis, a helyszíni virtuális gépen futó megfelelően kell adnia (**SmartHotel.Registration**). Ezek az adatbázis ContosoRG erőforráscsoporthoz tartozik, helyezze el. Ez az az éles erőforrásait az Azure-ban használt erőforráscsoport.

    ![SQL kiépítése](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Ezek egy új SQL Server-példány beállítása (**sql-smarthotel-eus2**) az elsődleges régióba.

    ![SQL kiépítése](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Állítsa a tarifacsomagot felel meg a kiszolgáló és -adatbázist kell. És az Azure Hybrid Benefittel pénzt takaríthat meg, mert már van egy SQL Server-licencét kiválasztásával.
5. Méretezéshez, használja a v-Core-alapú vásárlási, és állítsa be a munkaórákra azok várt követelményeinek.

    ![SQL kiépítése](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Ezután, hozzon létre az adatbázispéldányhoz.

    ![SQL kiépítése](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. A példány létrehozása után, nyissa meg az adatbázist, és jegyezze fel a szükségük van a Database Migration Assistant használata a migrálás részletei.

    ![SQL kiépítése](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**További segítségre van szüksége?**

- [Segítség kérése](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) SQL-adatbázis kiépítése.
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) magok erőforráskorlátok.


## <a name="step-2-migrate-the-database-with-dma"></a>2. lépés: A DMA-adatbázis áttelepítése

Contoso telepítse át a DMA segítségével SmartHotel adatbázis.

### <a name="install-dma"></a>Telepítse a DMA

1. Töltse le az eszközt a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) , a helyszíni SQL Server rendszerű virtuális gép (**SQLVM**).
2. A virtuális gépen futnak a telepítés (DownloadMigrationAssistant.msi).
3. Az a **Befejezés** lapon válasszon **indítsa el a Microsoft Data Migration Assistant** a varázsló befejezése előtt.

### <a name="migrate-the-database-with-dma"></a>A DMA-adatbázis migrálása

1. Hozzon létre egy új projektet a DMA (**SmartHotelDB**), és válassza ki **áttelepítése** 
2. Akkor válassza ki a forráskiszolgáló típusa szerint **SQL Server**, és a cél, **Azure SQL Database**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Az áttelepítési adatokat hozzáadása **SQLVM** a forráskiszolgáló és a **SmartHotel.Registration** adatbázis. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Egy hiba, amely úgy tűnik, hogy a hitelesítéshez társított kapnak. Azonban Miután megvizsgálta, a probléma az adatbázis nevét a pont (.). Áthidaló megoldásként, úgy döntött, hogy a név használatával új SQL-adatbázis kiépítése **SmartHotel-regisztrációs**, a probléma megoldásához. DMA újra futnak, amikor azok kijelölheti a **SmartHotel-regisztrációs**, és a varázsló folytatásához.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. A **objektumok kijelölése**, válassza ki az adatbázistáblák, és hozzon létre egy SQL-parancsfájl.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. A DMS a parancsfájl által létrehozott, kattintanak **Deploy schema**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA megerősíti, hogy az üzembe helyezés sikeres volt.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Most már az áttelepítés indítása.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Az áttelepítés befejezését követően a Contoso ellenőrizheti, hogy az adatbázis működik-e az Azure SQL-példányon.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Ezek a további SQL-adatbázis törlése **SmartHotel.Registration** az Azure Portalon.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>3. lépés: Kiépítése Web Apps alkalmazások

Adatbázis-kezelő áttelepítése, Contoso részleg most üzembe helyezése a két webes alkalmazásokat.

1. Kiválasztják **webalkalmazás** a portálon.

    ![Webalkalmazás](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Biztosítanak az alkalmazás nevét (**SHWEB-EUS2**), futtassa a Windows, és helyezze el, törölje az erőforrásokat csoportot **ContosoRG**. Akkor hozzon létre, és új app service-csomag.

    ![Webalkalmazás](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Után a webes alkalmazás üzembe, ismételje meg a folyamatot hozhat létre egy webalkalmazást, a WCF-szolgáltatás (**SHWCF-EUS2**)

    ![Webalkalmazás](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Miután végzett, az alkalmazásokat, ellenőrizze, hogy sikeres létrehozása címére megkeresését.

## <a name="step-4-configure-connection-strings"></a>4. lépés: Kapcsolati karakterláncok konfigurálása

Contoso cégnek szüksége van, győződjön meg arról, hogy a web apps és az adatbázis összes kommunikálhatnak. Ehhez konfigurálják a kapcsolati karakterláncokat a kódban, és a web apps.

1. A WCF-szolgáltatás az web app alkalmazásban (**SHWCF-EUS2**) > **beállítások** > **Alkalmazásbeállítások**, adnak hozzá egy új kapcsolati sztring  **DefaultConnection**.
2. A kapcsolati karakterláncot a lehívja a **SmartHotel-regisztrációs** adatbázis és a megfelelő hitelesítő adatokkal kell frissíteni.

    ![Kapcsolati sztring](media/contoso-migration-refactor-web-app-sql/string1.png)

3. A Visual Studiót használja, Contoso nyitja meg a megoldásfájlt a a **SmartHotel360 – belső-foglalási-alkalmazások** mappát. A **kapcsolati Sztringjei** szakasz a web.config fájlt a WCF-szolgáltatás SmartHotel.Registration.Wcf frissíteni kell a kapcsolati karakterlánccal.

     ![Kapcsolati sztring](media/contoso-migration-refactor-web-app-sql/string2.png)

4. A **ügyfél** a web.config fájlt a SmartHotel.Registration.Web szakaszában módosítani kell, hogy a WCF-szolgáltatás az új helyre mutasson. Ez az a WCF-webalkalmazás üzemeltetése a Szolgáltatásvégpont URL-CÍMÉT.

    ![Kapcsolati sztring](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>5. lépés: Webes alkalmazások közzététele

Utolsó lépésként Contoso közzéteszi a web apps az Azure-bA.

1. A Visual Studióban, kattintson a jobb gombbal a SmartHotel.REgistration.Wcf Projekt > **közzététel**.

    ![Közzététel](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. Közzététel indítása.

    ![Közzététel](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. Egy meglévő App Service, válassza ki, mivel azok már létrehozta a webalkalmazást.

    ![Közzététel](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. Miután kiválasztják a WCF-alkalmazás, a Visual Studio telepíti.

    ![Közzététel](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. Majd ismételje meg az eljárást a webalkalmazás - SmartHotel.Registration.Web közzététele.

    ![Közzététel](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


Ezen a ponton az alkalmazás sikeresen migrálva az Azure-bA.

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Áttelepítés után a Contoso cégnek szüksége van, karbantartása a lépések elvégzéséhez:  

- Távolítsa el a helyszíni virtuális gépek a vCenter-készlet.
- Távolítsa el a virtuális gépek helyi biztonsági mentési feladatok.
- Frissítse a belső dokumentációt, az új hely SmartHotel alkalmazás megjelenítése. Az adatbázis-futó Azure SQL database és az előtér, két webalkalmazást futtató megjelenítése.
- Tekintse át az erőforrásokat, amelyek interakciót folytatni a leszerelt virtuális gépeket, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.


## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és tegye biztonságossá a új infrastruktúrára.

### <a name="security"></a>Biztonság

- Contoso kell ahhoz, hogy az új **SmartHotel-regisztrációs** adatbázis biztonságos. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Különösen azokat a web apps, az SSL-tanúsítványokkal használatához frissítenie kell.

### <a name="backups"></a>Biztonsági másolatok

- Contoso cégnek szüksége van, tekintse át az Azure SQL Database biztonsági mentés követelményeinek. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Akkor érdemes megfontolni, regionális feladatátvételt biztosít az adatbázis feladatátvételi csoportok megvalósítását. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso figyelembe kell vennie a központi telepítése a webes alkalmazás a fő USA keleti RÉGIÓJA 2 és a rugalmasság az USA középső régiójában. Feladatátvétel esetén a regionális üzemkimaradások utáni helyreállításon biztosítása érdekében a Traffic Manager beállíthat azokat.

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Erőforrások üzembe helyezését követően Contoso rendelje hozzá a az Azure címkék alapján saját [infrastruktúrák tervezése](contoso-migration-infrastructure.md#set-up-tagging).
- Az összes licencelése a költség, a Contoso fogyaszt PaaS-szolgáltatások be van építve. Ez a program levonja a nagyvállalati szerződés.
1. Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe. 

## <a name="conclusion"></a>Összegzés

Ez a cikk a Contoso refactored az alkalmazás előtérbeli virtuális gép áttelepítése két Azure Web Apps révén a SmartHotel alkalmazást az Azure-ban. Ezek át az alkalmazás-adatbázis egy Azure SQL Database-adatbázishoz.






