---
title: A helyszíni számítási feladatok Azure-bA a Contoso migrálásának értékelése |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso felméri az Azure Migrate és a Data Migration Assistant használatával a helyszíni gépek az Azure-ba való migrálásra.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: raynew
ms.openlocfilehash: daf75987a49d2ce90a436fdfcff1a85d2fc0ca21
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300404"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso áttelepítése: Helyszíni számítási feladatok Azure-ba való migrálásának értékelése

Ebben a cikkben a Contoso felméri a helyszíni SmartHotel360 alkalmazást az Azure-ba való migrálásra.

Ez a cikk egy sorozat, amely a fiktív Contoso hogyan a helyszíni erőforrásokkal áttelepíti a Microsoft Azure felhőbe dokumentumok részét képezi. A sorozat háttér-információkat is tartalmaz, és részletes telepítési forgatókönyvek, amelyek bemutatják, hogyan állíthat be egy áttelepítési infrastruktúra felmérheti a a helyszíni erőforrások migrálásra való alkalmasságát, és futtassa a különböző típusú áttelepítéseket. Forgatókönyvek egyre összetettebbé válnak. Cikkek idővel felveszi az adatsorozat.

Cikk | Részletek | status
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden cikk ugyanazon az infrastruktúrán használható. | Elérhető
3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Ez a cikk
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Azt is áttelepíti az előtér-, az Azure Site Recovery szolgáltatással az alkalmazás. Az alkalmazás-adatbázis, áttelepíti egy Azure SQL Database felügyelt példányába az Azure Database Migration Service segítségével. | Elérhető
[5. cikk: Az Azure virtuális gépein egy alkalmazás újratárolása](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel360 alkalmazás számára, hogy az alkalmazás virtuális gépek és a Database Migration Service az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használatával. | Elérhető
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek, a Site Recovery szolgáltatás használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződött. | Elérhető
[8. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával. Az alkalmazás-adatbázis, áttelepíti az Azure Database for MySQL-hez a MySQL Workbench használatával. | Elérhető
[9. cikk: Az Azure web App alkalmazásban és az Azure SQL Database alkalmazás újrabontása](contoso-migration-refactor-web-app-sql.md) | Contoso a SmartHotel360 alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése egy Azure SQL Server-példány az a Database Migration Assistant szolgáltatást. | Elérhető
[10. cikk: Refaktorovat egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL-hez](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Elérhető
[11. cikk: Refaktorovat a Team Foundation Serverrel az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető


## <a name="overview"></a>Áttekintés

Contoso úgy véli, hogy az Azure-ba való migrálás, mivel a vállalat a műszaki és pénzügyi értékelés van a felhőbe való migrálásra alkalmas-e a helyszíni számítási feladatok futtatásához. A Contoso csapata különösen szeretné felmérheti a gépek és adatbázisok kompatibilitását. Ha meg szeretné becsülni a kapacitás és a Contoso-erőforrások Azure-ban futó szeretné.

Első lépések, és jobban megérthessék az érintett technológiákat, a Contoso felméri két a helyszíni alkalmazások esetében a következő táblázat foglalja össze. A vállalat áttelepítési forgatókönyvek felméri a migrálásra, áthelyezési és újrabontása alkalmazásokat. További információ újratárolása, és a újrabontás a [Contoso áttelepítése – áttekintés](contoso-migration-overview.md).

App neve | Platform | Alkalmazás szinten | Részletek
--- | --- | --- | ---
SmartHotel360<br/><br/> (a Contoso utazási követelmények kezeli) | Windows fut az SQL Server-adatbázis | Kétszintű alkalmazás. Az előtérbeli ASP.NET-webhely futtat egy virtuális gép (**WEBVM**) és a egy másik virtuális gépen fut az SQL Server (**SQLVM**). | Virtuális gépek a VMware vCenter-kiszolgáló által kezelt ESXi-gazdagépen futó.<br/><br/> Letöltheti a mintaalkalmazást a [GitHub](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (A Contoso ügyfélszolgálati-alkalmazás) | Linux/Apache, a MySQL PHP (a LAMP) fut | Kétszintű alkalmazás. Egy előtér-php-alapú webhely egy virtuális gépen fut (**OSTICKETWEB**) és a MySQL-adatbázist futtat egy másik virtuális Géphez (**OSTICKETMYSQL**). | Az alkalmazás belső alkalmazottai és külső ügyfelek esetében a problémák nyomon követésére ügyfélalkalmazások szolgáltatást használják.<br/><br/> Letöltheti a mintát a [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuális architektúra

Ez az ábra bemutatja az aktuális Contoso a helyszíni infrastruktúra:

![Aktuális Contoso-architektúra](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso egyik fő adatközpont rendelkezik. Az adatközpontban található a a város, New York-i keleti Egyesült Államokban található.
- Contoso három további helyszíni ágakat Egyesült államokbeli városba rendelkezik.
- A fő adatközpont csatlakozik az internetre egy fiber Metro Ethernet-kapcsolat (500 MB/s).
- Minden egyes fiókiroda az interneten helyileg kapcsolódik, térjen vissza a fő adatközpont IPsec VPN-alagutak rendelkező vállalati szintű kapcsolatokon keresztül. A telepítő lehetővé teszi, hogy a Contoso teljes véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpont teljes VMware-rel lesz virtualizálva. Contoso két vCenter Server 6.5-ös által kezelt ESXi 6.5-ös virtualizációs gazdagépeket tartalmaz.
- Contoso Active Directoryt identitáskezeléshez használ. Contoso DNS-kiszolgálókat használ, a belső hálózaton.
- A tartományvezérlők VMware virtuális gépek futtatását az adatközpontban. A tartományvezérlők helyi ágat, fizikai kiszolgálókon futnak.

## <a name="business-drivers"></a>A stratégiai

A Contoso informatikai vezetőségi szorosan együttműködik a vállalat üzleti partnerek megértéséhez, az üzleti szeretne való áttérés érhet el:

- **Üzleti növekedés cím**: Contoso nő. Ennek eredményeképpen nyomás növelte a vállalat a helyszíni rendszerek és infrastruktúra.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárások és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára. Az ügyfelek igényei kell gyors és a nem Hulladékmennyiség idő vagy költséget takaríthat meg, így a vállalat informatikai üzleti igények gyorsabban is készíthetnek.
- **A gyorsaság növeléséhez**:  Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a cég számára a globális gazdaság végrehajtott módosításokat a képesnek kell lennie. Contoso IT nem kell a területük vagy egy üzleti blocker válnak.
- **Méretezési csoport**: Növekedésével a vállalat üzleti sikeresen a Contoso informatikai rendszerek, amelyek ütemben növekedhet kell megadnia.

## <a name="assessment-goals"></a>Értékelés célok

A Contoso felhőalapú csapat azonosította a migrálás értékelések célok:

- Az áttelepítés után alkalmazások az Azure-ban még ma a helyszíni VMWare-környezetet a Contoso rendelkező alkalmazások azonos teljesítménybeli képességeinek kell rendelkeznie. A felhőbe való áthelyezés nem jelenti azt, hogy teljesítményének kevésbé fontos.
- Contoso meg kell ismernie az alkalmazások és adatbázisok kompatibilitását az Azure követelményeinek. Contoso is meg kell ismernie, a üzemeltetési lehetőségek az Azure-ban.
- A Contoso adatbázis felügyeleti kerülendő, alkalmazásokat a felhőbe való áthelyezése után.  
- A Contoso biztosítani szeretné tudni, nem csak az áttelepítési lehetőségek, de a költségeket az infrastruktúrát a felhőbe való áthelyezése után is.

## <a name="assessment-tools"></a>Értékelési eszközök

Contoso Microsoft-eszközöket használ a migrálási felmérést. Az eszközök összhangba kerüljenek a vállalati célok, és a Contoso kell biztosítania az összes szükséges információt.

Technológia | Leírás | Költségek
--- | --- | ---
[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso segítségével mérheti fel, és észleli a kompatibilitási problémákat, amelyek hatással lehetnek a funkció az Azure Data Migration Assistant szolgáltatást használja. Data Migration Assistant felméri funkcióparitás SQL források és célok között. Teljesítmény- és megbízhatóságbeli fejlesztéseket javasol azt. | Data Migration Assistant szolgáltatást egy olyan ingyenes, letölthető eszköz.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso az Azure Migrate szolgáltatás segítségével felmérheti a VMware virtuális gépeket. Az Azure Migrate felméri a gépek migrálásra való alkalmasságát. Az Azure-ban futó biztosít méretezési és költségbecslést.  | 2018 május az Azure Migrate egy olyan ingyenes szolgáltatás.
[Szolgáltatástérkép](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Az Azure Migrate a Service Map használatával a vállalat szeretne áttelepíteni a gépek közötti függőségek megjelenítése. | A Service Map az Azure Log Analytics részét képezi. Contoso jelenleg a Service Map 180 napig díjmentesen használható.

Ebben a forgatókönyvben a Contoso letölti és futtatja a Data Migration Assistant szolgáltatást az utazási alkalmazás helyszíni SQL Server-adatbázis értékelésére. Contoso használja az Azure Migrate és függőségi leképezés használatával értékeljük az alkalmazás virtuális gépek Azure-ba való migrálás előtt.

## <a name="assessment-architecture"></a>Migrálásfelmérési architektúra

![Migrálásfelmérési architektúra](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso egy fiktív egy jellemző nagyvállalati szervezet jelölő nevet.
- Contoso rendelkezik egy helyszíni adatközpont (**contoso-datacenter**) és a helyszíni tartományvezérlő (**CONTOSODC1**, **CONTOSODC2**).
- VMware virtuális gépek 6.5-ös verziójú VMware ESXi-gazdagépek találhatók (**contosohost1**, **contosohost2**).
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**, egy virtuális gépen futó).
- A SmartHotel360 utazási alkalmazás a következő jellemzőkkel rendelkeznek:
    - Az alkalmazás két VMware virtuális gépek között többszintű (**WEBVM** és **SQLVM**).
    - VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com**.
    - A virtuális gépek futnak a Windows Server 2008 R2 Datacenter SP1.
- A VMware-környezetet egy virtuális gépen futó vCenter Server (**vcenter.contoso.com**) felügyeli.
- Ügyfélszolgálati alkalmazás osTicket szolgáltatás:
    - Az alkalmazás két virtuális gép között többszintű (**OSTICKETWEB** és **OSTICKETMYSQL**).
    - A virtuális gépek futnak, Ubuntu Linux Server 16.04-LTS.
    - **OSTICKETWEB** fut, Apache-2 és a PHP 7.0-ban.
    - **OSTICKETMYSQL** MySQL 5.7.22 fut-e.

## <a name="prerequisites"></a>Előfeltételek

A Contoso és más felhasználók az értékelés a következő előfeltételeknek kell megfelelnie:

- Tulajdonosi vagy közreműködői jogosultságot az Azure-előfizetést, vagy egy erőforráscsoport, az Azure-előfizetésében.
- A helyszíni vCenter 6.5-ös, 6.0 vagy 5.5-ös verziót futtató kiszolgálópéldány.
- Egy csak olvasható fiókot vCenter-kiszolgáló, vagy hozzon létre egyet engedélyekkel.
- Engedélyek virtuális gép létrehozása a vCenter Server-példányon egy .ova-sablon használatával.
- Legalább egy ESXi-gazdagép 5.5-ös vagy újabb verziót futtat.
- Legalább két helyszíni VMware virtuális gép, amelyek közül az egyik egy SQL Server-adatbázist futtat.
- Engedélyek az Azure Migrate-ügynökök telepítéséhez minden virtuális gépen.
- A virtuális gépeknek közvetlen internetkapcsolattal kell rendelkezniük.  
    - Internet-hozzáférést korlátozhatja a [szükséges URL-címekre](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).  
    - Ha a virtuális gépek nem rendelkezik internetkapcsolattal, az Azure [Log Analytics-átjáró](../azure-monitor/platform/gateway.md) telepíteni kell őket, és az ügynök forgalmat rajta keresztül.
- Az SQL Server-példányt futtató virtuális gép teljes tartományneve az adatbázis-értékeléshez.
- Az SQL Server virtuális gépen futó Windows tűzfalnak engedélyeznie kell a külső kapcsolatokat a 1433-as (alapértelmezett) TCP-porton. Ez a beállítás lehetővé teszi a Data Migration Assistant való csatlakozáshoz.

## <a name="assessment-overview"></a>– Áttekintés

Itt látható, hogyan hajtja végre a Contoso az értékelés:

> [!div class="checklist"]
> * **1. lépés: Töltse le és telepítse a Data Migration Assistant**: Contoso Data Migration Assistant felkészíti a helyszíni SQL Server-adatbázis értékelése.
> * **2. lépés: Az adatbázis értékelése a Data Migration Assistant**: Contoso fut, és az adatbázis-értékelés elemzi.
> * **3. lépés: Felkészülés a virtuális gépek Azure Migrate használatával**: Contoso állítja be a helyszíni fiókok és a VMware beállításait módosítja.
> * **4. lépés: Fedezze fel a helyszíni virtuális gépek Azure Migrate használatával**: Contoso hoz létre egy Azure Migrate gyűjtő virtuális Gépen. Ezt követően a Contoso a gyűjtő virtuális gépek felderítéséhez értékelés futtatja.
> * **5. lépés: Felkészülés a függőségelemzésre Azure Migrate használatával**: Contoso Azure Migrate-ügynökök telepítése a virtuális gépek, így láthatja, hogy a vállalat virtuális gépek közötti függőségi leképezés.
> * **6. lépés: A virtuális gépek értékelése az Azure Migrate használatával**: Contoso ellenőrzi a függőségeket, a csoportokat a virtuális gépeket, és futtatja az értékelést. Amikor készen áll az értékelés, Contoso elemzi a migrálás előkészítése az értékelést.

## <a name="step-1-download-and-install-data-migration-assistant"></a>1. lépés: Töltse le és telepítse a Data Migration Assistant

1. Contoso Data Migration Assistant szolgáltatást a letölti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Data Migration Assistant telepíthető minden olyan gép, amely képes kapcsolódni az SQL Server-példányt. Contoso nem szükséges az SQL Server-gépen futtatnia.
    - Az SQL Server-gazdagépen Data Migration Assistant nem futtatható.
2. Contoso fut, a letöltött telepítőfájlra (DownloadMigrationAssistant.msi) a telepítés megkezdéséhez.
3. Az a **Befejezés** lapon, a Contoso kiválasztja **indítsa el a Microsoft Data Migration Assistant** a varázsló befejezése előtt.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>2. lépés: A SmartHotel360 az adatbázis-kiértékelés futtatása és elemzése

Most a Contoso futtathatja a helyszíni SQL Server-adatbázis a SmartHotel360 alkalmazás értékelését.

1. Data Migration Assistant, a Contoso kiválasztja **új** > **értékelés**, és ezután megadja az értékelés a projekt nevét.
2. A **forráskiszolgáló típusa**, Contoso kiválasztja **SQL Server Azure virtuális gépeken**.

    ![Data Migration Assistant – forrás kiválasztása](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Jelenleg Data Migration Assistant nem támogatja a felmérés az Azure SQL Database felügyelt példányába történő áttelepítéséhez. Áthidaló megoldásként Contoso az SQL Server-beli virtuális gépen feltételezett célként az értékelés.

3. A **cél verziójának kiválasztása**, Contoso az SQL Server 2017-ben a célverzió, választja ki. Ezt a verziót választja, a verziót, amelyet az SQL Database felügyelt példányát, mert a Contoso cégnek szüksége van.
4. Contoso lehetőséget választja, jelentések, tudjon meg többet a kompatibilitási és az új funkciók segítik:
    - **Kompatibilitási problémák** vegye figyelembe, amely meghiúsulhat a migrálás, vagy egy kisebb beállítások áttelepítése előtt igénylő módosításokat. Ez a jelentés megtartja a Contoso szerez tudomást bármely elavult funkciók jelenleg használatban van. A problémák kompatibilitási szint szerint vannak rendezve.
    - **Új szolgáltatásokra vonatkozó javaslat** megjegyzések az áttelepítés után az adatbázis használható SQL Server célplatformon új funkciói. Új szolgáltatási javaslatok a pontok alapján vannak rendezve **teljesítmény**, **biztonsági**, és **tárolási**.

    ![Data Migration Assistant - kompatibilitási problémák és az új funkciók](./media/contoso-migration-assessment/dma-assessment-2.png)

2. A **Kapcsolódás kiszolgálóhoz**, Contoso beírja a nevét, a virtuális gép, amelyen fut az adatbázis és a hozzáférési hitelesítő adatokat. Contoso kiválasztja **megbízható kiszolgálói tanúsítvány** , hogy a virtuális gép hozzáférhet az SQL Server. Ezután kiválasztja a Contoso **Connect**.

    ![Data Migration Assistant – Csatlakozás kiszolgálóhoz](./media/contoso-migration-assessment/dma-assessment-3.png)

3. A **forrás hozzáadása**, Contoso ad hozzá, az adatbázis szeretné annak ellenőrzéséhez, és ezután kiválasztja **tovább** és a felmérés elindításához.
4. Az értékelés jön létre.

    ![Data Migration Assistant - értékelés létrehozása](./media/contoso-migration-assessment/dma-assessment-4.png)

5. A **felülvizsgálati eredmények**, Contoso megtekinti az értékelések eredményeinek.

### <a name="analyze-the-database-assessment"></a>Az adatbázis-értékelés elemzése

Eredmények jelennek meg, amint azok elérhetők. Ha Contoso hibákat javít, akkor válasszon **értékelés indítsa újra a** kattintva futtassa újra az értékelést.

1. Az a **kompatibilitási problémák** jelentésében a Contoso ellenőrzi az esetleges problémákat az egyes kompatibilitási szinten. A kompatibilitási szintek a következőképpen feleltethetők meg az SQL Server-verzióknak:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Data Migration Assistant - kompatibilitási problémák jelentés](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Az a **Szolgáltatásjavaslatok** jelentésében a Contoso megtekinti a teljesítmény, biztonság és tárolási szolgáltatások, amelyek az értékelés szerint az áttelepítés után. Számos különböző funkció használata akkor javasolt, többek között a In-Memory OLTP, az oszlopcentrikus indexek, Stretch Database, Always Encrypted, a dinamikus adatmaszkolás és transzparens adattitkosítás.

    ![Data Migration Assistant – a szolgáltatás javaslatokat tartalmazó jelentés](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso kell [transzparens adattitkosítás engedélyezése](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) minden SQL Server-adatbázisok. Ez akkor is kritikus fontosságú, ha egy adatbázist, amikor azt már a helyszínen üzemeltetett, mint a felhőben van. Transzparens adattitkosítás engedélyezni kell a csak a migrálás után. Transzparens adattitkosítás már engedélyezve van, ha a Contoso át kell helyeznie a tanúsítvánnyal vagy aszimmetrikus kulccsal a célkiszolgáló a master adatbázishoz. Ismerje meg, hogyan [transzparens adatok titkosítás által védett adatbázis áthelyezése egy másik SQL Server-példány](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Contoso az értékelés JSON vagy CSV formátumban is exportálhatja.

> [!NOTE]
> Nagy méretű értékelések:
> - Egyidejűleg futtathatók több értékelést, és az értékelések állapotát megtekintheti a a **összes értékelés** lapot.
> - Értékelések egyesíthetők egy [SQL Server-adatbázis](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017).
> - Értékelések egyesíthetők egy [Power BI-jelentés](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>3. lépés: Felkészülés a virtuális gépek Azure Migrate használatával

Contoso létre kell hoznia egy VMware-fiókot, amellyel az Azure Migrate automatikusan felderíti az virtuális gépek értékelése, ellenőrizze, hozzon létre egy virtuális Gépet, jegyezze fel a portot, amelyet meg kell nyitni, és állítsa be a statisztikai beállítások szintjét.

### <a name="set-up-a-vmware-account"></a>VMware-fiók beállítása

Virtuális gépek felderítésének egy csak olvasható fiókot vCenter-kiszolgáló, amely a következő tulajdonságokkal rendelkezik a szükséges:

- **Felhasználó típusa**: Legalább egy olvasási jogosultsággal rendelkező felhasználó.
- **Engedélyek**: Az Adatközpont-objektum, válassza ki a **propagálása az gyermekobjektum** jelölőnégyzetet. A **szerepkör**válassza **csak olvasható**.
- **Részletek**: A felhasználó hozzáférést az adatközpontban lévő összes objektumra az adatközpontszinten hozzárendelve.
- Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

### <a name="verify-permissions-to-create-a-vm"></a>A virtuális gép létrehozásához szükséges engedélyek ellenőrzése

Contoso ellenőrzi, hogy rendelkezik-e virtuális gép létrehozása egy .ova formátumú fájl importálásával. Ismerje meg, hogyan [hozzon létre és rendelhet egy szerepkört jogosultságokkal](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Portok ellenőrzése

A Contoso értékelés függőségi leképezés használ. Függőségi leképezés kell egy ügynököt telepíteni a virtuális gépek, amelynek megfelelését. Az ügynök kell lennie, hogy csatlakozni tudjon az Azure-hoz a 443-as TCP-port az egyes virtuális Gépeken. Ismerje meg [kapcsolati követelmények](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Statisztikai beállítások megadása

Contoso az üzembe helyezés megkezdése előtt, a vCenter Server statisztikai beállításait a 3. szintre kell beállítania.

> [!NOTE]
> - A szint beállítása után Contoso kell legalább egy napot vár az értékelés futtatása előtt. Ellenkező esetben az értékelés esetleg nem működnek megfelelően.
> - Ha a szint nagyobb mint 3, az értékelés működik, de:
>    - Lemezek és a hálózat teljesítményadatai nem lesznek gyűjtve.
>    - A tárolás tekintetében az Azure Migrate egy standard lemez létrehozását javasolja az Azure-ban, amelynek a mérete megegyezik a helyszíni lemezével.
>    - Az Azure-ban minden helyszíni hálózati adapterhez, a hálózat egy hálózati adapter használata javasolt.
>    - A számítási kapacitás az Azure Migrate a VM-magokat és a memória méretét, és azonos konfigurációjú Azure virtuális Gépeken javasolja. Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.
> - Méretezés a 3. szintre használatával kapcsolatos további információkért lásd: [méretezési](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

A szint beállítása:

1. A vSphere webes ügyfélben, a Contoso megnyílik a vCenter Server-példányt.
2. Contoso kiválasztja **kezelés** > **beállítások** > **általános** > **szerkesztése**.
3. A **statisztika**, Contoso állítja be a statisztikai szint beállítását **3. szint**.

    ![vCenter Server statisztikájának szintjét](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>4. lépés: Virtuális gépek felderítése

Virtuális gépek felderítéséhez, a Contoso Azure Migrate-projektet hoz létre. Contoso letölti és beállítja a gyűjtő virtuális Gépen. Ezt követően a Contoso futtatja a gyűjtő a helyszíni virtuális gépek felderítéséhez.

### <a name="create-a-project"></a>Projekt létrehozása

1. Az a [az Azure portal](https://portal.azure.com), Contoso keres **Azure Migrate**. Ezt követően a Contoso egy projektet hoz létre.
2. Contoso adja meg a projekt nevét (**ContosoMigration**) és az Azure-előfizetést. Létrehoz egy új Azure-erőforráscsoportot (**ContosoFailoverRG**).
    > [!NOTE]
    > - Azure Migrate-projektet csak az az USA nyugati középső Régiója és USA keleti régiójában hozhat létre.
    > - Bármilyen célhelyre tervezhet migrálást.
    > - A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

    ![Az Azure Migrate - migrálási projekt létrehozása](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy helyszíni virtuális gép, más néven a *gyűjtőberendezés*. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és elküldi metaadatait, a virtuális gépek az Azure Migrate szolgáltatás. A gyűjtőberendezés beállításához Contoso letölti az OVA sablont, és importálja a helyszíni vCenter Server-példány a virtuális gép létrehozásához.

1. Az Azure Migrate-projektben Contoso kiválasztja **bevezetés** > **felderítés és értékelés** > **gépek felderítése**. Contoso letölti az OVA sablon fájlt.
2. Contoso másolja a projekt Azonosítóját és kulcsát. A projekt és Azonosítóját a gyűjtő konfigurálásához szükségesek.

    ![Az Azure Migrate - az OVA-fájl letöltése](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A virtuális gép üzembe helyezése előtt Contoso ellenőrzi, hogy az OVA-fájl biztonságos:

1. A számítógépen, amelyre letöltötte a fájlt a Contoso megnyílik egy rendszergazdai parancsablakot.
2. Contoso futtatja a következő parancsot az OVA-fájl kivonatának:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **Példa**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell a kivonati értékek felsorolt [Itt](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#continuous-discovery).

### <a name="create-the-collector-appliance"></a>A gyűjtőberendezés létrehozása

Most Contoso importálni a letöltött fájlt a vCenter Server-példány és a gyűjtőberendezés virtuális gép kiépítése:

1. A vSphere Client-konzolon, a Contoso kiválasztja **fájl** > **OVF-sablon telepítése**.

    ![vSphere webes ügyfélben - telepítése OVF-sablon](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Az OVF sablon központi telepítése varázsló Contoso kiválasztja **forrás**, és az OVA-fájl helyét, majd adja meg.
3. A **név és hely**, Contoso a gyűjtő virtuális Gépnek megjelenített nevét adja meg. Ezt követően kiválasztva a készlet hely, ahol a virtuális gép üzemeltetni szeretné. Contoso adja meg azt is, a gazdagép vagy -fürt futtatására a gyűjtőberendezés.
4. A **tárolási**, Contoso tárolási helyét adja meg. A **lemezformátum**, Contoso választja ki, hogyan szeretné kiosztani a tárhelyet a.
5. A **Hálózatleképezés**, Contoso adja meg, amelyben a gyűjtő virtuális gép kapcsolódni a hálózathoz. A hálózati metaadatokat küldhet az Azure-bA internetkapcsolattal kell rendelkeznie.
6. Contoso ellenőrzi a beállításokat, majd ezután kiválasztja az **üzembe helyezés után bekapcsolási** > **Befejezés**. Ha a készülék jön létre, megjelenik egy üzenet, amely megerősíti, hogy a művelet sikeresen befejeződött.

### <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Most Contoso fog futni, a gyűjtő virtuális gépek felderítéséhez. Jelenleg az adatgyűjtő jelenleg csak támogatja **angol (Egyesült Államok)** az operációs rendszer nyelvét és az adatgyűjtő felület nyelveként.

1. A vSphere Client-konzolon, a Contoso kiválasztja **konzol megnyitása**. Contoso Itt adhatja meg, a nyelv, időzóna és a gyűjtő virtuális gép jelszóbeállításait.
2. Az asztalon, a Contoso kiválasztja a **gyűjtő futtatása** parancsikon.

    ![a vSphere Client-konzolon - gyűjtő parancsikonja](./media/contoso-migration-assessment/collector-shortcut.png)

3. Az Azure Migrate Collector Contoso választja **Előfeltételek beállítása**. A Contoso elfogadja a licencfeltételeket, és a külső eszközadatokat olvas be.
4. A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel, hogy az idő szinkronizálva van-e, és, hogy fut-e a gyűjtőszolgáltatás. (A gyűjtő szolgáltatás alapértelmezés szerint telepítve van a virtuális gépen.) Contoso VMware powercli-t is telepíti.

    > [!NOTE]
    > Azt feltételezzük, hogy a virtuális gép rendelkezik-e közvetlen internet-hozzáférés proxy használata nélkül.

    ![Az Azure Migrate Collector – Előfeltételek ellenőrzése](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. A **adja meg a vCenter Server adatait**, a Contoso beírja a nevét (FQDN) vagy a vCenter Server-példány IP-címet, és a csak olvasható hitelesítő adatokat használja a felderítéshez.
6. Contoso választja ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak virtuális gépek, amelyek a megadott hatókörön belül képes felderíteni. A hatókör egy adott mappájába, az adatközpontban vagy a fürt állítható. A hatókör nem tartalmazhat több mint 1500 virtuális gépet.

    ![Adja meg a VCenter Server adatait](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. A **adja meg a migrálási projekt**, Contoso belép az Azure Migrate-projekt Azonosítóját és a Portalról másolt kulcsot. A projekt Azonosítóját és kulcsát, Contoso is keresse fel a projekt **áttekintése** lap > **gépek felderítése**.  

    ![Adja meg a migrálási projektet](./media/contoso-migration-assessment/collector-connect-azure.png)

8. A **adatgyűjtési folyamat megtekintése**, Contoso felderítési figyelheti, és ellenőrizze, hogy a virtuális gépekről gyűjtött metaadatok hatókörében van. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.

    ![Adatgyűjtési folyamat megtekintése](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

Gyűjtemény befejezését követően a Contoso ellenőrzi, hogy a virtuális gépek megjelennek-e a portálon:

1. Az Azure Migrate-projektben Contoso kiválasztja **kezelés** > **gépek**. Contoso ellenőrzi, hogy láthatók-e a virtuális gépeket szeretné felderíteni.

    ![Az Azure Migrate - felderített gépek](./media/contoso-migration-assessment/discovery-complete.png)

2. A gépek jelenleg nem rendelkezik telepített Azure Migrate-ügynökkel. Contoso a függőségek megtekintéséhez ügynököket kell telepítenie.

    ![Az Azure Migrate - ügynök telepítése kötelező megadni](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>5. lépés: Felkészülés a függőségelemzésre

Szeretné felmérheti, hogy virtuális gépek közötti függőségek megtekintéséhez, Contoso letölti és telepíti az alkalmazást, virtuális gépek ügynökök. Contoso pedig az alkalmazások, mind a Windows és Linux rendszerű virtuális gépek ügynököket telepít.

### <a name="take-a-snapshot"></a>Pillanatkép készítése

Tárol egy másolatot a virtuális gépek őket módosítása előtt, a Contoso egy pillanatképet az ügynökök telepítése előtt.

![Gép pillanatképe](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

1. A **gépek**, Contoso választja ki a gépet. Az a **függőségek** oszlop, Contoso kiválasztja **telepítés szükséges**.
2. Az a **gépek felderítése** Contoso panelen:
    - Letölti a Microsoft Monitoring Agent (MMA) és a függőségi ügynök minden Windows virtuális gép számára.
    - Letölti az MMA és a függőségi ügynök minden egyes Linux virtuális gép számára.
3. Contoso másolja át a munkaterület Azonosítójára és kulcsára. Contoso van szüksége a munkaterület Azonosítóját és kulcsát, amikor telepíti az MMA.

    ![Ügynök letöltése](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Az ügynökök telepítése Windows virtuális gépeken

Contoso futtatja a telepítést az egyes virtuális Gépeken.

#### <a name="install-the-mma-on-windows-vms"></a>Windows virtuális gépeken az MMA telepítése

1. Contoso mértékeredményeket a letöltött ügynökre.
2. A **célmappa**, Contoso megtartja az alapértelmezett telepítési mappát, majd ezután kiválasztja az **tovább**.
3. A **ügynök telepítésének beállításai**, Contoso kiválasztja **az ügynök csatlakoztatása az Azure Log Analyticshez** > **tovább**.

    ![A Microsoft Monitoring Agent telepítése – ügynök telepítésének beállításai](./media/contoso-migration-assessment/mma-install.png)

4. A **Azure Log Analytics**, Contoso illeszti be a munkaterület-Azonosítót és a kulcsot, akkor a másolja a portálról.

    ![A Microsoft Monitoring Agent telepítése – az Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. A **telepítésre kész**, Contoso telepíti az MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Telepítse a függőségi ügynököt Windows virtuális gépek

1. Contoso mértékeredményeket a letöltött függőségi ügynökre.
2. A Contoso fogadja el a licencfeltételeket, és megvárja, amíg a telepítés befejeződik.

    ![Függőségi ügynök beállítása – telepítés](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Az ügynök telepítéséhez a Linux rendszerű virtuális gépek

Contoso futtatja a telepítést az egyes virtuális Gépeken.

#### <a name="install-the-mma-on-linux-vms"></a>Az MMA telepítése Linux rendszerű virtuális gépeken

1. Contoso a ctypes Python-kódtár az egyes virtuális Gépeken telepíti a következő paranccsal:

    `sudo apt-get install python-ctypeslib`
2. Contoso a parancsot az MMA-ügynök telepítéséhez rendszergazdaként kell futtatnia. Legfelső szintű válik, a Contoso futtatja a következő parancsot, és ezután beírja a gyökér szintű jelszó:

    `sudo -i`
3. Contoso telepíti az MMA:
    - Contoso kerül, a parancsban a munkaterület Azonosítójára és kulcsára.
    - A 64 bites parancsai.
    - A munkaterület-Azonosítót és elsődleges kulcsot a Log Analytics-munkaterületet az Azure Portalon található. Válassza ki **beállítások**, majd válassza ki a **csatlakoztatott források** fülre.
    - Töltse le a Log Analytics-ügynököket, a ellenőrzőösszegének ellenőrzése, és telepítse a következő parancsokat, és előkészítheti az ügynököt:

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>A függőségi ügynök telepítése Linux rendszerű virtuális gépeken

Az MMA telepítése után a Contoso a függőségi ügynököt telepít a Linux rendszerű virtuális gépeken:

1. A függőségi ügynök Linux-számítógépeken InstallDependencyAgent Linux64.bin, azt a héjparancsfájlt, amely rendelkezik egy önkicsomagoló bináris segítségével telepítve van. Sh használatával, vagy hogy a fájl hozzáadása a Contoso futtatások végrehajtási engedélyeket magát a fájlt.

2. Contoso legfelső szintű Linux-függőségi ügynök telepíti:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>6. lépés: A Virtuálisgép-kiértékelés futtatása és elemzése

Contoso mostantól ellenőrizze a gépek függőségeit, és hozzon létre egy csoportot. Ezután futtatja az értékelést a csoport számára.

### <a name="verify-dependencies-and-create-a-group"></a>Ellenőrizze a függőségeket, és hozzon létre egy csoportot

1. Annak megállapításához, mely számítógépek elemzéséhez, a Contoso kiválasztja **függőségek megtekintése**.

    ![Az Azure Migrate - gépek függőségeinek megtekintése](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Az SQLVM esetében a függőségi térkép a következő részleteket tartalmazza:

    - Folyamat csoportokat vagy folyamatokat, amelyek a megadott időszakban (alapértelmezés szerint egy óra) SQLVM futó aktív hálózati kapcsolatokkal rendelkeznek.
    - Az összes függő gép bejövő (ügyfél) és kimenő (kiszolgálói) TCP-kapcsolatai.
    - Telepített Azure Migrate-ügynökkel rendelkező függő gépek külön mezőkben jelennek meg.
    - Gépek, amelyek nem rendelkeznek telepített ügynökök a port és IP-címadatok jelennek meg.

3. A telepített ügynökkel (WEBVM) rendelkező gépek esetében a Contoso kiválasztja a gép mezőjére a további információk megtekintéséhez. Az információ tartalmazza a teljes tartománynév, az operációs rendszer és a MAC-cím.

    ![Az Azure Migrate - csoportos függőségek megtekintése](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso választja ki a virtuális gépeket (SQLVM és WEBVM) a csoportba való felvételéhez. Contoso Ctrl + kattintás használatával több virtuális gép kiválasztása.
5. Contoso kiválasztja **csoport létrehozása**, és ezután beírja a nevét (**smarthotelapp**).

    > [!NOTE]
    > A függőségek részletesebb megtekintéséhez terjessze ki az időtartományt. Válasszon ki egy adott időtartamot, vagy válassza ki a kezdő és záró dátuma.

### <a name="run-an-assessment"></a>Értékelés futtatása

1. A **csoportok**, Contoso megnyitja a csoport (**smarthotelapp**), majd kiválasztja, és **értékelés létrehozása**.

    ![Az Azure Migrate - értékelés létrehozása](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Az értékelés megtekintéséhez Contoso kiválasztja **kezelés** > **értékelések**.

Contoso használja az alapértelmezett értékelési beállításokat, de Ön is [beállítások testre szabása](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>A virtuálisgép-kiértékelés elemzése

Egy Azure Migrate-értékelések információt az Azure-ral a helyszíni kompatibilitás javasolt Azure virtuális gép megfelelő méretezéséhez, és a becsült havi Azure-költségekről.

![Az Azure Migrate - értékelési jelentés](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

![Az Azure Migrate - értékelés megjelenítése](./media/contoso-migration-assessment/assessment-display.png)

Az értékelés rendelkezik olyan megbízhatósági minősítéssel, amely 1 csillagtól az 5 csillagig terjed (1 csillag a legalacsonyabb és 5 csillag a legmagasabb).
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásának alapján van hozzárendelve.
- A minősítés segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés akkor hasznos, ha végez *teljesítményalapú*. Az Azure Migrate lehet, hogy nincs elegendő adatpont adatponttal a használatalapú méretezéshez. A *helyszíni* osztályozás, a megbízhatósági minősítés mindig 5 csillag, mert az Azure Migrate rendelkezik a virtuális gép méretezéséhez szükséges összes adatponttal.
- Az elérhető adatpontok százalékától függően van megadva a megbízhatósági minősítés:

   Adatpontok rendelkezésre állása | Megbízhatósági minősítés
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

#### <a name="verify-azure-readiness"></a>Azure-kompatibilitás ellenőrzése

![Az Azure Migrate - készenléti állapot értékelése](./media/contoso-migration-assessment/azure-readiness.png)  

Az értékelési jelentés a táblában az információkat, amelyek összegzése látható. Teljesítményalapú megjelenítéséhez az Azure Migrate kell a következő információkat. Ha az adatokat nem lehet összegyűjteni, értékelés pontatlan lehet.

- A processzor és a memória kihasználtsági adatai.
- A virtuális géphez csatlakoztatott összes lemez olvasási/írási IOPS-értéke és adatátviteli teljesítménye.
- A virtuális géphez csatlakoztatott összes hálózati adapter bejövő és kimenő hálózati forgalmának adatai.

Beállítás | Jelzése | Részletek
--- | --- | ---
**Azure-beli virtuális gép felkészültsége** | Azt jelzi, hogy a virtuális gép készen áll az áttelepítéshez. | Lehetséges állapotok:</br><br/>- Készen áll az Azure-beli migrálásra<br/><br/>- Feltételekkel kész <br/><br/>- Nem áll készen az Azure-beli migrálásra<br/><br/>- A felkészültség ismeretlen<br/><br/> Virtuális gép nem áll készen, ha az Azure Migrate látható néhány lehetséges javítási intézkedést.
**Azure-beli virtuális gép mérete** | A készen álló virtuális gépek az Azure Migrate biztosít egy Azure virtuális gép mérete javaslat. | A méretezési javaslat az értékelési tulajdonságoktól függ:<br/><br/>- Ha teljesítményalapú méretezést használt, a méretezés a virtuális gépek teljesítményelőzményeit veszi figyelembe.<br/><br/>-Ha a használt *helyszíni*, méretezés a helyszíni virtuális gép mérete alapján, és a kihasználtsági adatokat nem használja a rendszer.
**Ajánlott eszköz** | Azure-beli gépek az ügynökök futnak, mert az Azure Migrate megvizsgálja a gépen futó folyamatokat. Azonosítja a gép adatbázisgép-e.
**Virtuális gép adatai** | A jelentés a helyszíni virtuális gép, beleértve az operációs rendszer, a rendszerindítási típus és a lemez-és storage-beállításokat ismerteti.

#### <a name="review-monthly-cost-estimates"></a>Havi költségbecslések áttekintése

Ebben a nézetben látható a teljes számítási és tárolási költsége a virtuális gép futtatása az Azure-ban. Azt is bemutatja az egyes gépek részleteit.

![Az Azure Migrate - Azure-költségek](./media/contoso-migration-assessment/azure-costs.png)

- A költségbecslések a javaslatok a méretekkel kapcsolatban a gép kiszámítása.
- A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek.

## <a name="clean-up-after-assessment"></a>Értékelés után tisztítása

- Az értékelés befejeződése Contoso megmaradnak az Azure Migrate készülék értékelések a jövőben használni.
- Contoso a VMware virtuális gép kikapcsolása. Contoso használni fogja azt újra, amikor további virtuális gépek értékeli.
- Contoso tartja a **Contoso áttelepítési** projekt az Azure-ban. A projekt jelenleg telepítve van a **ContosoFailoverRG** East US Azure régióban található erőforráscsoportot.
-  A gyűjtő virtuális Gépnek a 180 napos próbaverzió licenccel rendelkezik. Ha ezt a korlátot lejár, Contoso töltse le a gyűjtőt, és újra be kell.

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben a Contoso a SmartHotel360 alkalmazás-adatbázis figyelése az adatok áttelepítési Frissítésfelmérő eszköz használatával. A helyszíni virtuális gépek az Azure Migrate szolgáltatás használatával értékeli azt. Contoso áttekinti az értékeléseket, győződjön meg arról, amelyek a helyszíni erőforrások készen áll az Azure-ba való migrálásra.

## <a name="next-steps"></a>További lépések

A sorozat következő cikkben a Contoso egy lift-and-shift-áttelepítés használatával áthelyezi a SmartHotel360 alkalmazás Azure-ban. Contoso az előtér-alkalmazás WEBVM áttelepíti az Azure Site Recovery használatával. Ez áttelepíti az alkalmazás-adatbázis egy Azure SQL Database felügyelt példánya a Database Migration Service használatával. [Első lépések](contoso-migration-rehost-vm-sql-managed-instance.md) ezzel az üzembe helyezéssel.
