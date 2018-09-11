---
title: A Contoso a helyszíni alkalmazás újratárolása az Azure virtuális gépek és Azure SQL Database felügyelt példányába való migrálással |} A Microsoft Docs
description: Ismerje meg, hogyan a Contoso áthelyezi egy helyszíni alkalmazást az Azure virtuális gépekhez és Azure SQL Database felügyelt példány használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: 12baa21c8661012cd7ef96217724150a3d8c56f3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303428"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso áttelepítési: egy Azure virtuális gép és az SQL Database felügyelt példánya a helyszíni alkalmazások Újratárolása

Ebben a cikkben a Contoso áttelepíti a SmartHotel360 alkalmazás előtérbeli virtuális gép egy Azure virtuális géphez az Azure Site Recovery szolgáltatással. Contoso is áttelepíti az alkalmazás-adatbázis az Azure SQL Database felügyelt példányain.

> [!NOTE]
> Az Azure SQL Database felügyelt példánya jelenleg előzetes verzióban érhető el.

Ez a cikk egy sorozat része cikkeket, amely a fiktív Contoso hogyan a helyszíni erőforrásokkal áttelepíti a Microsoft Azure felhőbe dokumentumok egyike. A sorozat tartalmazza a háttér-információkat és a egy sorozat olyan áttelepítési infrastruktúra beállítása és futtatása a különböző típusú migrálások bemutató forgatókönyvek. Forgatókönyvek egyre összetettebbé válnak. Cikkek idővel felveszi az adatsorozat.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a sorozat használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md) | Contoso fut, a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso értékeli az alkalmazás virtuális gépek használatával a [Azure Migrate](migrate-overview.md) szolgáltatás. Contoso alkalmazás SQL Server-adatbázis értékelésére használatával [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése az Azure SQL Database felügyelt példányába használatával a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Ez a cikk
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Contoso Azure virtuális gépekre annak SmartHotel alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással. | Elérhető
[Cikk 6: Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazások Újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Contoso a Site Recovery használatával az Azure virtuális gépek Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződik. | Elérhető
[Cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure virtuális gépek Site Recovery használatával. Az áttelepítése az alkalmazás-adatbázis az Azure Database for MySQL-hez a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure web App alkalmazásban és az Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-webalkalmazás, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány. | Elérhető
[Cikk 10: Újrabontás egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-webalkalmazás több helyen is előfordul. A webalkalmazás folyamatos készregyártás a GitHub van integrálva. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a Team Foundation Server az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi migrálással, hogy az Azure-ban az Azure DevOps-szolgáltatásokkal. | Elérhető
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-ba, és ezután rearchitects az alkalmazást. Contoso rearchitects az alkalmazás webes réteg Windows tárolójaként, és az alkalmazás-adatbázis rearchitects Azure SQL Database használatával. | Elérhető
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service-ben, az Azure Functions, az Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető




Ebben a cikkben használt SmartHotel360 mintaalkalmazást letöltheti [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>A stratégiai

A Contoso informatikai vezetőségi szorosan együttműködik a vállalat üzleti partnerek megértéséhez, az üzleti szeretne való áttérés érhet el:

- **Üzleti növekedés cím**: Contoso nő. Ennek eredményeképpen nyomás növelte a vállalat a helyszíni rendszerek és infrastruktúra.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára. Az ügyfelek igényei kell gyors és a nem Hulladékmennyiség idő vagy költséget takaríthat meg, így a vállalat informatikai üzleti igények gyorsabban is készíthetnek.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a cég számára a globális gazdaság végrehajtott módosításokat a képesnek kell lennie. Contoso IT nem kell a területük vagy egy üzleti blocker válnak.
- **Méretezési csoport**: növekedésével a vállalat üzleti sikeresen a Contoso informatikai meg kell adnia rendszerek, amelyek ütemben növekedhet.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat azonosította a célok, az áttelepítés. A vállalat áttelepítési célokat használja a leginkább megfelelő áttelepítési módszer meghatározásához.

- Az áttelepítés után az alkalmazás az Azure-ban, amely az alkalmazás még ma a helyszíni VMWare-környezetet a Contoso azonos teljesítménybeli képességeinek kell rendelkeznie. A felhőbe való áthelyezés nem jelenti azt, hogy teljesítményének kevésbé fontos.
- Contoso nem szeretné az alkalmazás be. Az alkalmazás kritikus fontosságú és fontos üzleti, de a Contoso biztosítani szeretné egyszerűen, az alkalmazás a jelenlegi formájában áthelyezése a felhőbe.
- Adatbázis-felügyeleti feladatokat kerülendő, az alkalmazás való áttelepítése után.
- Contoso nem szeretné használni az Azure SQL Database az alkalmazás. Alternatívák a keresi.


## <a name="solution-design"></a>Megoldásterv

Után rögzíthet a célokat és követelményeket állapította meg, a Contoso tervez és egy üzembehelyezési megoldással áttekinti, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, amelyek a migrálás fog használni.

### <a name="current-architecture"></a>Aktuális architektúra 

- Contoso rendelkezik egy fő adatközpontok (**contoso-datacenter**). Az adatközpontban található a a város, New York-i keleti Egyesült Államokban található.
- Contoso három további helyszíni ágakat Egyesült államokbeli városba rendelkezik.
- A fő adatközpont csatlakozik az internetre egy fiber Metro Ethernet-kapcsolat (500 MB/s).
- Minden egyes fiókiroda az interneten helyileg kapcsolódik, térjen vissza a fő adatközpont IPsec VPN-alagutak rendelkező vállalati szintű kapcsolatokon keresztül. A telepítő lehetővé teszi, hogy a Contoso teljes véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpont teljes VMware-rel lesz virtualizálva. Contoso két vCenter Server 6.5-ös által kezelt ESXi 6.5-ös virtualizációs gazdagépeket tartalmaz.
- Contoso Active Directoryt identitáskezeléshez használ. Contoso DNS-kiszolgálókat használ, a belső hálózaton.
- Contoso rendelkezik egy helyszíni tartományvezérlővel (**contosodc1**).
- A tartományvezérlők VMware virtuális gépek futtathatók. A tartományvezérlők helyi ágat, fizikai kiszolgálókon futnak.
- A SmartHotel360 app többszintű két virtuális gép között (**WEBVM** és **SQLVM**) 6.5-ös verziójú VMware ESXi-gazdagépen található üzemelő (**contosohost1.contoso.com**). 
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**) egy virtuális gépen.

![Aktuális Contoso-architektúra](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>Javasolt architektúra

Ebben a forgatókönyvben a Contoso biztosítani szeretné a kétrétegű helyszíni utazási alkalmazást áttérnének az alábbiak szerint:

- Az alkalmazás-adatbázis migrálása (**SmartHotelDB**), egy Azure SQL Database felügyelt példányain.
- Az előtérbeli áttelepítése **WebVM** egy Azure virtuális géphez.
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek, ha az áttelepítés befejeződött.

![Forgatókönyv-architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>Adatbázis használata

A megoldástervezési folyamat részeként a Contoso volt az Azure SQL Database és az SQL Server-a felügyelt példány funkció összehasonlítását. Az alábbi szempontokat lerövidítették úgy dönt, hogy nyissa meg a felügyelt példány használatával.

- A felügyelt példány célja, hogy a helyszíni SQL Server legújabb majdnem 100 %-os kompatibilitást. A Microsoft javasolja a felügyelt példánynak a helyszíni SQL Server rendszert futtató ügyfelek számára vagy alkalmazások áttelepítése teljes körűen felügyelt szolgáltatás, amely minimális tervezési módosításaival, akik IaaS virtuális gépen.
- Contoso tervezi, hogy a helyszíni alkalmazások nagy számú át az iaas-ben. Ezek közül számos olyan ISV megadott. Contoso valósít meg, hogy felügyelt példány használatával biztosítja ezeket az alkalmazásokat, nem pedig az SQL Database, és előfordulhat, hogy nem támogatott az adatbázis-kompatibilitási.
- Contoso egyszerűen lift-and-shift migrálás felügyelt példányra segítségével teheti a teljesen automatizált Data Migration Service (DMS). E szolgáltatás helyben Contoso felhasználhatja azt a jövőbeli adatbázis-migrálási parancsait.
- SQL felügyelt példánya támogatja az SQL Server-ügynök, amely fontos kérdés a SmartHotel360 alkalmazás. Contoso cégnek szüksége van a kompatibilitási, egyéb módon meg kell újra kell terveznie a karbantartási tervek az alkalmazás által igényelt.
- Frissítési garanciával működő a Contoso exchange is a kedvezményes díjszabás a egy SQL Database felügyelt példányain használatával az Azure Hybrid Benefittel az SQL Server meglévő licenceit. Engedélyezheti, hogy a Contoso, akár 30 %-os megtakarítást a felügyelt példány.
- Felügyelt példány teljes szerepel a virtuális hálózathoz, így biztosít magas szintű elkülönítési és biztonsági Contoso-adatok. Contoso kérheti le a nyilvános felhő nyújtotta előnyök a nyilvános internetről elkülönített környezet megtartva.
- A felügyelt példány által támogatott mindig titkosítva, a dinamikus adatmaszkolás, a sorszintű biztonság és a fenyegetésészlelés például számos biztonsági funkció.


### <a name="solution-review"></a>Megoldás áttekintése

Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Szempontok** | **Részletek**
--- | ---
**Szakemberek számára** |  WEBVM átkerül az Azure-bA sem kell módosítani, így egyszerű a migrálás.<br/><br/> SQL felügyelt példánya a Contoso technikai követelmények és célok támogatja.<br/><br/> Felügyelt példány az SQL Server 2008 R2 távolabbi áthelyezésekor a jelenlegi üzemelő példány 100 %-os kompatibilitást biztosít.<br/><br/>  A frissítési garancia és az Azure Hybrid Benefit forSQL kiszolgáló és a Windows Server beruházási használhatnak.<br/><br/> A Database Migration Service további jövőbeli áttelepítések felhasználhatja azokat.<br/><br/> SQL felügyelt példánya a hibatűrést, amely a Contoso nem konfigurálja a szükséges rendelkezik beépített. Ez biztosítja, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | A WEBVM Windows Server 2008 R2 fut.  Ez az operációs rendszer támogatják az Azure-ban, már nem támogatott platformokon. [További információk](https://support.microsoft.com/en-us/help/956893).<br/><br/> A webes szint marad, a feladatátvétel csak WEBVM nyújt szolgáltatásokat a hibaérzékeny pont.<br/><br/> Az alkalmazás webes réteg virtuális gépként támogató, nem pedig egy felügyelt szolgáltatás, például az Azure App Service-ben való folytatáshoz contoso kell.<br/><br/> Az adatréteg számára felügyelt példány-kezelő nem feltétlenül a legjobb megoldás, ha a Contoso testre szeretne szabni az operációs rendszer vagy az adatbázis-kiszolgáló, vagy ha külső alkalmazások és az SQL Server futtatása szeretne. Az SQL Servert futtató IaaS virtuális gépen, ez rugalmasságot biztosítanak a sikerült. 

### <a name="migration-process"></a>Áttelepítési folyamat

Contoso telepítse át a webes és az adatok szintek a SmartHotel360 alkalmazás az Azure-bA; Ehhez hajtsa végre az alábbi lépéseket:

1. Contoso ugyanúgy kell hozzáadni az adott Azure-összetevőket ebben a forgatókönyvben számos már rendelkezik az Azure-infrastruktúra a helyen.
2. A Data Migration Service segítségével az adatréteg lesz migrálva. A Data Migration Service a Contoso-datacenter és az Azure között helyek közötti VPN-kapcsolat a helyszíni SQL Server rendszerű virtuális gép csatlakozik. Ezután a Data Migration Service telepít át az adatbázist.
3. A webes szint áttelepíthetők lift-and-shift-áttelepítés használatával a Site Recovery használatával. A folyamat magában foglalja a helyszíni VMware-környezet előkészítése, beállítása és a replikáció engedélyezése és feladatátvétele őket az Azure által a virtuális gépeket migrálja.

     ![Áttelepítési architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

Szolgáltatás | Leírás | Költségek
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | A Database Migration Service lehetővé teszi a zökkenőmentes áttelepítés adatbázis több forrásból származó, az Azure-beli adat-platformokra minimális állásidővel. | Ismerje meg [támogatott régiók](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) és [Database Migration Service díjszabása](https://azure.microsoft.com/pricing/details/database-migration/).
[Az Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Felügyelt példány egy felügyelt adatbázis-szolgáltatás, amely egy teljes körűen felügyelt SQL Server-példány az Azure-felhőben jelöli. Ugyanazt a kódot használja, mint a legújabb SQL Server Database Engine, és a legújabb funkciók, a teljesítménnyel kapcsolatos fejlesztések és a biztonsági javítások. | Egy SQL Database felügyelt példányain Azure-ban futó használata tekintetében a kapacitás alapján számítunk fel díjat. Tudjon meg többet [felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A Site Recovery szolgáltatás koordinálja és kezeli az áttelepítés és a vészhelyreállítás az Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-ba, során Azure Storage-díjat számítunk fel.  Az Azure virtuális gépek jönnek létre, és a költségekkel, amikor feladatátvételt hajt végre. Tudjon meg többet [Site Recovery-díjak és árképzési](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="prerequisites"></a>Előfeltételek

A Contoso és más felhasználók ebben a forgatókönyvben a következő előfeltételeknek kell megfelelnie:

Követelmények | Részletek
--- | ---
**Regisztrálja a felügyelt példány előzetes verzióban érhető el** | Ön regisztrálva legyenek az SQL Database felügyelt példányain korlátozott nyilvános előzetes verzióban érhető el. Azure-előfizetéssel kell [regisztráció](https://portal.azure.com#create/Microsoft.SQLManagedInstance). A regisztráció végrehajtásához, ezért ügyeljen arra, hogy a forgatókönyv megvalósításához megkezdése előtt regisztráljon néhány napot is igénybe vehet.
**Azure-előfizetés** | Érdemes már létrehozott egy előfizetést az értékelés az oktatóanyag-sorozatban az első cikkben végrehajtásakor. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem az előfizetés rendszergazdája, kell dolgozni a rendszergazdától tulajdonosi vagy közreműködői engedélyekkel.<br/><br/> Ha részletesebb engedélyek van szüksége, tekintse meg [Site Recovery-hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**A Site Recovery (helyszíni)** | A helyszíni vCenter Server-példány futhat egy 5.5-ös, 6.0-s vagy 6.5-ös verzió<br/><br/> Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú ESXi-gazdagép<br/><br/> Egy vagy több futtató VMware virtuális gépeket az ESXi-gazdagépen.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Támogatott [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurációja.
**Database Migration Service** | A Database Migration Service, szüksége lesz egy [kompatibilis a helyszíni VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Konfigurálhatja a helyszíni VPN-eszköznek kell lennie. Egy kívülre irányuló nyilvános IPv4-címmel kell rendelkeznie. A cím nem található a NAT-eszköz mögött.<br/><br/> Ellenőrizze, hogy hozzáférése van a helyszíni SQL Server-adatbázisát.<br/><br/> Windows tűzfal eléréséhez a forrás adatbázismotor képesnek kell lennie. Ismerje meg, hogyan [Windows tűzfal konfigurálása az access adatbázismotor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Ha a tűzfal elé adatbázis számítógépe, adja hozzá az adatbázis és a fájlokat a 445-ös port az SMB-n keresztül való hozzáférést engedélyező szabályokat.<br/><br/> A hitelesítő adatokat az SQL Server-példányhoz való kapcsolódáshoz használt, és amely a cél felügyelt példány a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie.<br/><br/> A hálózatra van szükség a helyszíni adatbázis, a Database Migration Service segítségével a forrás-adatbázis biztonsági mentése a megosztás.<br/><br/> Győződjön meg arról, hogy az a forrás SQL Server-példányt futtató szolgáltatásfiók írási engedéllyel rendelkezik a hálózati megosztáson.<br/><br/> Jegyezze fel a Windows-felhasználói és a jelszót, amely teljes vezérlési engedélyekkel rendelkezzen a hálózati megosztáson. A Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat a biztonsági mentési fájlok feltöltése az Azure Storage-tárolóba.<br/><br/> Az SQL Server Express telepítési folyamat beállítja a TCP/IP protokoll **letiltott** alapértelmezés szerint. Győződjön meg arról, hogy engedélyezve van.

## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogy a Contoso tervek az üzembe helyezés:

> [!div class="checklist"]
> * **1. lépés: Állítsa be egy SQL Database felügyelt példányain**: Contoso cégnek szüksége van egy előre létrehozott felügyelt példány, amelyre áttelepíti a helyszíni SQL Server-adatbázis.
> * **2. lépés: Készítse elő a Database Migration Service**: Contoso kell az adatbázis-migrálási szolgáltató regisztrálása, hozzon létre egy példányt, és majd a Database Migration Service-projekt létrehozása. Contoso is be kell állítania egy közös hozzáférésű jogosultságkód (SAS) egységes erőforrás-azonosító (URI) a Database Migration Service. Egy SAS URI-t a Contoso storage-fiókban lévő erőforrások delegált hozzáférést biztosít, így a Contoso korlátozott engedélyeket adhat a tárolási objektumra. Contoso beállít egy SAS URI-t, így a Database Migration Service hozzáférhet, amelyhez a szolgáltatás az SQL Server biztonsági mentési fájlokat tölt fel a tárfiók tárolója.
> * **3. lépés: Az Azure Site Recovery előkészítése**: Contoso létre kell hoznia egy tárfiókot a replikált adatok tárolásához a Site Recovery. Azt is létre kell hoznia egy Azure helyreállítási tárat.
> * **4. lépés: A Site Recovery a helyszíni VMware előkészítése**: Contoso készítse elő a virtuális gép felderítés és az ügynök telepítése Azure virtuális géphez való kapcsolódásra a feladatátvételt követően fiókokat.
> * **5. lépés: A gépek replikálása**: a replikáció beállítása a Contoso konfigurálni a Site Recovery forrás és cél környezetekben, állít be egy replikációs szabályzatot és elindítja a virtuális gépek replikálása az Azure Storage.
> * **6. lépés: Az adatbázis Migrálása a Database Migration Service segítségével**: Contoso áttelepíti az adatbázisban.
> * **7. lépés: A virtuális gépek áttelepítése a Site Recovery használatával**: Contoso fut a teszt feladatátvételt, hogy minden működik-e. Majd a Contoso teljes feladatátvételt az a virtuális gépek áttelepítése az Azure-ban futtatja.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>1. lépés: Készítse elő a egy SQL Database felügyelt példány

Állítsa be az Azure SQL Database felügyelt példányába, a Contoso cégnek szüksége van egy alhálózatot, amely megfelel a következő:

- Az alhálózaton kell kijelölnie. Üresnek kell lennie, és nem tartalmazhat bármilyen más felhőalapú szolgáltatás. Az alhálózat egy átjáró-alhálózatot nem lehet.
- A felügyelt példány létrehozása után Contoso adjon hozzá erőforrásokat az alhálózat.
- Az alhálózathoz társított hálózati biztonsági csoport nem rendelkezhet.
- Az alhálózat egy felhasználó által meghatározott útválasztás (UDR) útválasztási táblázatot kell rendelkeznie. A hozzárendelt csak útvonal lehet 0.0.0.0/0, következő ugrási internet. 
- Egyéni DNS nem kötelező: Ha egyéni DNS az Azure-beli virtuális hálózaton van megadva, az Azure rekurzív feloldók IP-címet (például a 168.63.129.16) hozzá kell adni a listához. Ismerje meg, hogyan [egyéni DNS konfigurálása a felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Az alhálózat nem lehet egy végpontot (storage- vagy SQL) társítva. A Szolgáltatásvégpontok a virtuális hálózat le kell tiltani.
- Az alhálózatnak legalább 16 IP-címet kell rendelkeznie. Ismerje meg, hogyan [méretezés a felügyelt példány alhálózatára](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- A Contoso hibrid környezetben egyéni DNS-beállítások szükségesek. Contoso DNS-beállítások egy vagy több, a vállalat az Azure DNS-kiszolgálók használatára konfigurálja. Tudjon meg többet [DNS testreszabási](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>A felügyelt példány egy virtuális hálózat beállítása

Contoso-rendszergazdák a következőképpen állítsa be a virtuális hálózatot: 

1. Azok az új virtuális hálózat létrehozása (**VNET-SQLMI-EU2**) az elsődleges régióban USA keleti RÉGIÓJA 2. Hozzáadja a virtuális hálózaton a **ContosoNetworkingRG** erőforráscsoportot.
2. Egy címterében 10.235.0.0/24 hozzá. Akkor győződjön meg arról, hogy a tartomány nem-e átfedésben a vállalat bármely más hálózatokhoz.
3. A hálózat két alhálózattal adnak hozzá:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Ez az alhálózat egy címtár csatlakoztatása a felügyelt példány szolgál.

    ![Felügyelt példány – virtuális hálózat létrehozása](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. Miután a virtuális hálózat és alhálózatok vannak telepítve, azokat hálózatok között is létesíthet a következő:

    - Partnerek **VNET-SQLMI-EUS2** a **VNET-HUB-EUS2** (a központi virtuális hálózaton esetében az USA keleti RÉGIÓJA 2).
    - Partnerek **VNET-SQLMI-EUS2** a **VNET-ÉLES-EUS2** (az éles hálózati környezetben).

    ![Hálózatok közötti társviszony](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Ezek egyéni DNS-beállítások megadása DNS először a Contoso Azure tartományvezérlők mutat. Az Azure DNS egy másodlagos. A Contoso Azure-tartományvezérlőket az alábbi helyeken találhatók:

    - Található a **ÉLES – DC-EUS2** alhálózatának, az USA keleti RÉGIÓJA 2 éles hálózati környezetben (**VNET-ÉLES-EUS2**)
    - **CONTOSODC3** cím: 10.245.42.4
    - **CONTOSODC4** cím: 10.245.42.5
    - Az Azure DNS-feloldási: 168.63.129.16

     ![Hálózati DNS-kiszolgálók](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*További segítségre van szüksége?*

- Áttekintheti a [SQL Database felügyelt példányain](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Ismerje meg, hogyan [virtuális hálózat létrehozása az SQL Database felügyelt példányain](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Ismerje meg, hogyan [társviszony-létesítés beállítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Ismerje meg, hogyan [Azure Active Directory DNS-beállításainak frissítése](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Útválasztó beállítása

A felügyelt példány kerül egy privát virtuális hálózatot. Contoso cégnek szüksége van egy útválasztási táblázatot, a virtuális hálózat az Azure felügyeleti szolgáltatással való kommunikációra. Ha a virtuális hálózat nem tud kommunikálni az azt kezelő szolgáltatás, a virtuális hálózat elérhetetlenné válik.

Contoso úgy véli, hogy ezek a tényezők:

- Az útvonaltábla adja meg, hogyan a felügyelt példány által küldött csomagokat kell átirányítani a virtuális hálózathoz a szabályok (útvonal) tartalmazza.
- Az útvonaltábla társítva, amelyben a felügyelt példányok üzembe helyezése alhálózatokat. Minden csomagot, hogy egy alhálózatot a társított útvonaltábla alapján kezeli.
- Egy alhálózat csak egy útvonaltábla társítható.
- Nincsenek nem vonatkozik külön díj útvonaltáblák létrehozásához a Microsoft Azure-ban.

 Útválasztási Contoso beállítása a rendszergazdák tegye a következőket:

1. Azok az udr-t (útvonal) tábla létrehozása a **ContosoNetworkingRG** erőforráscsoportot.

    ![Útválasztási táblázat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Után az útvonaltábla a felügyelt példány követelményeket, ahhoz, hogy (**MIRouteTable**) van üzembe helyezve, adnak hozzá egy útvonalat a 0.0.0.0/0 címelőtaggal rendelkező. A **következő ugrás típusa** beállítás **Internet**.

    ![Útválasztási táblázat előtagot](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. az útvonaltáblában a társítják a **SQLMI-DB-EUS2** alhálózat (az a **VNET-SQLMI-EUS2** hálózati). 

    ![Útválasztási táblázat alhálózat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*További segítségre van szüksége?*

Ismerje meg, hogyan [útvonalak beállítása a felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

Contoso-rendszergazdák mostantól építhető ki egy SQL Database felügyelt példánya:

1. A felügyelt példány szolgál egy üzleti alkalmazás, mert azok a vállalat elsődleges USA keleti RÉGIÓJA 2 régióban a felügyelt példány üzembe helyezése. Adnak hozzá a felügyelt példány az **ContosoRG** erőforráscsoportot.
2. Akkor válassza ki, egy árképzési szint, méretű számítási és tárolási példány. Tudjon meg többet [felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. A felügyelt példány üzembe helyezése után két új erőforrások megjelennek a **ContosoRG** erőforráscsoportot:

    - A case Contoso virtuális fürtöt több felügyelt példánnyal rendelkezik.
    - A felügyelt példány az SQL Server-adatbázist. 

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*További segítségre van szüksége?*

Ismerje meg, hogyan [felügyelt példány üzembe helyezése](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>2. lépés: Készítse elő a Database Migration Service

Készítse elő a Database Migration Service, a Contoso rendszergazdák kell elvégeznie néhány dolgot:

- A Database Migration Service-szolgáltató regisztrálása az Azure-ban.
- Adja meg a Database Migration Service hozzáférést az Azure Storage-fel a biztonságimásolat-fájlokat, amelyek adatbázis áttelepítése. A hozzáférés biztosításához az Azure Storage, az Azure Blob storage-tárolókat hozzon létre. Azok a hoznak létre egy SAS URI-azonosítóját a Blob storage-tárolóba. 
- Database Migration Service-projekt létrehozása.

Ezt követően, végezze el az alábbi lépéseket:

1. Az adatbázis-migrálási szolgáltató az előfizetéshez tartozó regisztrálják.
    ![Database Migration Service - regisztráció](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Akkor hozzon létre egy Blob storage-tárolóba. Contoso hoz létre egy SAS URI-t, hogy a Database Migration Service hozzá tud férni.

    ![Database Migration Service – hozzon létre egy SAS URI-t](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. A Database Migration Service-példány létrehozása azokat. 

    ![Database Migration Service - példány létrehozása](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Ezek elhelyezni a Database Migration Service-példány az a **ÉLES – DC-EUS2** alhálózatának a **VNET-ÉLES – DC-EUS2** virtuális hálózat.
    - A Database Migration Service ide kerül, mivel a szolgáltatás a virtuális hálózatban, amely hozzáférhet a helyszíni SQL Server virtuális gép egy VPN-átjárót kell lennie.
    - A **VNET-ÉLES-EUS2** társviszonyban áll a **VNET-HUB-EUS2** és a távoli átjárók használata engedélyezett. A **távoli átjárók használata** beállítással biztosíthatja, hogy a Database Migration Service kommunikálhatnak-e szükség szerint.

        ![Database Migration Service – a hálózat konfigurálása](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*További segítségre van szüksége?*

- Ismerje meg, hogyan [állítsa be a Database Migration Service](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Ismerje meg, hogyan [hozhat létre és használhat SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>3. lépés: A Site Recovery szolgáltatással az Azure előkészítése

Több Azure elemek szükségesek a Contoso beállítása a Site Recovery a webes szint virtuális gép migrálása (**WEBMV**):

- Egy virtuális hálózatot, amelyben az erőforrásokat átvevő találhatók.
- Egy tárfiókot a replikált adatok tárolásához. 
- Az Azure Recovery Services-tárolóba.

A Site Recovery az alábbiak szerint állíthatja a Contoso rendszergazdák:

1. Mivel a virtuális gép egy web front end a SmartHotel360 alkalmazásba, a Contoso átadja a feladatokat a meglévő éles hálózati környezetben a virtuális gép (**VNET-ÉLES-EUS2**) és az alhálózati (**ÉLES-FE-EUS2**). A hálózatot és alhálózatot az elsődleges USA keleti RÉGIÓJA 2 régióban találhatók. A Contoso a hálózat beállítása során azt [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md).
2. Ezek a storage-fiók létrehozása (**contosovmsacc20180528**). Contoso egy általános célú fiókot használja. Contoso standard storage és a helyileg redundáns tárolás replikációs választja ki.

    ![Site Recovery – tárfiók létrehozása](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. A helyen lévő hálózati és tárolási fiókkal, a tároló létrehozása (**ContosoMigrationVault**). Contoso helyezi a tároló a **ContosoFailoverRG** erőforráscsoport az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Hozzon létre a Recovery Services - tároló](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*További segítségre van szüksége?*

Ismerje meg, hogyan [állítsa be az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>4. lépés: A Site Recovery a helyszíni VMware előkészítése

A Site Recovery VMware előkészítése, a Contoso rendszergazdák ezeket a feladatokat kell elvégeznie:

- Készíteni egy fiókot a vCenter Server-példány vagy a vSphere ESXi-gazdagépen. A fiók automatizálja a virtuális gépek felderítésének.
- Készítsen elő egy fiókot, amely lehetővé teszi, hogy a mobilitási szolgáltatás automatikus telepítéséhez, amely a Contoso biztosítani szeretné replikálni a VMware virtuális gépeken.
- Készítse elő a helyszíni virtuális gépek a feladatátvételt követően felálló Azure virtuális gépek csatlakozni.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. Legalább egy csak olvasható fiókra szükség.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Contoso cégnek szüksége van egy műveletek, például a létrehozása és a lemezek eltávolítása, és ne tudják bekapcsolni a virtuális gépeket futtató fiókot.

Contoso rendszergazdák; Ehhez hajtsa végre ezeket a feladatokat a fiók beállítása:

1. Létrehoz egy szerepkört a vCenter-szinten.
2. Szerepkörhöz rendeli hozzá a szükséges engedélyekkel.

*További segítségre van szüksége?*

Ismerje meg, hogyan [hozzon létre és rendelhet egy szerepkört automatikus felderítéshez](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítése

A mobilitási szolgáltatást, amely a Contoso biztosítani szeretné replikálni a virtuális gépen telepítve kell lennie. Contoso úgy véli, hogy ezek a tényezők a mobilitási szolgáltatásról:

- A Site Recovery felhasználóival, márpedig az automatikus ügyfélleküldéses telepítés összetevő Contoso lehetővé teszi, hogy a virtuális gép replikálását.
- Az automatikus ügyfélleküldéses telepítéshez, a Contoso elő kell készítenie a Site Recovery eléri a virtuális Gépet használó fiókból.
- Ez a fiók van megadva, ha replikációs úgy van konfigurálva, az Azure-konzolon.
- Contoso tartomány vagy helyi fiók, amely engedéllyel rendelkezik a virtuális Gépre telepíteni kell rendelkeznie.

*További segítségre van szüksége*

Ismerje meg, hogyan [hozzon létre egy fiókot a mobilitási szolgáltatás leküldéses telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Az Azure-bA a feladatátvételt követően a Contoso biztosítani szeretné az Azure-ban a replikált virtuális gépek csatlakozni tudnak. Szeretne csatlakozni a replikált virtuális gépek az Azure-ban, a Contoso-rendszergazdák a helyszíni virtuális gépen az áttelepítés előtt néhány feladatot kell elvégeznie: 

1. Az interneten keresztüli eléréshez akkor engedélyezze az RDP a helyszíni virtuális gépen a feladatátvétel előtt. Akkor győződjön meg arról, hogy a TCP és UDP-szabályokat a adják a **nyilvános** profilt, és az, hogy az RDP engedélyezve van-e a **Windows tűzfal** > **engedélyezett alkalmazások** az összes profil számára.
2. A Contoso-site-to-site VPN-kapcsolaton keresztüli eléréséhez akkor engedélyezze az RDP a helyszíni gépen. Azok az RDP engedélyezése **Windows tűzfal** > **engedélyezett alkalmazások és szolgáltatások** a **tartomány és privát** hálózatok.
3. Azok az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa be a helyszíni virtuális gép **OnlineAll**.

Contoso-rendszergazdák is ellenőriznie kell ezeket az elemeket, a feladatátvétel futtatásakor:

- Lehetnek nincsenek függőben lévő Windows-frissítések a virtuális gépen a feladatátvétel elindításakor. Ha Windows-frissítések függőben lévő, felhasználók Contoso nem jelentkezhet be a virtuális géphez, amíg a frissítés nem fejeződött.
- A feladatátvételt követően ellenőrizze a rendszergazdák **rendszerindítási diagnosztika** , a virtuális gép képernyőképének megtekintéséhez. Ha nem tudja megtekinteni a rendszerindítási diagnosztika, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>5. lépés: A helyszíni virtuális gépek replikálása az Azure-bA

Mielőtt futtatná az áttelepítés az Azure-ba, Contoso rendszergazdáknak kell beállítani, és a helyszíni virtuális gép replikációjának engedélyezéséhez.

### <a name="set-a-replication-goal"></a>Állítsa be a replikációs cél

1. A tárolóban, a tároló neve alatt (**ContosoVMVault**), akkor állítsa be a replikációs cél (**bevezetés** > **Site Recovery**  >   **Az infrastruktúra előkészítése**).
2. Akkor adja meg, hogy a gépek a helyszínen található, az, hogy azok a VMware virtuális gépek replikálása Azure-bA.

    ![Infrastruktúra - védelmi cél előkészítése](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

A folytatáshoz Contoso rendszergazdák győződjön meg arról, hogy azok befejezte üzembe helyezés megtervezése. Kiválasztják **Igen, elvégeztem**. Ez a központi telepítési Contoso csak egyetlen virtuális Gépet áttelepíti, és nincs szükség az üzembe helyezés megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso-rendszergazdák, a forrás-környezet konfigurálása. A forráskörnyezet beállítása, hogy azok egy OVF-sablon letöltése és vele a konfigurációs kiszolgáló és a kapcsolódó összetevőket is magas rendelkezésre állású üzembe, a helyszíni VMware virtuális gép. A kiszolgálón található összetevők:

- A konfigurációs kiszolgálót, hogy koordinálja a helyszíni infrastruktúra és az Azure közötti kommunikációt. A konfigurációs kiszolgáló felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. A folyamatkiszolgáló:
    - Fogadja a replikált adatokat.
    - Gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja a replikációs dátum.
    - Azure Storage replikáció dátum küld.
- A folyamatkiszolgáló Ezenfelül telepíti a mobilitási szolgáltatást a replikálandó virtuális gépeken. A folyamatkiszolgáló hajt végre a helyszíni VMware virtuális gépek automatikus felderítését.
- Után a konfigurációs kiszolgáló virtuális gép létrejött, és elindult Contoso regisztrálja a kiszolgálót a tárolóban.

A forrás beállítása környezeti Contoso rendszergazdák tegye a következőket:

1. Az Azure Portalról az OVF-sablon letöltése (**infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**).
    
    ![Konfigurációs kiszolgáló hozzáadása](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Ezek a sablon létrehozása és üzembe helyezése a virtuális gép VMware importálja.

    ![OVF-sablon üzembe helyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  A virtuális gép első bekapcsolásakor elindul a Windows Server 2016 telepítési folyamatot. Fogadja el a licencszerződést, és beírja egy rendszergazdai jelszót.
4. A telepítés befejeződött, amikor bejelentkeznek a virtuális gép rendszergazdája. Első alkalommal bejelentkezéskor az Azure Site Recovery Configuration Tool automatikusan elindul.
5. A Site Recovery Configuration Tool, adja meg egy nevet a konfigurációs kiszolgálót regisztrálja a tárolóban.
6. Az eszköz ellenőrzi a virtuális gép kapcsolatot az Azure-bA. A kapcsolat létrejötte után kiválasztják **jelentkezzen be a** bejelentkezni az Azure-előfizetést. A hitelesítő adatokat, amelyben a konfigurációs kiszolgáló regisztrálva van a tárolóhoz hozzáféréssel kell rendelkeznie. 

    ![A konfigurációs kiszolgáló regisztrálása](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul. Bejelentkezés a gép újra. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.
8. A varázsló, válassza ki a hálózati Adaptert replikációs forgalom fogadására. Ez a beállítás a konfigurálás után nem módosítható.
9. Akkor válassza ki az előfizetést, az erőforráscsoportot és a Recovery Services-tároló, amelyben a konfigurációs kiszolgálót regisztrálja.

    ![Helyreállítási tár kiválasztása](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Töltse le és telepíti a MySQL-kiszolgáló és a VMWare powercli-t. Ezt követően azok ellenőrzi a kiszolgáló beállításait.
11. Ellenőrzést követően, a vCenter Server példány vagy a vSphere-gazdagépen a teljes tartománynév vagy IP-címét adja meg. Hagyja bejelölve az alapértelmezett portot, és adja meg a vCenter Server-példány megjelenített neve, az Azure-ban.
12. A Site Recovery képes automatikusan felderíteni a replikáláshoz elérhető VMware virtuális gépeket, hogy korábban létrehozott fiókot kell adnia. 
13. Adja meg hitelesítő adatait, így a mobilitási szolgáltatás automatikusan települ, amikor a replikáció engedélyezve van. A Windows-gépek esetében a fióknak a virtuális gépek helyi rendszergazdai engedélyekkel kell rendelkeznie. 

    ![VCenter-kiszolgáló konfigurálása](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Amikor befejeződött a regisztráció, az Azure Portalon, azok ismételten visszaigazolhatja, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a a **forrás** lap a tárolóban. 15 percig vagy tovább is tarthat a felderítés. 
8. A Site Recovery VMware-kiszolgálókat a megadott beállítások használatával csatlakozik, és felderíti a virtuális gépeket.

### <a name="set-up-the-target"></a>A cél beállítása

Most a Contoso-rendszergazdák a cél replikálási környezet konfigurálása:

1. A **infrastruktúra előkészítése** > **cél**, ezek a tárolóbeállítások selecs.
2. A Site Recovery ellenőrzi, hogy nincs-e a storage-fiók és a célként megadott hálózat.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Ha a forrás és cél be vannak állítva, a Contoso rendszergazdák replikációs házirend létrehozása, és a házirendet társítja a konfigurációs kiszolgáló:

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozás és Társítsa**, hoznak létre a **ContosoMigrationPolicy** házirend.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pont megőrzése**: alapértelmezett 24 órányi. Ez az érték határozza meg, mennyi ideig őrzi az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**: az alapértelmezett 1 órás. Ez az érték, amellyel jönnek létre alkalmazáskonzisztens pillanatképek gyakorisága határozza meg.
 
    ![Replikációs házirend - létrehozása](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs házirend - társítás](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*További segítségre van szüksége?*

- Tudjon meg ezeket a lépéseket teljes bemutató [vészhelyreállítás beállítása helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhetők el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [használt konfigurációs kiszolgáló telepítése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikációs beállítások konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>A replikáció engedélyezése

Most Contoso rendszergazdák is replikáljon WebVM.

1. A **alkalmazás replikálása** > **forrás** > **replikálása**, akkor válassza ki az adatforrás-beállítások.
2. Azt jelzik, hogy, hogy szeretnének-e a virtuális gépek engedélyezése, válassza ki a vCenter Server-példány, és a konfigurációs kiszolgáló.

    ![Engedélyezze a replikációt – forrás](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Akkor adja meg a cél beállításai, többek között az erőforráscsoportot és a hálózatot, amelyben az Azure virtuális gép található a feladatátvételt követően. Akkor adja meg a tárfiókot, amely a replikált adatokat tárolni szeretné.

    ![Engedélyezze a replikációt – cél](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Kiválasztják **WebVM** replikálásra. A Site Recovery telepíti a mobilitási szolgáltatást az egyes virtuális Gépeken, ha a replikáció engedélyezve van. 

    ![A replikáció engedélyezése – válassza ki a virtuális Gépet](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Ellenőrizze, hogy a megfelelő replikációs szabályzat van kiválasztva, és engedélyezze a replikációt azoknál **WEBVM**. Ezek tracs replikáció állapotát a **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure Portalon, megjelenik az állapotot az Azure-bA replikáló virtuális gépek:

    ![Infrastruktúra-nézet](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*További segítségre van szüksége?*

Tudjon meg ezeket a lépéseket teljes bemutató [engedélyezze a replikációt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database"></a>6. lépés: Az adatbázis Migrálása 

Contoso rendszergazdák kell a Database Migration Service-projekt létrehozása, majd telepítse át az adatbázist.

### <a name="create-a-database-migration-service-project"></a>A Database Migration Service-projekt létrehozása

1. Ezek a Database Migration Service-projekt létrehozása. Kiválasztják a **SQL Server** forráskiszolgáló típusa, és **Azure SQL Database felügyelt példányába** célként.

     ![Database Migration Service – új migrálási projekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Megnyílik a varázsló.

### <a name="migrate-the-database"></a>Az adatbázis migrálása 

1. A varázsló akkor adja meg a forrás virtuális gép, amelyen a helyszíni adatbázis is található. Ezek az adatbázis eléréséhez hitelesítő adatok megadása.

    ![Database Migration Service - Adatforrás részletei](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. akkor válassza ki az adatbázist a migráláshoz (**SmartHotel.Registration**):

    ![Database Migration Service - adatbázisokat forrás kiválasztása](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. A célként azok nevét a felügyelt példány az Azure-ban, és a hozzáférési hitelesítő adatokat adja meg.

    ![Database Migration Service - cél részletei](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. A **új tevékenység** > **áttelepítés futtatása**, azok áttelepítés beállításainak megadása:
    - Forrás és a cél hitelesítő adatait.
    - Az adatbázist a migráláshoz.
    - A hálózati megosztást, a helyszíni virtuális gépen. A Database Migration Service forrás biztonsági mentések a megosztáshoz vesz igénybe. 
        - A forrás SQL Server-példányt futtató szolgáltatásfiók írási engedélyekkel rendelkeznie kell a megosztáson található.
        - A teljes Tartománynevet a megosztás elérési útját kell használni.
    - Az SAS URI-t, amelyhez a szolgáltatás az áttelepítéshez a biztonságimásolat-fájlokat tölt fel a tárfiók tárolójának hozzáférést biztosít a Database Migration Service.

        ![Database Migration Service - migrálási beállítások konfigurálása](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. A migrálási beállítások mentéséhez, és futtassa az áttelepítés.
6. A **áttekintése**, azok monitos a migrálás állapota.

    ![Database Migration Service - figyelő](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Áttelepítés befejeződött, akkor győződjön meg arról, hogy létezik-e a céladatbázisok a felügyelt példányon.

    ![Database Migration Service - adatbázis áttelepítésének ellenőrzése](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>7. lépés: A virtuális gép áttelepítése

Contoso rendszergazdák futtatása egy gyors feladatátvételi teszt, és ezután migrálja a virtuális Gépet.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Migrálás előtt WEBVM, feladatátvételi teszt segítségével győződjön meg arról, hogy minden a várt módon működik. A rendszergazdák hajtsák végre a következő lépéseket:

1. Ezek feladatátvételi teszt futtatása a legújabb elérhető pontra időben (**legutóbb feldolgozott**).
2. Kiválasztják **gép leállítása a feladatátvétel megkezdése előtt**. Ezt a lehetőséget választja, a Site Recovery megkísérli leállítani a forrás virtuális gép, mielőtt elindítja a feladatátvétel. Feladatátvételi továbbra is fennáll, még akkor is, ha a Leállítás meghiúsul. 
3. Teszt feladatátvétel futtatása: 
    1. Győződjön meg arról, hogy az áttelepítéshez szükséges feltételek vannak érvényben, hogy lefuttatja az előfeltételek ellenőrzését.
    2. A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pontot választja, a helyreállítási pont létrehozása az adatokból.
    3.  Az Azure virtuális gép létrehozása az előző lépésben feldolgozott adatok használatával.
3. A feladatátvétel befejezését követően a replika Azure virtuális gép megjelenik az Azure Portalon. Ezek a cégek ellenőrzik, hogy minden megfelelően működik: a virtuális gép mérete megfelelő, a megfelelő hálózathoz csatlakozik, és fut-e. 
4. Miután ellenőrizte a feladatátvételi tesztet, azok tisztítása a feladatátvételt, majd jegyezze fel a kapcsolatos megfigyelések. 

### <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

1. Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon dolgozni, a Contoso rendszergazdák hozzon létre egy helyreállítási terv áttelepítése, és WEBVM ad hozzá a csomagot:

     ![Helyreállítási terv - elem létrehozása](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Feladatátvétel futtatása, a csomagot, válassza a legutóbbi helyreállítási pontot. Akkor adja meg, hogy a Site Recovery próbálja elindítja a feladatátvétel előtt állítsa le az a helyszíni virtuális gép.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. A feladatátvétel után, győződjön meg arról, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure Portalon.

   ![Helyreállítási terv részletei](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Miután ellenőrizte, az áttelepítési folyamat befejezéséhez az áttelepítés befejezésekor, állítsa le a virtuális gép replikációját, és a Site Recovery-számlázása is a virtuális gép leállítása.

    ![Feladatátvétel - áttelepítés befejezése](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>A kapcsolati karakterlánc frissítése

Az áttelepítési folyamat utolsó lépését, mint a Contoso rendszergazdák frissítse a kapcsolati karakterláncot, az alkalmazás átirányítása a migrált adatbázis, amely a Contoso felügyelt példány fut-e.

1. Az Azure Portalon, akkor keresse meg a kapcsolati karakterláncot kiválasztásával **beállítások** > **kapcsolati karakterláncok**.

    ![Kapcsolati sztringek](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. A karakterlánc a felhasználó nevét és jelszavát, az SQL Database felügyelt példányain frissítse azokat.
3. A karakterlánc konfigurálása után, cserélje le az alkalmazás web.config fájljában aktuális kapcsolati karakterlánc.
4. A fájl frissítése és a mentés után, indítsa újra az IIS a WEBVM futtatásával `IISRESET /RESTART` egy parancssori ablakban.
5. Az IIS újraindítását követően az alkalmazás használ az adatbázis, amely az SQL Database felügyelt példányain fut-e.
6. Ezen a ponton is leállítja őket a helyszínen a SQLVM gép. Az áttelepítés befejeződött.

*További segítségre van szüksége?*

- Ismerje meg, hogyan [feladatátvételi teszt futtatása](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Ismerje meg, hogyan [helyreállítási terv létrehozása](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Ismerje meg, hogyan [átadja a feladatokat az Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött a SmartHotel360 app-beli virtuális gépen fut, és a SmartHotel360 adatbázis érhető el az Azure SQL Database felügyelt példányába.  

Most Contoso cégnek szüksége van a következő karbantartási feladatokat végezheti el:  

- Távolítsa el a WEBVM gép a vCenter Server-készlet.
- Az SQLVM gép eltávolítása a vCenter Server-készlet.
- Távolítsa el WEBVM és SQLVM helyi biztonsági mentési feladatok.
- Frissítse az új hely és az IP-cím megjelenítéséhez a WEBVM belső dokumentációjában.
- Távolítsa el SQLVM belső dokumentációjában. Másik lehetőségként Contoso felülvizsgálhatja SQLVM megjelenítése, mert törölték, és már nem a virtuális gép leltárazása a dokumentációban.
- Tekintse át az erőforrásokat, amelyek interakciót folytatni a leszerelt virtuális gépeket. Frissítés bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és az új infrastruktúra biztonságossá.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapat áttekinti az Azure virtuális gépek és az SQL Database felügyelt példánya a végrehajtásával kapcsolatos biztonsági problémák keresése:

- A csapat áttekinti a hálózati biztonsági csoportok, amelyek segítségével szabályozhatja a hozzáférést a virtuális gép. Hálózati biztonsági csoportok segítségével győződjön meg arról, hogy csak érkező forgalmat, hogy az alkalmazás adhat át.
- A Contoso biztonsági csapat is a mérlegeli a lemezen lévő adatok védelme az Azure Disk Encryption és az Azure Key Vault használatával.
- A csapat lehetővé teszi a felügyelt példány fenyegetésészlelési. A fenyegetésészlelés a Contoso biztonsági csapat vagy szolgáltatás desk rendszer egy a jegy megnyitásához a fenyegetés észlelésekor riasztást küld. Tudjon meg többet [fenyegetések észlelése a felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Felügyelt példány biztonsági - fenyegetések észlelése](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

További információ a biztonsági eljárások a virtuális gépek, lásd: [ajánlott biztonsági eljárások IaaS számítási feladatokhoz az Azure-ban](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="bcdr"></a>BCDR

Az üzletmenet-folytonossági és vészhelyreállítási (bcdr) funkciók a Contoso fogadja a következő műveleteket:

- Adatok biztonsága: Contoso biztonsági másolatot készít az adatok a virtuális gépeken az Azure Backup szolgáltatással. [További információ] https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Bízhatja alkalmazások: Contoso Site Recovery használatával egy másodlagos régióba replikálja az alkalmazást az Azure-beli virtuális gépek. [További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- Contoso megtanulja tájékozódhat felügyelt SQL-példányra, beleértve a [biztonsági másolatairól](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).


### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Contoso rendelkezik egy meglévő licencelése WEBVM. Az Azure Hybrid Benefittel díjszabás előnyeit, Contoso alakítja át a meglévő Azure virtuális gép.
- Contoso lehetővé teszi, hogy a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Cost Management egy többfelhős költségkezelő felügyeleti megoldás, amellyel a Contoso használata és kezelése az Azure és egyéb felhőerőforrások. Tudjon meg többet [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Összegzés

Ebben a cikkben Contoso az Azure-ban a SmartHotel360 alkalmazást áthelyezi az alkalmazás áttelepítésével előtérbeli virtuális gép az Azure-bA a Site Recovery szolgáltatással. Contoso az Azure Database Migration Service használatával a helyszíni adatbázis áttelepíti egy Azure SQL Database felügyelt példányain.

## <a name="next-steps"></a>További lépések

A következő cikkben a sorozat, a Contoso [áthelyezi az Azure virtuális gépeken a SmartHotel360 app](contoso-migration-rehost-vm.md) az Azure Site Recovery szolgáltatással.

