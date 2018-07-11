---
title: A helyszíni számítási feladatok Azure-bA a Contoso migrálásának értékelése |} A Microsoft Docs
description: Ismerje meg, hogyan a Contoso felméri a helyszíni gépek az Azure-bA az Azure Migrálási és az adatbázis Migraton az áttelepítéshez
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: f0bbfd7c63ba187ef535168cbac05c3e09420d43
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917829"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso áttelepítése: a helyszíni számítási feladatok Azure-bA migrálásának értékelése

Ez a cikk bemutatja, hogyan értékeli a Contoso az Azure-ba való migrálásra való felkészüléskor a helyszíni SmartHotel alkalmazást.

Ez a dokumentum a harmadik a cikksorozatot, amely dokumentálja a fiktív Contoso hogyan a helyszíni erőforrásokkal áttelepíti a Microsoft Azure felhőbe. A sorozat háttér-információkat is tartalmaz, és a egy sorozat különböző telepítési forgatókönyvek esetén, amelyek bemutatják, hogyan állíthat be egy áttelepítési infrastruktúra felmérheti a a helyszíni erőforrások migrálásra való alkalmasságát, és futtassa a különböző típusú áttelepítéseket. Forgatókönyvek egyre összetettebbé válnak, és adunk hozzá további cikkek idővel.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes Contoso áttelepítési forgatókönyvek ugyanazon az infrastruktúrán használható. | Elérhető
3. cikk: Mérje fel a helyszíni erőforrásokhoz (Ez a cikk)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Mérje fel az alkalmazás virtuális gépek a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési Azure virtuális gépek és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Az alkalmazás előtérbeli virtuális gépet át [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az adatbázis használatával a [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás migrálása a felügyelt SQL-példányt. | Elérhető
[Cikk 5: Az Azure virtuális gépeken áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan a Contoso át SmartHotel alkalmazásuk virtuális gépek csak a Site Recovery használatával.
[A cikk 6: Újratárolás az Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. A Site Recovery számára, hogy az alkalmazás virtuális gépeit és a egy SQL Server rendelkezésre állási csoportot az alkalmazás-adatbázis áttelepítése a Database Migration service használnak. | Elérhető
[7. cikk: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan telepíti át a Contoso osService Linux alkalmazás az Azure Site Recovery használatával.
[A cikk 8: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a osService Linux-alkalmazás, virtuális gépek migrálása esetében a Site Recovery és a MySQL Workbench használatával történő áttelepítésének (az Azure MySQL Server-példány. | Elérhető


## <a name="overview"></a>Áttekintés

Ahogy, vegye figyelembe a migrálás az Azure-ba, Contoso vállalat műszaki és pénzügyi felmérést, döntse el, a helyszíni számítási feladatok alkalmasak-e a felhőbe való migrálásra szeretné. Különösen a Contoso csapat felmérheti a gépek és adatbázisok kompatibilitását, és megbecsülheti a kapacitás és a költségek az Azure-ban futó erőforrásaikat szeretné.

Értékelnek, és értelmezheti az érintett, azok fogjuk mérje fel a helyszíni alkalmazásaikat, két technológiákat a következő táblázat foglalja össze. Vegye figyelembe, hogy azok értékelni áttelepítési forgatókönyvek, áthelyezési és újrabontása alkalmazásokat az áttelepítéshez. További információ újratárolása, és a újrabontás a [Contoso áttelepítése – áttekintés](contoso-migration-overview.md).

**Alkalmazás neve** | **Platform** | **Alkalmazás szinten** | **Részletek**
--- | --- | --- | ---
SmartHotel<br/><br/> Kezeli a Contoso utazási követelmények | Az SQL Server-adatbázis a Windows rendszerű | Kétszintű alkalmazás és az előtér egy virtuális gép (WEBVM) és a egy másik virtuális gép (SQLVM) futó SQL Server futó ASP.NET-webhely | Virtuális gépek a VMware vCenter-kiszolgáló által kezelt ESXi-gazdagépen futó.<br/><br/> A mintaalkalmazás letölthető [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Contoso szolgáltatás ügyfélszolgálati alkalmazás | Linux/Apache, a egy MySQL-PHP (a LAMP) futnak. | Kétrétegű-alkalmazás és egy frontend php-alapú webhely egy virtuális gép (OSTICKETWEB) és a egy másik virtuális gép (OSTICKETMYSQL) fut a MySQL-adatbázishoz | Az alkalmazás belső alkalmazottai és külső ügyfelek esetében a problémák nyomon követésére ügyfélalkalmazások szolgáltatást használják.<br/><br/> A mintaalkalmazás letölthető [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuális architektúra


Íme az aktuális Contoso a helyszíni infrastruktúra bemutató ábra.

![Contoso-architektúra](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso a az városa New York a kelet-Egyesült Államok található egy fő adatközpont rendelkezik.
- Három további helyszíni ágakat Egyesült államokbeli városba rendelkeznek.
- A fő adatközpont fiber metro ethernet-kapcsolattal (500 MB/s) az internethez csatlakozik.
- Minden egyes fiókiroda helyi üzleti osztály kapcsolatok, térjen vissza a fő adatközpont IPSec VPN-alagutat az internetes csatlakozik. Ez lehetővé teszi, hogy a teljes hálózat véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpont teljes VMware-rel lesz virtualizálva. Két ESXi 6.5-ös virtualizációs gazdagépeket, a vCenter Server 6.5-ös által felügyelt rendelkeznek.
- Contoso az Active Directory identitáskezelési és a DNS-kiszolgálók a belső hálózaton.
- A tartományvezérlők VMware virtuális gépek futtatását az adatközpontban. A tartományvezérlők helyi ágat, fizikai kiszolgálókon futnak.





## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik az üzleti partnerek megértéséhez, az üzleti szeretne való áttérés érhet el:

- **Üzleti növekedés cím**: Contoso nő, és ennek eredményeképpen nincs a helyszíni rendszerek és infrastruktúra nyomás.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára.  Az üzleti igények informatikai gyorsan, és nem Hulladékmennyiség idő vagy költséget takaríthat meg, így gyorsabban továbbítása az ügyfelek igényei.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a versenyképes sikeres engedélyezése a módosításokat, képesnek kell lennie.  Azt nem a módon, vagy egy üzleti blocker válnak.
- **Méretezési csoport**: a vállalat növekedésével sikeres, a Contoso informatikai kell megadnia rendszerek, amelyek képesek a ugyanolyan ütemben nő.

## <a name="assessment-goals"></a>Értékelés célok

A Contoso felhőalapú csapat rendelkezik rögzített le azok áttelepítési felmérések célok:

- Az áttelepítés után az Azure-beli alkalmazások kell teljesítmény ugyanazokat a lehetőségeket, mint jelenleg helyszíni VMWare környezetben.  A felhőbe való áthelyezés nem jelenti azt, hogy teljesítményének kevésbé fontos.
- Contoso meg kell ismernie az Azure-követelményeknek, valamint azok üzemeltetési lehetőségek az Azure-ban alkalmazásaik és adatbázisok kompatibilitását.
- A Contoso adatbázis felügyeleti kerülendő, miután alkalmazások helyezte át a felhőbe.  
- A Contoso biztosítani szeretné tudni, nem csak az áttelepítési lehetőségek, de a felhőbe való áthelyezés után az infrastruktúra társított költségek is.

## <a name="assessment-tools"></a>Értékelési eszközök
Contoso az értékelés Microsoft-eszközöket használ. Ezek az eszközök igazodnak a saját céljainak és kell biztosítania az összes szükséges információt.

**Technológia** | **Leírás** | **Költségek**
--- | --- | ---
[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ezek segítségével DMA értékeli és észleli a kompatibilitási problémákat, amelyek hatással lehetnek a funkció az Azure-ban. A DMA értékeli a funkcióparitás SQL források és célok között, és a teljesítmény- és megbízhatóságbeli fejlesztéseket javasol. | Ez egy ingyenesen letölthető eszköz.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso Ez a szolgáltatás segítségével felmérheti a VMware virtuális gépeket. Felméri a gépek migrálásra való alkalmasságát, és méretezési, illetve költségbecsléseket ad az Azure-ban való futtatásra vonatkozóan.  | Nincs jelenleg használja ezt a szolgáltatást (Előfordulhat, hogy 2018-as) számítunk fel díjat.
[Szolgáltatástérkép](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Az Azure Migrate a Service Map használatával jeleníti meg a függőségeket a migrálni kívánt gépek között. |  A Service Map az Azure Log Analytics részét képezi. Jelenleg 180 napig díjmentesen használható.

Ebben a forgatókönyvben a Contoso letölti és futtatja a DMA értékeli az utazási alkalmazás helyszíni SQL Server-adatbázis. Ezek az Azure migrate és függőségi leképezés használatával értékeljük az alkalmazás-beli virtuális gépek Azure-ba való migrálás előtt.



## <a name="assessment-architecture"></a>Migrálásfelmérési architektúra


![Migrálásfelmérési architektúra](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso egy vállalati szervezet jelölő fiktív nevét.
- Contoso rendelkezik egy helyszíni adatközpont (**contoso-datacenter**), a helyszíni tartományvezérlővel (CONTOSODC1, CONTOSODC2).
- VMware virtuális gépek találhatók a VMware ESXI gazdagépek 6.5-ös verzióját futtatja. Gazdagépek: **contosohost1**, **contosohost2**
- A VMware-környezet kezeli a vCenter server 6.5-ös (**venter**, futó virtuális gépen.
- SmartHotel utazási alkalmazás:
    - Az alkalmazás két VMware virtuális gépek között többszintű **WEBVM** és **SQLVM**.
    - VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com**.
    - A virtuális gépek futnak a Windows Server 2008 R2 Datacenter SP1.
- A VMware-környezetet egy virtuális gépen futó vCenter Server (**vcenter.contoso.com**) felügyeli.
- A OSTicket szolgáltatás Desk alkalmazás:
    - Az alkalmazás két virtuális gépen, többszintű **OSTICKETWEB** és **OSTICKETMYSQL**.
    - A virtuális gépek futnak, az Ubuntu Linux Server 16.04-LTS.
    - A OSTICKETWEB virtuális gép fut, Apache-2 és a PHP 7.0-ban.
    - Virtuális gép OSTICKETMYSQL MySQL 5.7.22 fut.

![Architektúra](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Előfeltételek

Íme, mi Contoso (és) van szüksége az értékelés:

- Tulajdonosi vagy közreműködői hozzáférés az Azure-előfizetést, vagy az Azure-előfizetéshez egy erőforráscsoportban.
- Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú helyszíni vCenter-kiszolgáló.
- Egy csak olvasható fiók a vCenter-kiszolgálón, vagy a szükséges jogosultság egy ilyen fiók létrehozásához.
- Jogosultság egy virtuális gép létrehozására a vCenter-kiszolgálón egy .OVA-sablon használatával.
- Legalább egy 5.0-s vagy újabb verziójú ESXi-gazdagép.
- Legalább két helyszíni VMware virtuális gép, amelyek közül az egyik egy SQL Server-adatbázist futtat.
- Engedélyek az Azure Migrate-ügynökök telepítéséhez minden virtuális gépen.
- A virtuális gépeknek közvetlen internetkapcsolattal kell rendelkezniük.
        - Az internetes hozzáférést korlátozhatja [a szükséges URL-címekre](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Ha gépek, amelyeken nincs internetkapcsolat a [OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md) kell őket telepíteni.
- Az SQL Server-példányt futtató virtuális gép teljes tartományneve az adatbázis-értékeléshez.
- Az SQL Server virtuális gépen futó Windows tűzfalnak engedélyeznie kell a külső kapcsolatokat a 1433-as (alapértelmezett) TCP-porton, hogy a DMA csatlakozni tudjon.


## <a name="assessment-overview"></a>– Áttekintés

Az alábbiakban hogyan Contoso fog az értékelés tennie:


> [!div class="checklist"]
> * **1. lépés: Töltse le és telepítse a DMA**: DMA előkészítése a helyszíni SQL Server-adatbázis értékelésére.
> * **2. lépés: Az adatbázist a DMA értékeli**: az adatbázis-kiértékelés futtatása és elemzése.
> * **3. lépés: Felkészülés a virtuális gépek az Azure Migrate**: állítsa be a helyszíni fiókok és értékelésére VMware beállításait.
> * **4. lépés: Az Azure Migrate a helyszíni virtuális gépek felderítése**: egy Azure Migrate gyűjtő virtuális gép létrehozása. Ezután, futtassa a gyűjtő virtuális gépek felderítéséhez az értékeléshez.
> * **5. lépés: Felkészülés a függőségelemzésre az Azure Migrate**: a virtuális gépek telepítése az Azure Migrate-ügynökök, hogy a virtuális gépek közötti függőségi leképezés is megtekinthetik.
> * **6. lépés: A virtuális gépek, az Azure Migrate értékelése**: Ellenőrizze a függőségeket, a virtuális gépek csoportosítása és az értékelés futtatása. Az értékelés után készen áll, akkor a migrálás előkészítése elemezhetők.


## <a name="step-1-download-and-install-the-dma"></a>1. lépés: Töltse le és telepítse a DMA

1. Contoso letölti a DMA a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Az asszisztens, amely képes kapcsolódni az SQL-példány bármely gépen telepíthető. Nem szükséges az SQL Server-gépen futtatnia.
    - Az SQL Server-gazdagépen, nem futtatható.
2. Futtatás a letöltött telepítőfájlra (DownloadMigrationAssistant.msi), hogy elindítsa a telepítést.
3. Az a **Befejezés** lapon válasszon **indítsa el a Microsoft Data Migration Assistant** a varázsló befejezése előtt.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>2. lépés: Futtassa, és a SmartHotel az adatbázis-értékelés elemzése

Most már a Contoso értékelését azok a helyszíni SQL Server SmartHotel alkalmazás futtatható.

1. A Database Migration Assistant, a kattintson **új**válassza **Assessment**, és nevezze el az értékelés project - **SmartHotel**.
2. Akkor válassza ki a **forráskiszolgáló típusa** , **SQL Server Azure virtuális gépeken**.

    ![Forrás kiválasztása](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      A DMA jelenleg nem támogatja a felügyelt SQL-példányokra való migrálások értékelését. Áthidaló megoldásként Contoso használ az SQL Server Azure virtuális gépen feltételezett célként az értékelés.

3. A **cél verziójának kiválasztása**, azok az SQL Server 2017-ben válassza ki a célverzió. Válassza ezt, mert a felügyelt SQL-példány által használt verziója van szükségük.
4. Kompatibilitási és az új funkciók kapcsolatos információk felderítésére válassza ki:
    - **Kompatibilitási problémák** vegye figyelembe, amely meghiúsulhat a migrálás, vagy egy kisebb beállítások áttelepítése előtt igénylő módosításokat. Szinkronban tartja szerez tudomást jelenleg használatban lévő szolgáltatások, már elavult. A problémák kompatibilitási szint szerint vannak rendezve.
    - **Új szolgáltatásokra vonatkozó javaslat** megjegyzések az áttelepítés után az adatbázis használható SQL Server célplatformon új funkciói. Ezek teljesítmény, biztonság és tárterület alapján vannak rendszerezve.

    ![Cél kiválasztása](./media/contoso-migration-assessment/dma-assessment-2.png)

2. A **Kapcsolódás kiszolgálóhoz**, akkor adja meg a virtuális gép nevét az adatbázis és a hitelesítő adatokkal futtatja az eléréséhez. Szükségük van ahhoz, hogy **megbízható kiszolgálói tanúsítvány** , hogy az SQL Server is kapnak. Ezután kattintanak **Connect**.

    ![Cél kiválasztása](./media/contoso-migration-assessment/dma-assessment-3.png)

3. A **forrás hozzáadása**, adnak hozzá az adatbázis felmérése, és kattintson a kívánt **tovább** és a felmérés elindításához.
4. Az értékelés jön létre.

    ![Értékelés létrehozása](./media/contoso-migration-assessment/dma-assessment-4.png)

5. A **felülvizsgálati eredmények**, az értékelések eredményeinek megjelenik.


### <a name="analyze-the-database-assessment"></a>Az adatbázis-értékelés elemzése

Eredmények jelennek meg, amint azok elérhetők. Ha ezek a problémák megoldása gombra kell kattintani **indítsa újra a felmérés** kattintva futtassa újra az értékelést.

1. Az a **kompatibilitási problémák** jelentésben, ellenőrizze az egyes kompatibilitási szinten problémákat. A kompatibilitási szintek a következőképpen feleltethetők meg az SQL Server-verzióknak:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Kompatibilitási problémák](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Az a **Szolgáltatásjavaslatok** jelentésében a Contoso megtekintheti a teljesítmény, biztonság és tárolási szolgáltatások, amelyek az értékelés szerint az áttelepítés után. Számos különböző funkció használata akkor javasolt, beleértve az In-Memory OLTP és Oszloptár, Stretch Database, Always Encrypted, a dinamikus Adatmaszkolás és transzparens adattitkosítási (TDE).

    ![Szolgáltatási javaslatok](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Azt javasoljuk, hogy Contoso [lehetővé teszi a TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) az összes SQL Server adatbázisok, és ez fontos még több felhőbeli adatbázisok esetén. TDE csak engedélyezni kell az áttelepítés után. Ha TDE már engedélyezve van, szüksége lesz a tanúsítvánnyal vagy aszimmetrikus kulccsal áthelyezése a célkiszolgáló a master adatbázishoz. [További információk](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Exportálhatja az értékelés JSON vagy CSV-formátumban.

Vegye figyelembe, hogy ha egy nagyobb méretezés értékelés is:

- Egyidejűleg futtathatók több értékelést, és az értékelések állapotának megtekintéséhez nyissa meg a **összes értékelés** lapot.
- [SQL Server-adatbázis értékelések egyesíthetők](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [A Power bi-jelentés értékelések egyesíthetők](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>3. lépés: Felkészülés a virtuális gépek az Azure Migrate

Contoso van szüksége, hozzon létre egy VMware-fiókot, amelynek használatával az Azure Migrate automatikusan értékelése a virtuális gépek felderítéséhez, a virtuális gép létrehozásához szükséges engedélyek ellenőrzése jegyezze fel a portot, amelyet meg kell nyitni, és állítsa be a statisztikai beállítások szintjét.

### <a name="set-up-a-vmware-account"></a>VMware-fiók beállítása

 Virtuális gépek felderítésének van szükség egy csak olvasható fiókra a Vcenterben, az alábbi tulajdonságokkal:

- Felhasználó típusa: Egy legalább olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

### <a name="verify-permissions-to-create-a-vm"></a>A virtuális gép létrehozásához szükséges engedélyek ellenőrzése

Contoso ellenőrizze, hogy jogosult a virtuális gép létrehozása a fájl importálásával. OVA formátumot. [További információk](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Portok ellenőrzése

A Contoso értékelés függőségi leképezés használ. Ezt a szolgáltatást telepítenie kell egy ügynököt az értékelni kívánt virtuális gépeket. Az ügynök kell lennie, hogy csatlakozni tudjon az Azure-hoz a 443-as TCP-port az egyes virtuális Gépeken. [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) a kapcsolódási követelményekről.


### <a name="set-statistics-settings"></a>Statisztikai beállítások megadása

Mielőtt azok az üzembe helyezés, a Contoso be kell állítania a vCenter Server statisztikai beállításait a 3. szintre. Vegye figyelembe:

- A szint beállítása után várjon legalább egy napot az értékelés futtatása előtt kell. Máskülönben előfordulhat, hogy az értékelés nem a vártnak megfelelően fog működni.
- Ha a szint nagyobb mint 3, az értékelés működik, de:
    - A rendszer nem gyűjti majd a lemezek és a hálózat teljesítményadatait.
    - A tárolás tekintetében az Azure Migrate egy standard lemez létrehozását javasolja az Azure-ban, amelynek a mérete megegyezik a helyszíni lemezével.
    - A hálózatkezelés tekintetében a rendszer azt javasolja, hogy minden helyszíni hálózati adapterhez hozzon létre egy Azure-beli hálózati adaptert.
    - A számítási kapacitás tekintetében az Azure Migrate áttekinti a VM-magokat és a memória méretét, és egy azonos konfigurációjú Azure-beli virtuális gép létrehozását javasolja. Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.
- [További információ](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) a 3. szintű méretezésről.

Ezek a szint a következő megadásához:

1. A vSphere webes ügyfélben akkor nyissa meg a vCenter server-példányt.
2. A **kezelés** > **beállítások** > **általános**, kattintanak **szerkesztése**.
3. A **statisztika**, azok értékre a statisztikai szint beállítását **3. szint**.

    ![vCenter statisztikai szintje](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>4. lépés: Virtuális gépek felderítése

Virtuális gépek felderítéséhez, a Contoso Azure Migrate-projektet hoz létre. Töltse le és telepítse a gyűjtő virtuális gép, és futtassa a gyűjtő a helyszíni virtuális gépek felderítéséhez.

### <a name="create-a-project"></a>Projekt létrehozása

1. Az a [az Azure portal](https://portal.azure.com), keresnek a **Azure Migrate**, és hozzon létre egy projektet (ContosoMigration).
2. Adja meg a projekt nevét, az Azure-előfizetéssel, és a egy új Azure erőforráscsoport létrehozása **ContosoFailoverRG**. Vegye figyelembe:

    - Azure Migrate-projektet csak az USA középnyugati régiójában és keleti régiójában lehet létrehozni.
    - Bármilyen célhelyre tervezhet migrálást.
    - A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtőberendezés beállításához, a Contoso letölti egy. OVA sablont, és importálja azt a virtuális gép létrehozása a helyszíni vCenter-kiszolgálóhoz.

1. Az Azure Migrate-projektben > **bevezetés** > **felderítés és értékelés** > **gépek felderítése**, letöltik a. Sablon OVA-fájl.
2. Azok az másolja a projekt Azonosítóját és kulcsát. A gyűjtő konfigurálásához szükséges.

    ![Az .ova-fájl letöltése](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A virtuális gép üzembe helyezése előtt Contoso ellenőrzi, hogy a. Fájl (OVA) biztonságos.

1. A számítógépen, amelyre a fájlt letöltötte azokat egy rendszergazdai parancsablakot nyissa meg.
2. A következő parancsot az OVA létrehozni a kivonatot futnak:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell az ezeket a beállításokat (1.0.9.12 verzió)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>A gyűjtőberendezés létrehozása

Most Contoso importálni a letöltött fájlt a vCenter-kiszolgáló és a konfigurációs kiszolgáló virtuális gép üzembe helyezéséhez.

1. A vSphere Client-konzolon kattintson **fájl** > **OVF-sablon telepítése**.

    ![Az OVF telepítése](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Az OVF-sablon üzembe helyezése varázsló > **forrás**, akkor adja meg a helyét a. OVA-fájl.
3. A **név és hely**, akkor adja meg egy rövid nevet a gyűjtő virtuális Gépen, és a készlet helyét, amelyben a virtuális gép fog üzemeltetni. Akkor is megadhatja a gazdagép vagy fürt, amelyen a gyűjtő berendezés futni fog.
5. A **tárolási**, akkor adja meg a tárolási hely és a **lemezformátum**, és hogyan szeretne kiosztani a tárhelyet.
7. A **Hálózatleképezés**, akkor adja meg a hálózat, amelyhez a gyűjtő virtuális gép csatlakozni fog. A hálózatnak internetkapcsolattal kell rendelkeznie a metaadatok az Azure-ba küldéséhez.
8. Tekintse át a beállításokat, és válassza ki **üzembe helyezés után bekapcsolási**> **Befejezés**. A berendezés létrehozása után megjelenik egy üzenet, amely visszaigazolja a sikeres létrehozást.

### <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Most, futtassa a gyűjtő virtuális gépek felderítéséhez. Vegye figyelembe, hogy az adatgyűjtő jelenleg csak támogatja "Angol (Egyesült Államok)", az operációs rendszer és az adatgyűjtő felület nyelveként.

1. A vSphere Client-konzol > **konzol megnyitása**, nyelvi, időzóna és a gyűjtő virtuális gép jelszóbeállításait megadása.
2. Az asztalon kattintson a **gyűjtő futtatása** parancsikon.

    ![Gyűjtő parancsikonja](./media/contoso-migration-assessment/collector-shortcut.png)

4. Az Azure Migrate Collector > **Előfeltételek beállítása**, fogadja el a licencfeltételeket, és a külső adatokat olvasni.
5. A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel, hogy az idő szinkronizálva van-e, és, hogy fut-e a gyűjtőszolgáltatás (Ez alapértelmezés szerint telepítve van a virtuális gépen). VMWare powercli-t is telepíti.

    > [!NOTE]
    > Feltételezzük, hogy a virtuális gép közvetlen, proxy nélkül internet-hozzáféréssel rendelkezik.

    ![Előfeltételek ellenőrzése](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. A **adja meg a vCenter Server adatait**, adja meg a nevét (FQDN) vagy a vCenter-kiszolgáló IP-címét és a csak olvasható hitelesítő adatokat használja a felderítéshez.
7. Akkor válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak a megadott hatókörön belül deríti fel a virtuális gépeket. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat 1500-nál több virtuális gépet.

    ![Csatlakozás a vCenterhez](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. A **adja meg a migrálási projekt**, adja meg, kattintson az Azure Migrate-projekt Azonosítóját és a Portalról másolt kulcsot. Szerezheti be őket ismét a projekt **áttekintése** lap > **gépek felderítése**.  

    ![Csatlakozás az Azure szolgáltatáshoz](./media/contoso-migration-assessment/collector-connect-azure.png)

7. A **adatgyűjtési folyamat megtekintése** Contoso figyelheti a felderítést, és ellenőrizze, hogy a virtuális gépekről gyűjtött metaadatok hatókörében van. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.

    ![Gyűjtés folyamatban](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A gyűjtés befejezése után a Contoso ellenőrzi, hogy a virtuális gépek megjelennek-e a portálon.

1. Az Azure Migrate-projektben > **kezelés** > **gépek**, akkor ellenőrizze, hogy a felderíteni kívánt virtuális gépek jelennek meg.

    ![Felderített gépek](./media/contoso-migration-assessment/discovery-complete.png)

3. Vegye figyelembe, hogy az Azure Migrate-ügynök jelenleg nincs telepítve a gépeken. Contoso cégnek szüksége van, ezek a megtekintéséhez függőségek telepítéséhez.

    ![Felderített gépek](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>5. lépés: Felkészülés a függőségelemzésre

Contoso elérni kívánt virtuális gépek közötti függőségek megtekintéséhez, töltse le és telepítse ügynökök az alkalmazás virtuális gépeit. Contoso pedig az alkalmazások, Windows és a Linux rendszerű virtuális gépek azért teszi ezt.

### <a name="take-a-snapshot"></a>Pillanatkép készítése

Virtuális gép másolatának vezetnek módosítása, által pillanatképének elkészítése előtt az ügynökök telepítése előtt.

![Gép pillanatképe](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

1. A a **gépek** lapon, válassza ki a gépet, majd **telepítés szükséges** a a **függőségek** oszlop.
2. Az a **gépek felderítése** lapon, tegye a következőket:
    - Töltse le az MMA és a függőségi ügynököt Windows virtuális gépek
    - Töltse le az MMA és a függőségi ügynököt minden egyes Linux virtuális gép
3. Most, másolja, a munkaterület Azonosítójára és kulcsára. Van szükségük ezek az MMA telepítése során.

    ![Ügynök letöltése](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Az ügynökök telepítése Windows virtuális gépeken

A telepítés az egyes virtuális Gépeken futnak.

#### <a name="install-the-mma-on-windows-vms"></a>Windows virtuális gépeken az MMA telepítése

1. Ezek kattintson duplán a letöltött ügynökre.
2. A **célmappa**, azok megtartani az alapértelmezett telepítési mappa > **tovább**.
2. A **ügynök telepítésének beállításai**, kiválasztják **az ügynök csatlakoztatása az Azure Log Analyticshez** > **tovább**.

    ![Az MMA telepítése](./media/contoso-migration-assessment/mma-install.png)

5. A **Azure Log Analytics**, akkor illessze be a munkaterület Azonosítóját és kulcsát, a portálról kimásolt.

    ![Az MMA telepítése](./media/contoso-migration-assessment/mma-install2.png)

6. A **telepítésre kész**, akkor most már telepítheti az MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Telepítse a függőségi ügynököt Windows virtuális gépek

1. Ezek kattintson duplán a letöltött függőségi ügynökre.
2. Fogadja el a licencfeltételeket, és várjon, amíg a telepítés befejeződik.

    ![Függőségi ügynök](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Az ügynök telepítéséhez a Linux rendszerű virtuális gépek

A telepítés az egyes virtuális Gépeken futnak.

#### <a name="install-the-mma-on-linux-vms"></a>Az MMA telepítése Linux rendszerű virtuális gépeken

1. Azok minden virtuális gép használatával telepítse a python-ctypes kódtár: **sudo apt-get paranccsal telepítse a python-ctypeslib**.
2. A parancs az MMA-ügynök telepítése a legfelső szintű kell futnak.  Válhat a legfelső szintű futtassa a következő parancsot, és adja meg a gyökér szintű jelszó: **sudo -i**.
3. Most már az MMA-ügynök telepítése.
    - Helyezze be a parancsot a megfelelő munkaterület Azonosítójára és kulcsára.
    - A 64 bites parancsai.
    - A **munkaterület-Azonosítót** és **elsődleges kulcs** az OMS-portálon belül található > **beállítások**, a a **csatlakoztatott források** fülre.
    - Futtassa az alábbi parancsokat az OMS-ügynök letöltése, az ellenőrzőösszegekkel és telepítés/előkészítése az ügynök ellenőrzése.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>A függőségi ügynök telepítése Linux rendszerű virtuális gépeken

Az MMA telepítése után a Contoso telepítheti a függőségi ügynököt a Linuxos virtuális gépeken.

1. A függőségi ügynök Linux rendszerű számítógépek InstallDependencyAgent Linux64.bin, azt a héjparancsfájlt, egy önkicsomagoló bináris segítségével telepítve van. Azok a fájl használatával futtassa sh, vagy adja hozzá végrehajtási engedélyeket magát a fájlt.

2. Gyökér szintű a Linux függőségi ügynök telepítése:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>6. lépés: Futtassa, és a Virtuálisgép-kiértékelés elemzése

Contoso mostantól ellenőrizze a gépek függőségeit, és hozzon létre egy csoportot. Ezután, futtassa az értékelést a csoport számára.

### <a name="verify-dependencies-and-create-a-group"></a>Ellenőrizze a függőségeket, és hozzon létre egy csoportot


1. A gépek elemzéséhez, kattintson **függőségek megtekintése**.

    ![Gépek függőségeinek megtekintése](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Az SQLVM esetében a függőségi térkép a következő részleteket tartalmazza:

    - Az SQLVM-en futó, aktív hálózati kapcsolatokkal rendelkező folyamatcsoportok/folyamatok a megadott időszakban (alapértelmezés szerint egy óra)
    - Az összes függő gép bejövő (ügyfél) és kimenő (kiszolgálói) TCP-kapcsolatai.
    - A telepített Azure Migrate-ügynökkel rendelkező függő gépek külön mezőkben jelennek meg
    - A telepített ügynökkel nem rendelkező gépek esetében a port- és IP-címadatok jelennek meg.

3. A telepített ügynökkel (WEBVM) rendelkező gépek esetében, kattintson a gép mezőjére a további információk, beleértve a teljes tartománynév, az operációs rendszer és a MAC-cím megtekintéséhez.

    ![Csoportos függőségek megtekintése](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Most, válassza ki a virtuális gépeket (SQLVM és WEBVM) a csoportba való felvételéhez.  Azok is használja a CTRL + kattintással jelölje ki a több virtuális gép.
5. Kattintanak **csoport létrehozása**, és adjon meg egy nevet (smarthotelapp).

> [!NOTE]
    > A függőségek részletesebb megtekintéséhez terjessze ki az időtartományt. Megadhat egy adott időtartamot vagy kezdő és befejező dátumokat is.


### <a name="run-an-assessment"></a>Értékelés futtatása


1. Az a **csoportok** lapon nyissa meg a csoportot (smarthotelapp), és kattintson a **értékelés létrehozása**.

    ![Értékelés létrehozása](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Az értékelés a **Kezelés** > **Értékelések** lapon jelenik meg.

Contoso használja az alapértelmezett értékelési beállításokat, de testre is szabhatók. [További információk](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>A virtuálisgép-kiértékelés elemzése

Egy Azure Migrate-értékelések információt kompatibilitásról a helyszíni virtuális gépek az Azure-hoz, javasolt Azure virtuális gép megfelelő méretezéséhez, valamint a becsült havi Azure-költségekről.

![Értékelési jelentés](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

![Értékelés megjelenítése](./media/contoso-migration-assessment/assessment-display.png)

Az értékelés kap egy megbízhatósági minősítést amely 1 csillagtól 5 csillagig terjed (1 csillag a legalacsonyabb, 5 csillag a legmagasabb).
- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- A minősítés segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- Megbízhatósági minősítés akkor hasznos, ha végez *teljesítményalapú* , az Azure Migrate lehet, hogy nincs elegendő adatpont ehhez használat a használatalapú méretezéshez. A *helyszíni méretezésnél* a megbízhatósági minősítés mindig 5 csillagos, mert az Azure Migrate rendelkezik a virtuális gép méretezéséhez szükséges összes adatponttal.
- Az elérhető adatpontok százalékától függően van megadva a megbízhatósági minősítés:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

#### <a name="verify-readiness"></a>Felkészültség ellenőrzése

![Készenléti állapot értékelése](./media/contoso-migration-assessment/azure-readiness.png)  

Az értékelési jelentés egy táblában összefoglalva jeleníti meg az információkat. Vegye figyelembe, hogy a teljesítményalapú méretezés megjelenítéséhez az Azure Migrate-nek szüksége van a következő adatokra. Ha ezt az információt nem lehet összegyűjteni, az értékelés pontatlan lehet.

- A processzor és a memória kihasználtsági adatai.
- A virtuális géphez csatlakoztatott összes lemez olvasási/írási IOPS-értéke és adatátviteli teljesítménye.
- A virtuális géphez csatlakoztatott összes hálózati adapter bejövő és kimenő hálózati forgalmának adatai.


**Beállítás** | **Jelzés** | **Részletek**
--- | --- | ---
**Azure-beli virtuális gép felkészültsége** | Azt jelzi, hogy a virtuális gép készen áll-e a migrálásra | Lehetséges állapotok:</br><br/>- Készen áll az Azure-beli migrálásra<br/><br/>- Feltételekkel kész <br/><br/>- Nem áll készen az Azure-beli migrálásra<br/><br/>- A felkészültség ismeretlen<br/><br/> Ha a virtuális gép nem áll készen, bemutatunk néhány lehetséges javítási intézkedést.
**Azure-beli virtuális gép mérete** | A készen álló virtuális gépek esetében javaslatot teszünk egy Azure-beli virtuálisgép-méretre. | A méretezési javaslat az értékelési tulajdonságoktól függ:<br/><br/>- Ha teljesítményalapú méretezést használt, a méretezés a virtuális gépek teljesítményelőzményeit veszi figyelembe.<br/><br/>- Ha „helyszíni méretezést” használt, a méretezés a helyszíni virtuális gép méretétől függ, és a kihasználtsági adatokat nem használja fel a rendszer.
**Ajánlott eszköz** | Mivel az ügynökök a mi gépeinken futnak, az Azure Migrate megvizsgálja a gépen futó folyamatokat, és meghatározza, hogy a gép adatbázisgép-e.
**Virtuális gép adatai** | A jelentés a helyszíni virtuális gép beállításait mutatja, beleértve az operációs rendszer, a rendszerindítási típus, a lemez és a tárterület adatait.


#### <a name="review-monthly-cost-estimates"></a>Havi költségbecslések áttekintése

Ez a nézet a virtuális gépeknek az Azure-ban való futtatásával kapcsolatos összes számítási és tárolási költséget mutatja az egyes gépek adataival együtt.

![Készenléti állapot értékelése](./media/contoso-migration-assessment/azure-costs.png)

- A költségbecslések az egyes gépekre vonatkozó méretjavaslatokon alapulnak.
- A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek.


## <a name="clean-up-after-assessment"></a>Értékelés után tisztítása

- Az értékelés befejeződése után a Contoso megőrzi az Azure Migration a készülék jövőbeli értékelések.
- A virtuális gép VMware kikapcsolása. Akkor lesz indítsa el újra, amikor további virtuális gépek értékelik.
- Ezek a Contoso Migrálási projekt, megtarthatja az Azure-ban.  Jelenleg üzemel ContosoFailoverRG erőforráscsoportban, a keleti Azure régiójában.
-  A gyűjtő virtuális gép rendelkezik egy 180 napos próbaverzió licenccel. Ha ezt a korlátot lejár, akkor kell töltse le és állítsa be a gyűjtő újra.


## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben Contoso SmartHotel alkalmazás adatbázisában a DMA eszközzel, és a helyszíni virtuális gépek az Azure Migrate szolgáltatással kiértékeltük. Majd áttekintettük az értékeléseket, győződjön meg arról, készen áll a helyszíni erőforrásokhoz az Azure-ba való migrálásra.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozatban a következő cikkben Contoso annak SmartHotel app, az Azure-ban egy lift-and-shift-áttelepítés áthelyezi. Contoso áttelepíti az előtér WEBVM az alkalmazás használatával az Azure Site Recovery és az alkalmazás-adatbázis egy Azure SQL felügyelt példányra, a Database Migration Service segítségével. [Első lépések](contoso-migration-rehost-vm-sql-managed-instance.md) ezzel az üzembe helyezéssel.
