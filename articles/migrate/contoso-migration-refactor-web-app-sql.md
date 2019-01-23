---
title: Refaktorovat a Contoso-alkalmazást, és az Azure Web App, Azure SQL Database áttelepítése révén |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso áthelyezi telepítse át egy helyszíni alkalmazást, az Azure Web App és az Azure SQL Server-adatbázishoz.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: dec58b2bb82eeab8b95c59e2775d21d58e18cac4
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450421"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso áttelepítése: Egy Azure Web App és az Azure SQL Database-adatbázishoz a helyszíni alkalmazás újrabontása

Ez a cikk bemutatja, hogyan a Contoso refactors a SmartHotel360 alkalmazás Azure-ban. Az Azure Web Apps és az alkalmazás-adatbázis egy Azure SQL Database-adatbázishoz az alkalmazás előtérbeli virtuális gép át azokat.

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a saját helyszíni erőforrásokat a Microsoft Azure felhőbe. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak. Idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso alkalmazás rendelkező virtuális gépek értékeli a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Egy alkalmazás újratárolása az Azure virtuális gépek és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut egy lift-and-shift-migrálás az Azure-bA az SmartHotel alkalmazás. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányra, használja a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
[5. cikk: Egy alkalmazás újratárolása az Azure virtuális gépek](contoso-migration-rehost-vm.md) | Bemutatja, hogyan a Contoso át a SmartHotel app virtuális gépek csak a Site Recovery használatával. | Elérhető
[6. cikk: Egy alkalmazás újratárolása az Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoport](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. Contoso számára, hogy az alkalmazás virtuális gépeit és a Database Migration service, az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használja. | Elérhető
[7. cikk: Egy Linux alkalmazás újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso hajtja végre a Linux osTicket alkalmazás lift-and-shift áttelepítés Azure virtuális gépekre, a Site Recovery | Elérhető
[8. cikk: Egy Linux alkalmazás újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
9. cikk: Alkalmazás refaktorálása Azure-webalkalmazáshoz és Azure SQL Database-hez | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Ez a cikk
[10. cikk: Az Azure Web Apps és az Azure MySQL egy Linux-alkalmazás újrabontása](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Refaktorovat a TFS-t az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a saját helyi Team Foundation Server (TFS) központi, migrálás, hogy az Azure-ban az Azure DevOps-szolgáltatásokkal. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető

Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel360 alkalmazás VMware virtuális gépeken futó Azure-bA. Ha szeretné használni ezt az alkalmazást, nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és a helyszíni rendszerek és infrastruktúra nyomást.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára.  Az üzleti igények informatikai gyorsan, és nem Hulladékmennyiség idő vagy költséget takaríthat meg, így gyorsabban továbbítása az ügyfelek igényei.
- **A gyorsaság növeléséhez**:  Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a versenyképes sikeres engedélyezése a módosításokat, képesnek kell lennie.  Azt nem a módon, vagy egy üzleti blocker válnak.
- **Méretezési csoport**: A vállalat növekedésével sikeres, a Contoso informatikai rendszerek, amelyek képesek a ugyanolyan ütemben nő kell megadnia.
- **Költségek**: A Contoso biztosítani szeretné a licencelési költségek csökkentése érdekében.

## <a name="migration-goals"></a>Migrálási célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok a leginkább megfelelő áttelepítési módszer meghatározásához használt.

**Követelmények** | **Részletek**
--- | --- 
**App** | Az alkalmazás az Azure-ban marad, kritikus fontosságú, mivel még ma.<br/><br/> Teljesítmény ugyanazokat a lehetőségeket azonban jelenleg nem VMWare kell rendelkeznie.<br/><br/> A csapat az alkalmazás be nem szeretné. Most a rendszergazdák egyszerűen áthelyezi az alkalmazás biztonságosan a felhőben.<br/><br/> A csapat szeretné a Windows Server 2008 R2, amelyen az alkalmazás jelenleg fut környezetei már nem.<br/><br/> A csapat emellett szeretné egy modern PaaS-adatbázis platformon, ami minimalizálja a felügyeleti kell esniük az SQL Server 2008 R2.<br/><br/> Contoso szeretné kihasználni a lévő SQL Server-licenc és frissítési garanciával rendelkező, ahol csak lehetséges.<br/><br/> Emellett a Contoso biztosítani szeretné a hibaérzékeny pont a webes szint csökkentése érdekében.
**Korlátozások** | Az alkalmazás egy ASP.NET-alkalmazás és a egy ugyanazon a virtuális Gépen futó WCF szolgáltatás áll. Szeretné ez elosztja a két webalkalmazást az Azure App Service használatával. 
**Azure** | Contoso szeretne áthelyezni az alkalmazás az Azure-ba, de nem szeretné futtatni, a virtuális gépeken. A Contoso biztosítani szeretné kihasználni az Azure PaaS-szolgáltatások a webes és az adatokat. 
**DevOps** | A Contoso biztosítani szeretné egy fejlesztési és üzemeltetési modell, Azure DevOps használatával a buildelési és kiadási folyamatok.

## <a name="solution-design"></a>Megoldásterv

Után rögzíthet célokat és követelményeket állapította meg, a Contoso tervez, és tekintse át a telepítési megoldás, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, amelyek az áttelepítéshez használt Átviteltípust.

### <a name="current-app"></a>Aktuális alkalmazás

- A SmartHotel360 helyszíni alkalmazás többszintű (WEBVM és SQLVM) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


### <a name="proposed-solution"></a>Javasolt megoldás

- Az alkalmazás az adatbázisszinten, a Contoso Azure SQL Database képest az SQL Server használatával [Ez a cikk](https://docs.microsoft.com/azure/sql-database/sql-database-features). A Contoso szeretnék felhőszolgáltatót váltani az Azure SQL Database néhány okok miatt:
    - Az Azure SQL Database felügyelt relációs adatbázis-szolgáltatás. It delivers predictable performance at multiple service levels, with near-zero administration. Előny az állásidő, beépített intelligens optimalizálással, és globális méretezhetőség és rendelkezésre állás a dinamikus méretezhetőség.
    - Contoso kihasználhatja a Data Migration Assistant (DMA) segítségével mérheti fel, és a helyszíni adatbázis migrálása az Azure SQL egyszerűsített.
    - Frissítési garanciával működő a Contoso kedvezményes díjszabást kínál a egy SQL Database, az Azure Hybrid Benefit használatával az SQL Server meglévő licenceinek tudjon cserélni. Ez akár 30 %-os megtakarítás nyújtani.
    - Az SQL Database számos olyan biztonsági funkciók, például mindig titkosított, dinamikus adatmaszkolást és a sorszintű biztonság/fenyegetések észlelése.
- Az alkalmazás webes réteg a Contoso az Azure App Service határozott meg. A PaaS-szolgáltatás lehetővé teszi, hogy telepítse az alkalmazást néhány konfigurációs módosítás. Contoso módosítsa a Visual Studio használatával, és két webes alkalmazások üzembe helyezése. Egy a webhelyhez, és egy, a WCF-szolgáltatás.
- Fejlesztési és üzemeltetési folyamatok követelményeinek, Contoso használata az Azure DevOps Source Code Management (SCM) Git-tárházak ki van választva. Automatizált buildekig és a kiadás a kód felépítéséhez használt, és üzembe helyezése az Azure Web Apps.
  
### <a name="solution-review"></a>Megoldás áttekintése
Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Consideration** | **Részletek**
--- | ---
**Szakemberek számára** | A SmartHotel360 alkalmazáskód nem kell módosítani az Azure-ba való migrálásra.<br/><br/> Contoso kihasználhatják a befektetés frissítési garanciával rendelkező SQL Server és a Windows Server az Azure Hybrid Benefit használatával.<br/><br/> Az áttelepítés után a Windows Server 2008 R2 nem kell támogatni. [További információk](https://support.microsoft.com/lifecycle).<br/><br/> Contoso is konfigurálhatja a webes szint, az alkalmazás több példánya, így már nem olyan hibaérzékeny pont.<br/><br/> Az adatbázis már nem SQL Server 2008 R2 elévülési függ.<br/><br/> Az SQL Database támogatja a műszaki követelményeket. A Contoso amelynek megfelelőségét ellenőrizni kell a helyszíni adatbázis, a Database Migration Assistant eszközzel, és talált a rendszer kompatibilis.<br/><br/> SQL-adatbázis, amely a Contoso beállítása nem kell beépített hibatűrő képességgel rendelkezik. Ez biztosítja, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | Az Azure App Services csak egy alkalmazás központi telepítési támogatja minden webes alkalmazáshoz. Ez azt jelenti, hogy a két webalkalmazást üzembe helyezett (egyet a webhelyhez) és a WCF-szolgáltatást kell lennie.<br/><br/> Ha a Contoso Data Migration Assistant helyett Data Migration Service-t használ az adatbázis migrálása, azt nem kell az infrastruktúra készen áll a migrálás adatbázisok ipari méretekben. Contoso kell hozhat létre, ha az elsődleges régióban nem érhető el, győződjön meg arról, hogy feladatátvételt egy másik régióban.

## <a name="proposed-architecture"></a>Javasolt architektúra

![Forgatókönyv-architektúra](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Áttelepítési folyamat

1. A Contoso kiosztja az Azure SQL-példány és áttelepíti a a SmartHotel360 adatbázis.
2. Contoso látja el és konfigurálja a Web Apps, és azokat a SmartHotel360 alkalmazást helyezünk üzembe.

    ![Áttelepítési folyamat](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso DMA segítségével mérheti fel, és észleli a kompatibilitási problémákat, amelyek hatással lehetnek a funkció az Azure-ban. A DMA értékeli a funkcióparitás SQL források és célok között, és a teljesítmény- és megbízhatóságbeli fejlesztéseket javasol. | Ez egy ingyenesen letölthető eszköz.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Egy intelligens, teljes körűen felügyelt felhőalapú relációsadatbázis-szolgáltatás. | Költség funkciók, az átviteli sebesség és a mérete alapján. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Az Azure App Services - webalkalmazások](https://docs.microsoft.com/azure/app-service/overview) | Hozzon létre sokoldalú felhőalapú alkalmazásokat egy teljes körűen felügyelt platform használatával | Költség méretét, helyét és használat alapján. [További információk](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Az Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Folyamatos integráció és készregyártás (CI/CD) folyamatot biztosít az alkalmazások fejlesztéséhez. A folyamat egy Git-tárház kódját, egy kibocsátáskezelési rendszert, csomagok és más build-összetevőket és egy rendszert üzembe helyezéséhez a változások fejlesztési, tesztelési és éles környezetek kezelése kezdődik. 

## <a name="prerequisites"></a>Előfeltételek

A következő Contoso kell futtatni az ebben a forgatókönyvben:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Contoso előfizetések létrehozása közben egy korai cikket. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Az Azure-ban egy SQL Database-példány üzembe helyezése**: Contoso látja el egy SQL-példány az Azure-ban. Miután az alkalmazás webhelye migrálása az Azure-ba, a WCF-szolgáltatás webes alkalmazás ezt a példányt fog mutatni.
> * **2. lépés: A DMA-adatbázis migrálása**: Contoso áttelepíti a Database Migration Assistant alkalmazás adatbázis.
> * **3. lépés: Provision Web Apps**: Contoso látja el a két webalkalmazást.
> * **4. lépés: Állítsa be az Azure DevOps**: Contoso létrehoz egy új Azure DevOps-projektet, és importálja a Git-tárház.
> * **5. lépés: Kapcsolati karakterláncok konfigurálása**: Contoso konfigurálja a kapcsolati karakterláncokat a webes szint web app, a WCF service web app és az SQL-példány közötti kommunikációhoz.
> * **6. lépés: Állítsa be a build és a folyamatok felszabadítása**: Utolsó lépésként Contoso beállítja a buildelési és kiadási folyamatok létrehozásához az alkalmazás, és telepíti őket a két különálló Azure Web Apps.


## <a name="step-1-provision-an-azure-sql-database"></a>1. lépés: Az Azure SQL-adatbázis kiépítése

1. Contoso rendszergazdák válassza ki az SQL-adatbázis létrehozása az Azure-ban. 

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


## <a name="step-2-migrate-the-database-with-dma"></a>2. lépés: A DMA-adatbázis migrálása

Contoso rendszergazdák telepítse át a SmartHotel360 adatbázis DMA segítségével.

### <a name="install-dma"></a>Telepítse a DMA

1. Töltse le az eszközt a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) , a helyszíni SQL Server rendszerű virtuális gép (**SQLVM**).
2. A virtuális gépen futnak a telepítés (DownloadMigrationAssistant.msi).
3. Az a **Befejezés** lapon válasszon **indítsa el a Microsoft Data Migration Assistant** a varázsló befejezése előtt.

### <a name="migrate-the-database-with-dma"></a>A DMA-adatbázis migrálása

1. A DMA-ban, új projekt létrehozása (**SmartHotelDB**), és válassza ki **áttelepítése** 
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

9. Az áttelepítés befejezését követően a Contoso rendszergazdák ellenőrizheti, hogy az adatbázis működik-e az Azure SQL-példányon.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Ezek a további SQL-adatbázis törlése **SmartHotel.Registration** az Azure Portalon.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>3. lépés: Provision Web Apps

Adatbázis-kezelő telepíti át, Contoso-rendszergazdák mostantól építhető ki a két webalkalmazást.

1. Kiválasztják **webalkalmazás** a portálon.

    ![Webalkalmazás](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Biztosítanak az alkalmazás nevét (**SHWEB-EUS2**), futtassa a Windows, és helyezze el, törölje az erőforrásokat csoportot **ContosoRG**. Akkor hozzon létre, és új app service-csomag.

    ![Webalkalmazás](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Után a webes alkalmazás üzembe, ismételje meg a folyamatot hozhat létre egy webalkalmazást, a WCF-szolgáltatás (**SHWCF-EUS2**)

    ![Webalkalmazás](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Miután végzett, az alkalmazásokat, ellenőrizze, hogy sikeres létrehozása címére megkeresését.


## <a name="step-4-set-up-azure-devops"></a>4. lépés: Az Azure DevOps beállítása


Contoso cégnek szüksége van, fejlesztési és üzemeltetési infrastruktúra és az alkalmazás folyamatokat hozhat létre.  Ehhez Contoso rendszergazdák új DevOps-projekt létrehozása, importálása a kódot, majd állítsa be a build és folyamatok felszabadítása.

1.   A Contoso Azure DevOps-fiókban lévő új projekt létrehozása (**ContosoSmartHotelRefactor**), és válassza ki **Git** verziókezeléshez.

    ![Új projekt](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. A Git-adattár, amely jelenleg rendelkezik az alkalmazás kódját importálja azokat. Van egy [nyilvános adattár](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) és töltheti le.

    ![Alkalmazáskód letöltése](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. A kód az importálása után, a Visual Studio csatlakozni az adattárhoz, és klónozza a kód Team Explorer használatával.

    ![Csatlakozás a projekthez](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. A-adattárat a fejlesztői gépen való klónozták, miután az alkalmazás a megoldás fájl megnyitásakor. A web app és a wcf service rendelkező külön projekt a fájlon belül.

    ![Megoldásfájl](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>5. lépés: Kapcsolati sztringek konfigurálása

Contoso rendszergazdák kell, hogy a web apps és az adatbázis összes kommunikálhatnak. Ehhez konfigurálják a kapcsolati karakterláncokat a kódban, és a web apps.

1. A WCF-szolgáltatás az web app alkalmazásban (**SHWCF-EUS2**) > **beállítások** > **Alkalmazásbeállítások**, adnak hozzá egy új kapcsolati sztring  **DefaultConnection**.
2. A kapcsolati karakterláncot a lehívja a **SmartHotel-regisztrációs** adatbázis és a megfelelő hitelesítő adatokkal kell frissíteni.

    ![Kapcsolati sztring](media/contoso-migration-refactor-web-app-sql/string1.png)

3. A Visual Studio használatával, megnyitja a **SmartHotel.Registration.wcf** projektet a a megoldásfájlt. A **kapcsolati Sztringjei** szakasz a web.config fájlt a WCF-szolgáltatás SmartHotel.Registration.Wcf frissíteni kell a kapcsolati karakterlánccal.

     ![Kapcsolati sztring](media/contoso-migration-refactor-web-app-sql/string2.png)

4. A **ügyfél** a web.config fájlt a SmartHotel.Registration.Web szakaszában módosítani kell, hogy a WCF-szolgáltatás az új helyre mutasson. Ez az a WCF-webalkalmazás üzemeltetése a Szolgáltatásvégpont URL-CÍMÉT.

    ![Kapcsolati sztring](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. A módosítások vannak a kódot, miután a rendszergazdáknak kell gombra a módosítások véglegesítéséhez. A Visual Studio Team Explorer használatával azok commmit és szinkronizálása.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>6. lépés: Állítsa be a build és az Azure fejlesztési és üzemeltetési folyamatok felszabadítása

Contoso-rendszergazdák mostantól konfigurálhatja az Azure DevOps-hajtsa végre a build és kiadás folyamat.

1. Az Azure DevOps, kattintson **készítése és kiadása** > **új adatcsatorna**.

    ![Új adatcsatorna](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. Kiválasztják **Azure Git-Adattárakkal** és a megfelelő tárházban.

    ![A Git és a tárház](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. A **válasszon ki egy sablont**, azok válassza ki, hogy az ASP.NET-sablont, közelednek.

     ![ASP.NET template](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. A név **ContosoSmartHotelRefactor – ASP.NET-CI** build szolgál. Kattintanak **várólistára & mentése**.

     ![Mentés és üzenetsorba](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. Ez az első build címmel. A buildszám, és tekintse meg a folyamat a kattintanak. A befejezése után megtekinthetik a folyamat visszajelzést, és kattintson a **összetevők** az összeállítási eredmények áttekintéséhez.

    ![Áttekintés](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. A mappa **Drop** a build eredményeit tartalmazza.

    - A két zip-fájlokat a csomagokat, amelyek tartalmazzák az alkalmazások.
    - Ezek a fájlok használhatók a kibocsátási folyamat üzembe helyezés az Azure Web Appshez

     ![Összetevő](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. Kattintanak **kiadásokban** > **+ új adatcsatorna**.

    ![Új adatcsatorna](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Akkor válassza ki az Azure App Service központi telepítési sablont.

    ![Azure App Service-sablon](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. Ezek a kibocsátási folyamat neve **ContosoSmartHotel360Refactor**, és a WCF-webalkalmazás (SHWCF-EUS2) nevét adja meg a **fázis** nevét.

    ![Környezet](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. A szakasz alatt kattintson **1 feladat, 1. feladat** a WCF-szolgáltatás központi telepítésnek a konfigurálásához.

    ![A WCF üzembe helyezése](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. Ezek a cégek ellenőrzik az előfizetés kiválasztva, és jogosult, és kattintson a **App service neve**.

     ![Válassza ki az app Service-ben](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. A folyamat a > **összetevők**, kiválasztják **és a egy összetevő hozzáadása**, és hozhat létre, és válassza ki a **ContosoSmarthotel360Refactor** folyamat.

     ![Felépítés](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. Kattintson a Villám az összetevő be van jelölve., amely lehetővé teszi a folyamatos készregyártás eseményindítója.

     ![Villám](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. A folyamatos készregyártás eseményindítója értékre kell állítani **engedélyezve**.

   ![Engedélyezve van a folyamatos üzembe helyezés](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. Most, lépjen vissza az 1. fázis feladat e feladatok, és kattintson **üzembe helyezése az Azure App Service**.

    ![App service üzembe helyezése](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. A **egy fájl vagy mappa kiválasztása**, akkor keresse meg a **SmartHotel.Registration.Wcf.zip** fájlt, amely a build és a clilck során hozott létre **mentése**.

    ![A WCF mentése](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. Kattintanak **folyamat** > **szakaszok** **+ Hozzáadás**, környezetet hozzáadandó **SHWEB-EUS2**. Akkor válassza ki egy másik Azure App Service-környezet.

    ![Adja hozzá a környezet](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. Ezek ismételje meg az eljárást a webalkalmazás közzététele (**SmartHotel.Registration.Web.zip**) fájlt a megfelelő webalkalmazáshoz.

    ![Webalkalmazás közzététele](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. Miután elmentette, a kiadási folyamathoz jelennek meg a következő.

     ![Kiadási folyamat összegzése](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. Lépjen vissza a **hozhat létre**, és kattintson a **eseményindítók** > **engedélyezze a folyamatos integrációt**. Ez lehetővé teszi a folyamatot, hogy amikor módosításokat fontos, hogy a kódot, és teljes buildelési és kiadási történik.

    ![Folyamatos integráció engedélyezése](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. Kattintanak **várólistára & mentése** futtathatja a teljes folyamatot. Által aktivált, amely az alkalmazás első kiadása az Azure App Service létrehoz egy új létrehozást.

    ![Folyamat mentése](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Contoso rendszergazdái is hajtsa végre a build és kiadás az Azure DevOps folyamatról. A létrehozás befejezése után a kiadási indul el.

    ![Hozhat létre, és az alkalmazás kiadása](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. A folyamat befejezése után mindkét hely lett telepítve, és az alkalmazás nem működőképes és futó online állapotú.

    ![A Befejezés kiadás](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

Ezen a ponton az alkalmazás sikeresen migrálva az Azure-bA.



## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Áttelepítés után a Contoso cégnek szüksége van, karbantartása a lépések elvégzéséhez:  

- Távolítsa el a helyszíni virtuális gépek a vCenter-készlet.
- Távolítsa el a virtuális gépek helyi biztonsági mentési feladatok.
- Frissítse a belső dokumentációjában az új helyeket a SmartHotel360 alkalmazás megjelenítéséhez. Az adatbázis-futó Azure SQL database és az előtér, két webalkalmazást futtató megjelenítése.
- Tekintse át az erőforrásokat, amelyek interakciót folytatni a leszerelt virtuális gépeket, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.


## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és tegye biztonságossá a új infrastruktúrára.

### <a name="security"></a>Biztonság

- Contoso cégnek szüksége van ahhoz, hogy az új **SmartHotel-regisztrációs** adatbázis biztonságos. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Contoso, a web apps, az SSL-tanúsítványokkal használatához frissítenie kell.

### <a name="backups"></a>Biztonsági másolatok

- Contoso cégnek szüksége van, tekintse át az Azure SQL Database biztonsági mentés követelményeinek. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso is szüksége van az SQL Database biztonsági mentéseinek kezelésével kapcsolatos, és helyreállítja. [További](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) kapcsolatos automatikus biztonsági mentést.
- Contoso érdemes fontolóra feladatátvételi csoportok regionális feladatátvételt biztosít az adatbázishoz. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso cégnek szüksége van, érdemes megfontolni a webalkalmazást a fő USA keleti RÉGIÓJA 2 és a rugalmasság az USA középső régiójában. Contoso sikerült konfigurálni a Traffic Manager feladatátvétel esetén a regionális üzemkimaradások utáni helyreállításon biztosítása érdekében.

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Erőforrások üzembe helyezését követően Contoso rendelje hozzá a az Azure címkék alapján saját [infrastruktúrák tervezése](contoso-migration-infrastructure.md#set-up-tagging).
- Az összes licencelése a költség, a Contoso fogyaszt PaaS-szolgáltatások be van építve. Ez a program levonja a nagyvállalati szerződés.
- Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.

## <a name="conclusion"></a>Összegzés

Ez a cikk a Contoso refactored az alkalmazás előtérbeli virtuális gép áttelepítése két Azure Web Apps révén a SmartHotel360 alkalmazást az Azure-ban. Az alkalmazás-adatbázis lett migrálva az Azure SQL-adatbázis.






