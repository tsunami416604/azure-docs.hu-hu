---
title: A Contoso a helyszíni alkalmazás újratárolása az Azure virtuális gépek és Azure SQL Database felügyelt példányába való migrálással |} A Microsoft Docs
description: Ismerje meg, hogyan a Contoso áthelyezi egy helyszíni alkalmazást az Azure virtuális gépekhez és Azure SQL Database felügyelt példány használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 3e3f8dffbaa7109423aacdbfbaa658bada8bb84a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215339"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso áttelepítési: egy Azure virtuális gép és az SQL Database felügyelt példánya a helyszíni alkalmazások Újratárolása

Ebben a cikkben Contoso áttelepíti a SmartHotel alkalmazás előtérbeli virtuális gép egy Azure virtuális géphez az Azure Site Recovery szolgáltatással. Contoso is áttelepíti az alkalmazás-adatbázis az Azure SQL Database felügyelt példányain.

> [!NOTE]
> Az Azure SQL Database felügyelt példánya jelenleg előzetes verzióban érhető el.

Ez a cikk egy sorozat része cikkeket, amely a fiktív Contoso hogyan a helyszíni erőforrásokkal áttelepíti a Microsoft Azure felhőbe dokumentumok egyike. A sorozat tartalmazza a háttér-információkat és a egy sorozat olyan áttelepítési infrastruktúra beállítása és futtatása a különböző típusú migrálások bemutató forgatókönyvek. Forgatókönyvek egyre összetettebbé válnak. Cikkek idővel felveszi az adatsorozat.


Cikk | Részletek | Állapot
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a sorozat használt mintaalkalmazások áttekintése. | Szabad
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Szabad
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md) | Contoso fut, a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso értékeli az alkalmazás virtuális gépek használatával a [Azure Migrate](migrate-overview.md) szolgáltatás. Contoso alkalmazás SQL Server-adatbázis értékelésére használatával [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Szabad
4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése az Azure SQL Database felügyelt példányába használatával a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Ez a cikk
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Contoso Azure virtuális gépekre annak SmartHotel alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással. | Szabad
[Cikk 6: Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazások Újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Szabad
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Contoso a Site Recovery használatával az Azure virtuális gépek Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződik. | Szabad
[Cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure virtuális gépek Site Recovery használatával. Az áttelepítése az alkalmazás-adatbázis az Azure Database for MySQL-hez a MySQL Workbench használatával. | Szabad
[9. cikk: Újrabontás egy alkalmazást az Azure web App alkalmazásban és az Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-webalkalmazás, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány. | Szabad
[Cikk 10: Újrabontás egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-webalkalmazás több helyen is előfordul. A webalkalmazás folyamatos készregyártás a GitHub van integrálva. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Szabad
[11. cikk: Újrabontás a Team Foundation Server, a Visual Studio Team Servicesben](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi migrálással, hogy a Visual Studio Team Services az Azure-ban. | Szabad
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-ba, és ezután rearchitects az alkalmazást. Contoso rearchitects az alkalmazás webes réteg Windows tárolójaként, és az alkalmazás-adatbázis rearchitects Azure SQL Database használatával. | Szabad
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service-ben, az Azure Functions, az Azure Cognitive Services és az Azure Cosmos DB használatával. | Szabad

Ebben a cikkben használt SmartHotel mintaalkalmazást letöltheti [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>A helyszíni architektúra


Ez az ábra bemutatja az aktuális Contoso a helyszíni infrastruktúra:

![Aktuális Contoso-architektúra](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

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
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára. Az ügyfelek igényei kell gyors és a nem Hulladékmennyiség idő vagy költséget takaríthat meg, így a vállalat informatikai üzleti igények gyorsabban is készíthetnek.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a cég számára a globális gazdaság végrehajtott módosításokat a képesnek kell lennie. Contoso IT nem kell a területük vagy egy üzleti blocker válnak.
- **Méretezési csoport**: növekedésével a vállalat üzleti sikeresen a Contoso informatikai meg kell adnia rendszerek, amelyek ütemben növekedhet.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat azonosította a célok, az áttelepítés. A vállalat áttelepítési célokat használja a leginkább megfelelő áttelepítési módszer meghatározásához.

- Az áttelepítés után az alkalmazás az Azure-ban, amely az alkalmazás még ma a helyszíni VMWare-környezetet a Contoso azonos teljesítménybeli képességeinek kell rendelkeznie. A felhőbe való áthelyezés nem jelenti azt, hogy teljesítményének kevésbé fontos.
- Contoso nem szeretné az alkalmazás be. Az alkalmazás kritikus fontosságú és fontos üzleti, de a Contoso biztosítani szeretné egyszerűen, az alkalmazás a jelenlegi formájában áthelyezése a felhőbe.
- Adatbázis-felügyeleti feladatokat kerülendő, az alkalmazás való áttelepítése után.
- Contoso nem szeretné használni az Azure SQL Database az alkalmazás. Alternatívák a keresi.

## <a name="proposed-architecture"></a>Javasolt architektúra

Ebben a forgatókönyvben:

- A Contoso biztosítani szeretné áttelepíteni a kétrétegű helyszíni utazási alkalmazást.
- Az alkalmazás két virtuális gép között többszintű (**WEBVM** és **SQLVM**) és a egy 6.5-ös verziójú VMware ESXi-gazdagép található (**contosohost1.contoso.com**). 
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**) egy virtuális gépen.
- Contoso áttelepíti az alkalmazás-adatbázis (**SmartHotelDB**), egy Azure SQL Database felügyelt példányain.
- Contoso áttelepíti a helyszíni VMware virtuális gépeket az Azure virtuális Gépekhez.
- Contoso rendelkezik egy helyszíni adatközpont (**contoso-datacenter**) és a egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek, ha az áttelepítés befejeződött.

![Forgatókönyv-architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

Szolgáltatás | Leírás | Költség
--- | --- | ---
[Adatbázis-kezelési szolgáltatás](https://docs.microsoft.com/azure/dms/dms-overview) | Az adatbázis-kezelési szolgáltatás lehetővé teszi a zökkenőmentes áttelepítés adatbázis több forrásból származó, az Azure-beli adat-platformokra minimális állásidővel. | Ismerje meg [támogatott régiók](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) és [adatbázis-kezelési szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/database-migration/).
[Az Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Felügyelt példány egy felügyelt adatbázis-szolgáltatás, amely egy teljes körűen felügyelt SQL Server-példány az Azure-felhőben jelöli. Ugyanazt a kódot használja, mint a legújabb SQL Server Database Engine, és a legújabb funkciók, a teljesítménnyel kapcsolatos fejlesztések és a biztonsági javítások. | Egy SQL Database felügyelt példányain Azure-ban futó használata tekintetében a kapacitás alapján számítunk fel díjat. Tudjon meg többet [felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A Site Recovery szolgáltatás koordinálja és kezeli az áttelepítés és a vészhelyreállítás az Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-ba, során Azure Storage-díjat számítunk fel.  Az Azure virtuális gépek jönnek létre, és a költségekkel, amikor feladatátvételt hajt végre. Tudjon meg többet [Site Recovery-díjak és árképzési](https://azure.microsoft.com/pricing/details/site-recovery/).

 ## <a name="migration-process"></a>Áttelepítési folyamat

Contoso telepítse át a webes és az adatok szintek annak SmartHotel alkalmazás az Azure-bA; Ehhez hajtsa végre az alábbi lépéseket:

1. Contoso ugyanúgy kell hozzáadni az adott Azure-összetevőket ebben a forgatókönyvben számos már rendelkezik az Azure-infrastruktúra a helyen.
2. A Data Migration Service segítségével az adatréteg lesz migrálva. A Data Migration Service a Contoso-datacenter és az Azure között helyek közötti VPN-kapcsolat a helyszíni SQL Server rendszerű virtuális gép csatlakozik. Ezután a Data Migration Service telepít át az adatbázist.
3. A webes szint áttelepíthetők lift-and-shift-áttelepítés használatával a Site Recovery használatával. A folyamat magában foglalja a helyszíni VMware-környezet előkészítése, beállítása és a replikáció engedélyezése és feladatátvétele őket az Azure által a virtuális gépeket migrálja.

     ![Áttelepítési architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>Előfeltételek

A Contoso és más felhasználók ebben a forgatókönyvben a következő előfeltételeknek kell megfelelnie:

Követelmények | Részletek
--- | ---
**Regisztrálja a felügyelt példány előzetes verzióban érhető el** | Ön regisztrálva legyenek az SQL Database felügyelt példányain korlátozott nyilvános előzetes verzióban érhető el. Azure-előfizetéssel kell [regisztráció](https://portal.azure.com#create/Microsoft.SQLManagedInstance). A regisztráció végrehajtásához, ezért ügyeljen arra, hogy a forgatókönyv megvalósításához megkezdése előtt regisztráljon néhány napot is igénybe vehet.
**Azure-előfizetés** | Érdemes már létrehozott egy előfizetést az értékelés az oktatóanyag-sorozatban az első cikkben végrehajtásakor. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem az előfizetés rendszergazdája, kell dolgozni a rendszergazdától tulajdonosi vagy közreműködői engedélyekkel.<br/><br/> Ha részletesebb engedélyek van szüksége, tekintse meg [Site Recovery-hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**A Site Recovery (helyszíni)** | A helyszíni vCenter Server-példány futhat egy 5.5-ös, 6.0-s vagy 6.5-ös verzió<br/><br/> Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú ESXi-gazdagép<br/><br/> Egy vagy több futtató VMware virtuális gépeket az ESXi-gazdagépen.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Támogatott [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurációja.
**Adatbázis-kezelési szolgáltatás** | Az adatbázis-kezelő szolgáltatás, szüksége lesz egy [kompatibilis a helyszíni VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Konfigurálhatja a helyszíni VPN-eszköznek kell lennie. Egy kívülre irányuló nyilvános IPv4-címmel kell rendelkeznie. A cím nem található a NAT-eszköz mögött.<br/><br/> Ellenőrizze, hogy hozzáférése van a helyszíni SQL Server-adatbázisát.<br/><br/> Windows tűzfal eléréséhez a forrás adatbázismotor képesnek kell lennie. Ismerje meg, hogyan [Windows tűzfal konfigurálása az access adatbázismotor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Ha a tűzfal elé adatbázis számítógépe, adja hozzá az adatbázis és a fájlokat a 445-ös port az SMB-n keresztül való hozzáférést engedélyező szabályokat.<br/><br/> A hitelesítő adatokat az SQL Server-példányhoz való kapcsolódáshoz használt, és amely a cél felügyelt példány a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie.<br/><br/> A hálózatra van szükség a helyszíni adatbázis, amely az adatbázis-felügyeleti szolgáltatás használatával a forrás-adatbázis biztonsági mentése a megosztás.<br/><br/> Győződjön meg arról, hogy az a forrás SQL Server-példányt futtató szolgáltatásfiók írási engedéllyel rendelkezik a hálózati megosztáson.<br/><br/> Jegyezze fel a Windows-felhasználói és a jelszót, amely teljes vezérlési engedélyekkel rendelkezzen a hálózati megosztáson. Az adatbázis-kezelési szolgáltatás megszemélyesíti a felhasználói hitelesítő adatokat a biztonsági mentési fájlok feltöltése az Azure Storage-tárolóba.<br/><br/> Az SQL Server Express telepítési folyamat beállítja a TCP/IP protokoll **letiltott** alapértelmezés szerint. Győződjön meg arról, hogy engedélyezve van.

## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogy a Contoso tervek az üzembe helyezés:

> [!div class="checklist"]
> * **1. lépés: Állítsa be egy SQL Database felügyelt példányain**: Contoso cégnek szüksége van egy előre létrehozott felügyelt példány, amelyre áttelepíti a helyszíni SQL Server-adatbázis.
> * **2. lépés: Felkészülés az adatbázis-kezelési szolgáltatás**: Contoso kell az adatbázis-migrálási szolgáltató regisztrálása, hozzon létre egy példányt, és majd hozzon létre egy adatbázis-kezelési szolgáltatás projektet. Contoso is be kell állítania egy közös hozzáférésű jogosultságkód (SAS) egységes erőforrás-azonosító (URI) az adatbázis-felügyeleti szolgáltatás. Egy SAS URI-t a Contoso storage-fiókban lévő erőforrások delegált hozzáférést biztosít, így a Contoso korlátozott engedélyeket adhat a tárolási objektumra. Contoso beállítja egy SAS URI-t, hogy az adatbázis-kezelő szolgáltatás, amelyhez a szolgáltatás az SQL Server biztonsági mentési fájlokat tölt fel a tárfiók tárolójának hozzáférhessen.
> * **3. lépés: Az Azure Site Recovery előkészítése**: Contoso létre kell hoznia egy tárfiókot a replikált adatok tárolásához a Site Recovery. Azt is létre kell hoznia egy Azure helyreállítási tárat.
> * **4. lépés: A Site Recovery a helyszíni VMware előkészítése**: Contoso készítse elő a virtuális gép felderítés és az ügynök telepítése Azure virtuális géphez való kapcsolódásra a feladatátvételt követően fiókokat.
> * **5. lépés: A gépek replikálása**: a replikáció beállítása a Contoso konfigurálni a Site Recovery forrás és cél környezetekben, állít be egy replikációs szabályzatot és elindítja a virtuális gépek replikálása az Azure Storage.
> * **6. lépés: Az adatbázis Migrálása az adatbázis-Management szolgáltatás használatával**: Contoso áttelepíti az adatbázisban.
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

Contoso a következőképpen állítja be a virtuális hálózat: 

1. Contoso létrehoz egy új virtuális hálózatot (**VNET-SQLMI-EU2**) az elsődleges régióban USA keleti RÉGIÓJA 2. Hozzáadja a virtuális hálózaton a **ContosoNetworkingRG** erőforráscsoportot.
2. Contoso-10.235.0.0/24 címteréhez rendeli hozzá. Contoso biztosítja, hogy a tartomány nem-e átfedésben a vállalat bármely más hálózatokhoz.
2. A hálózat két alhálózattal ad hozzá a Contoso:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Ez az alhálózat egy címtár csatlakoztatása a felügyelt példány szolgál.

    ![Felügyelt példány – virtuális hálózat létrehozása](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Miután a virtuális hálózat és alhálózatok vannak telepítve, a Contoso módon is társul hálózatok:

    - Partnerek **VNET-SQLMI-EUS2** a **VNET-HUB-EUS2** (a központi virtuális hálózaton esetében az USA keleti RÉGIÓJA 2).
    - Partnerek **VNET-SQLMI-EUS2** a **VNET-ÉLES-EUS2** (az éles hálózati környezetben).

    ![Hálózatok közötti társviszony](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso állítja be az egyéni DNS-beállításait. DNS először a Contoso Azure tartományvezérlők mutat. Az Azure DNS egy másodlagos. A Contoso Azure-tartományvezérlőket az alábbi helyeken találhatók:

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

 Útválasztás beállítása:

1. Contoso UDR táblázatot hoz létre. Contoso hoz létre az útvonaltáblában lévő a **ContosoNetworkingRG** erőforráscsoportot.

    ![Útválasztási táblázat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Után az útvonaltábla a felügyelt példány követelményeket, ahhoz, hogy (**MIRouteTable**) van üzembe helyezve, Contoso hozzáad egy útvonalat a 0.0.0.0/0 címelőtaggal rendelkező. A **következő ugrás típusa** beállítás **Internet**.

    ![Útválasztási táblázat előtagot](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso társítja az útvonaltáblában a a **SQLMI-DB-EUS2** alhálózat (az a **VNET-SQLMI-EUS2** hálózati). 

    ![Útválasztási táblázat alhálózat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*További segítségre van szüksége?*

Ismerje meg, hogyan [útvonalak beállítása a felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

Most Contoso építhető ki egy SQL Database felügyelt példánya:

1. A felügyelt példány szolgál egy üzleti alkalmazás, mert a Contoso telepíti a felügyelt példányon a vállalat elsődleges USA keleti RÉGIÓJA 2 régióban. Contoso ad hozzá a felügyelt példány az **ContosoRG** erőforráscsoportot.
2. Contoso kiválaszt egy árképzési szint, méretű számítási és tárolási példány. Tudjon meg többet [felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. A felügyelt példány üzembe helyezése után két új erőforrások megjelennek a **ContosoRG** erőforráscsoportot:

    - A case Contoso virtuális fürtöt több felügyelt példánnyal rendelkezik.
    - A felügyelt példány az SQL Server-adatbázist. 

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*További segítségre van szüksége?*

Ismerje meg, hogyan [felügyelt példány üzembe helyezése](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-management-service"></a>2. lépés: Felkészülés az adatbázis-kezelési szolgáltatás

Készítse elő az adatbázis-kezelő szolgáltatás, a Contoso cégnek szüksége van, ehhez néhány dolgot:

- Az adatbázis-kezelési szolgáltatás-szolgáltató regisztrálása az Azure-ban.
- Adja meg az adatbázis-kezelési szolgáltatás hozzáférést az Azure Storage-fel a biztonságimásolat-fájlokat, amelyek adatbázis áttelepítése. A hozzáférés biztosításához az Azure Storage a Contoso egy Azure Blob storage-tárolót hoz létre. Contoso hoz létre egy SAS URI-azonosítóját a Blob storage-tárolóba. 
- Hozzon létre egy adatbázis-kezelési szolgáltatás projektet.

Contoso befejezi az alábbi lépéseket:

1. Contoso regisztrálja az adatbázis-migrálási szolgáltató az előfizetésben.
    ![Adatbázis-felügyeleti szolgáltatás – regisztráció](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Contoso hoz létre egy Blob storage-tárolóba. Contoso hoz létre egy SAS URI-t, hogy az adatbázis-kezelési szolgáltatás hozzá tud férni.

    ![Adatbázis-kezelési szolgáltatásban – hozzon létre egy SAS URI-t](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Contoso létrehoz egy adatbázis-kezelési szolgáltatás példányt. 

    ![Felügyeleti szolgáltatás adatbázis - példány létrehozása](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso helyezi el az adatbázis-kezelési szolgáltatás példány a **ÉLES – DC-EUS2** alhálózatának a **VNET-ÉLES – DC-EUS2** virtuális hálózat.
    - Contoso van helyezi az adatbázis-kezelő szolgáltatás, mivel a szolgáltatás a virtuális hálózatban, amely hozzáférhet a helyszíni SQL Server virtuális gép egy VPN-átjárót kell lennie.
    - A **VNET-ÉLES-EUS2** társviszonyban áll a **VNET-HUB-EUS2** és a távoli átjárók használata engedélyezett. A **távoli átjárók használata** beállítással biztosíthatja, hogy az adatbázis-felügyeleti szolgáltatás közötti kommunikációhoz szükséges.

        ![Adatbázis-kezelési szolgáltatásban – a hálózat konfigurálása](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*További segítségre van szüksége?*

- Ismerje meg, hogyan [az adatbázis-kezelő szolgáltatás beállítása](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Ismerje meg, hogyan [hozhat létre és használhat SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>3. lépés: A Site Recovery szolgáltatással az Azure előkészítése

Több Azure elemek szükségesek a Contoso beállítása a Site Recovery a webes szint virtuális gép migrálása (**WEBMV**):

- Egy virtuális hálózatot, amelyben az erőforrásokat átvevő találhatók.
- Egy tárfiókot a replikált adatok tárolásához. 
- Az Azure Recovery Services-tárolóba.

Contoso a következők szerint állítja be a Site Recovery:

1. Mivel a virtuális gép egy web front end az SmartHotel alkalmazáshoz, a Contoso átadja a feladatokat a meglévő éles hálózati környezetben a virtuális gép (**VNET-ÉLES-EUS2**) és az alhálózati (**ÉLES-FE-EUS2**). A hálózatot és alhálózatot az elsődleges USA keleti RÉGIÓJA 2 régióban találhatók. A Contoso a hálózat beállítása során azt [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md).
2. Contoso tárfiókot hoz létre (**contosovmsacc20180528**). Contoso egy általános célú fiókot használja. Contoso standard storage és a helyileg redundáns tárolás replikációs választja ki.

    ![Site Recovery – tárfiók létrehozása](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. A hálózati és tárolási fiók helyen, a Contoso egy tárolót hoz létre (**ContosoMigrationVault**). Contoso helyezi a tároló a **ContosoFailoverRG** erőforráscsoport az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Hozzon létre a Recovery Services - tároló](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*További segítségre van szüksége?*

Ismerje meg, hogyan [állítsa be az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>4. lépés: A Site Recovery a helyszíni VMware előkészítése

A Site Recovery VMware előkészítése, a Contoso ezeket a feladatokat kell elvégeznie:

- Készíteni egy fiókot a vCenter Server-példány vagy a vSphere ESXi-gazdagépen. A fiók automatizálja a virtuális gépek felderítésének.
- Készítsen elő egy fiókot, amely lehetővé teszi, hogy a mobilitási szolgáltatás automatikus telepítéséhez, amely a Contoso biztosítani szeretné replikálni a VMware virtuális gépeken.
- Készítse elő a helyszíni virtuális gépek a feladatátvételt követően felálló Azure virtuális gépek csatlakozni.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. Legalább egy csak olvasható fiókra szükség.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Contoso cégnek szüksége van egy műveletek, például a létrehozása és a lemezek eltávolítása, és ne tudják bekapcsolni a virtuális gépeket futtató fiókot.

Ezek a feladatok végrehajtásával a Contoso állítja be a fiók:

1. Létrehoz egy szerepkört a vCenter-szinten.
2. Szerepkörhöz rendeli hozzá a szükséges engedélyekkel.

*További segítségre van szüksége?*

Ismerje meg, hogyan [hozzon létre és rendelhet egy szerepkört automatikus felderítéshez](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítése

A mobilitási szolgáltatást, amely a Contoso biztosítani szeretné replikálni a virtuális gépen telepítve kell lennie. Contoso úgy véli, hogy ezek a tényezők a mobilitási szolgáltatásról:

- A Site Recovery felhasználóival, márpedig az automatikus ügyfélleküldéses telepítés összetevő Contoso lehetővé teszi, hogy a virtuális gép replikálását.
- Az automatikus ügyfélleküldéses telepítéshez, a Contoso elő kell készítenie a Site Recovery eléri a virtuális Gépet használó fiókból.
- Contoso megadja ezt a fiókot, ha azt állítja be az Azure-konzolon a replikációs.
- Contoso tartomány vagy helyi fiók, amely engedéllyel rendelkezik a virtuális Gépre telepíteni kell rendelkeznie.

*További segítségre van szüksége*

Ismerje meg, hogyan [hozzon létre egy fiókot a mobilitási szolgáltatás leküldéses telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Az Azure-bA a feladatátvételt követően a Contoso biztosítani szeretné az Azure-ban a replikált virtuális gépek csatlakozni tudnak. Ha csatlakozni szeretne a replikált virtuális gépek az Azure-ban, a Contoso a helyszíni virtuális gépen az áttelepítés előtt néhány feladatot kell elvégeznie: 

1. Az interneten keresztüli eléréshez Contoso lehetővé teszi, hogy RDP-t a helyszíni virtuális Gépet a feladatátvétel előtt. Contoso biztosítja, hogy a TCP és UDP-szabályokat a adják a **nyilvános** profilt, és az, hogy az RDP engedélyezve van-e a **Windows tűzfal** > **engedélyezett alkalmazások** az összes profil számára .
2. A Contoso-site-to-site VPN-kapcsolaton keresztüli hozzáférés érdekében a Contoso lehetővé teszi, hogy RDP a helyszíni gépen. Contoso lehetővé teszi, hogy az RDP **Windows tűzfal** > **engedélyezett alkalmazások és szolgáltatások** a **tartomány és privát** hálózatok.
3. Contoso beállítása a helyszíni virtuális Gépen, az operációs rendszer TÁROLÓHÁLÓZATI szabályzata **OnlineAll**.

Contoso is be kell jelölnie ezeket az elemeket, a feladatátvétel futtatásakor:

- Lehetnek nincsenek függőben lévő Windows-frissítések a virtuális gépen a feladatátvétel elindításakor. Ha Windows-frissítések függőben lévő, Contoso nem tud bejelentkezni a virtuális géphez, amíg a frissítés nem fejeződött.
- A feladatátvételt követően ellenőrizze Contoso **rendszerindítási diagnosztika** , a virtuális gép képernyőképének megtekintéséhez. Ha Contoso nem tekintheti meg a rendszerindítási diagnosztika, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>5. lépés: A helyszíni virtuális gépek replikálása az Azure Site Recovery használatával

Mielőtt futtatná az áttelepítés az Azure-ba, Contoso cégnek szüksége van, a replikáció beállítása és a helyszíni virtuális gép replikációjának engedélyezéséhez.

### <a name="set-a-replication-goal"></a>Állítsa be a replikációs cél

1. A tárolóban, a tároló neve alatt (**ContosoVMVault**), Contoso állítja be a replikációs cél (**bevezetés** > **Site Recovery**  >   **Az infrastruktúra előkészítése**).
2. Contoso Megadja, hogy a gépek a helyszínen található, hogy számukra, hogy a Contoso biztosítani szeretné az Azure-bA replikálni, és a VMware virtuális gépek.

    ![Infrastruktúra - védelmi cél előkészítése](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

A folytatáshoz Contoso cégnek szüksége van, ellenőrizze, hogy üzembe helyezésének tervezése befejeződött. Győződjön meg arról, hogy a Contoso kiválasztja **Igen, elvégeztem**. Ebben a felállásban Contoso végez áttelepítést csak egyetlen virtuális Gépet, így nem szükséges, üzembe helyezés megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Most Contoso cégnek szüksége van a forráskörnyezet beállítása. A forráskörnyezet beállítása, a Contoso letölti az OVF-sablonnal. Contoso használja a sablon üzembe helyezéséhez, a konfigurációs kiszolgáló és a egy magas rendelkezésre állásúként kapcsolódó összetevőit, a helyszíni VMware virtuális gép. A kiszolgálón található összetevők:

- A konfigurációs kiszolgálót, hogy koordinálja a helyszíni infrastruktúra és az Azure közötti kommunikációt. A konfigurációs kiszolgáló felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. A folyamatkiszolgáló:
    - Fogadja a replikált adatokat.
    - Gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja a replikációs dátum.
    - Azure Storage replikáció dátum küld.
- A folyamatkiszolgáló Ezenfelül telepíti a mobilitási szolgáltatást, amely a Contoso biztosítani szeretné replikálni a virtuális gépen. A folyamatkiszolgáló hajt végre a helyszíni VMware virtuális gépek automatikus felderítését.
- Után a konfigurációs kiszolgáló virtuális gép létrejött, és elindult Contoso regisztrálja a kiszolgálót a tárolóban.

A forráskörnyezet beállítása:

1. Contoso az OVF-sablon tölt le az Azure Portalon (**infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**).
    
    ![Konfigurációs kiszolgáló hozzáadása](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Contoso importálja a sablon létrehozása és üzembe helyezése a virtuális gép VMware.

    ![OVF-sablon üzembe helyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Contoso először a virtuális gép bekapcsolása, ha a virtuális gép elindul egy Windows Server 2016 telepítési folyamatot. Contoso fogadja el a licencszerződést, és beírja egy rendszergazdai jelszót.
4. Ha a telepítés befejeződött, Contoso jelentkezik be a virtuális gép rendszergazdája. Először, a Contoso jelentkezik be a virtuális gép automatikusan futtatja az Azure Site Recovery Configuration Tool.
5. A Site Recovery Configuration Tool Contoso kerül, egy nevet a konfigurációs kiszolgálót regisztrálja a tárolóban.
6. Az eszköz ellenőrzi a virtuális gép kapcsolatot az Azure-bA. A kapcsolat létrejötte után kiválasztja-e a Contoso **jelentkezzen be a** bejelentkezni az Azure-előfizetést. A hitelesítő adatokat, amelyben a konfigurációs kiszolgáló regisztrálva van a tárolóhoz hozzáféréssel kell rendelkeznie. 

    ![A konfigurációs kiszolgáló regisztrálása](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul. Contoso jelentkezik be a gép újra. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.
8. A varázsló Contoso választja ki a hálózati adapter replikációs forgalom fogadására. Ez a beállítás a konfigurálás után nem módosítható.
9. Contoso választja ki az előfizetést, az erőforráscsoportot és a Recovery Services-tároló, amelyben a konfigurációs kiszolgálót regisztrálja.

    ![Helyreállítási tár kiválasztása](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Contoso letölti és telepíti a MySQL-kiszolgáló és a VMWare powercli-t. Ezt követően Contoso ellenőrzi a kiszolgáló beállításait.
11. Contoso ellenőrzést követően kerül, a vCenter Server példány vagy a vSphere-gazdagépen a teljes tartománynév vagy IP-címét. Contoso hagyja az alapértelmezett portot, és megadja a megjelenített neve, a vCenter Server-példány az Azure-ban.
12. Contoso meg kell adnia a fiókot, amelyet korábban létrehozott, hogy a Site Recovery képes automatikusan felderíteni a replikáláshoz elérhető VMware virtuális gépeket. 
13. Contoso kerül a hitelesítő adatait, így a mobilitási szolgáltatás automatikusan települ, amikor a replikáció engedélyezve van. A Windows-gépek esetében a fióknak a virtuális gépek helyi rendszergazdai engedélyekkel kell rendelkeznie. 

    ![VCenter-kiszolgáló konfigurálása](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Amikor befejeződött a regisztráció, az Azure Portalon, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a újra ellenőrzi-e Contoso a **forrás** lap a tárolóban. 15 percig vagy tovább is tarthat a felderítés. 
8. A Site Recovery a megadott beállításokkal csatlakozik a VMware-kiszolgálók. A Site Recovery felderíti a virtuális gépeket.

### <a name="set-up-the-target"></a>A cél beállítása

Most már van szüksége a Contoso a replikálási célkörnyezet beállítása:

1. A **infrastruktúra előkészítése** > **cél**, Contoso választja ki a cél beállításai.
2. A Site Recovery ellenőrzi, hogy nincs-e a storage-fiók és a célként megadott hálózat.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Ha a forrás és cél be vannak állítva, a Contoso létrehoz egy replikációs házirendet, és a házirendet társítja a konfigurációs kiszolgáló:

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozás és Társítsa**, Contoso hoz létre a **ContosoMigrationPolicy** házirend.
2. Contoso az alapértelmezett beállítást használja:
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

Most Contoso is replikáljon WebVM.

1. A **alkalmazás replikálása** > **forrás** > **replikálása**, Contoso választja ki az adatforrás-beállítások.
2. Contoso azt jelzi, hogy azt szeretné engedélyezni a virtuális gépeket választja ki a vCenter Server-példány és beállítja a konfigurációs kiszolgáló.

    ![Engedélyezze a replikációt – forrás](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Contoso megadja a cél beállításai, többek között az erőforráscsoportot és a hálózatot, amelyben az Azure virtuális gép található a feladatátvételt követően. Contoso megadja a tárfiókot, amely a replikált adatokat tárolni szeretné.

    ![Engedélyezze a replikációt – cél](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso kiválasztja **WebVM** replikálásra. A Site Recovery telepíti a mobilitási szolgáltatást az egyes virtuális Gépeken, ha a replikáció engedélyezve van. 

    ![A replikáció engedélyezése – válassza ki a virtuális Gépet](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso ellenőrzi, hogy a megfelelő replikációs szabályzat van kiválasztva. Ezt követően lehetővé teszi a replikációs **WEBVM**. A replikáció állapotát nyomon követi a Contoso **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure Portalon, a Contoso a struktúra látható az Azure-bA replikáló virtuális gépek:

    ![Infrastruktúra-nézet](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*További segítségre van szüksége?*

Tudjon meg ezeket a lépéseket teljes bemutató [engedélyezze a replikációt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-by-using-the-database-management-service"></a>6. lépés: Az adatbázis Migrálása az adatbázis-Management szolgáltatás használatával

Hozzon létre egy adatbázis-kezelési szolgáltatás projektet, majd utána áttelepíteni az az adatbázis a Contoso cégnek szüksége van.

### <a name="create-a-database-management-service-project"></a>Adatbázis-kezelési szolgáltatás projekt létrehozása

1. Contoso egy adatbázis-kezelési szolgáltatás projektet hoz létre. Contoso kiválasztja a **SQL Server** forráskiszolgáló típusa. Contoso kiválasztja **Azure SQL Database felügyelt példányába** célként.

     ![Adatbázis-felügyeleti szolgáltatás – új migrálási projekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Megnyílik a varázsló.

### <a name="migrate-the-database"></a>Az adatbázis migrálása 

1. A varázsló, a Contoso adja meg a forrás virtuális gép, amelyen a helyszíni adatbázis is található. Contoso megadja a hitelesítő adatait az adatbázis eléréséhez.

    ![Adatbázis-kezelési szolgáltatás - Adatforrás részletei](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Contoso választja ki az adatbázist a migráláshoz (**SmartHotel.Registration**):

    ![Adatbázis-felügyeleti szolgáltatás – válassza ki a forrásadatbázisokat](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. A cél Contoso beírja a nevét, a felügyelt példány az Azure-ban. Contoso eléréséhez szükséges hitelesítő adatokat a felügyelt példány kerül.

    ![Adatbázis-kezelési szolgáltatás - cél részletei](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. A **új tevékenység** > **áttelepítés futtatása**, Contoso áttelepítés beállításait határozza meg:
    - Forrás és a cél hitelesítő adatait.
    - Az adatbázist a migráláshoz.
    - A hálózati megosztást, hogy a Contoso a helyszíni virtuális gépen. Az adatbázis-kezelési szolgáltatás forrás biztonsági mentések a megosztáshoz vesz igénybe. 
        - A forrás SQL Server-példányt futtató szolgáltatásfiók írási engedélyekkel rendelkeznie kell a megosztáson található.
        - A teljes Tartománynevet a megosztás elérési útját kell használni.
    - Az SAS URI-t az adatbázis-kezelő szolgáltatás, amelyhez a szolgáltatás az áttelepítéshez a biztonságimásolat-fájlokat tölt fel a tárfiók tárolójának hozzáférést biztosít.

        ![Felügyeleti szolgáltatás adatbázis - migrálási beállítások konfigurálása](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Contoso menti az áttelepítést, és futtatja majd.
6. A **áttekintése**, Contoso az áttelepítés állapotát figyeli.

    ![Adatbázis-kezelési szolgáltatás - figyelő](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Áttelepítés befejeződött, a Contoso ellenőrzi, hogy létezik-e a céladatbázisok a felügyelt példányon.

    ![Adatbázis-felügyeleti szolgáltatás – az adatbázis-áttelepítés ellenőrzése](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>7. lépés: A Site Recovery használatával Migrálja a virtuális Gépet

Contoso egy gyors feladatátvételi tesztet futtatja, és ezután migrálja a virtuális Gépet.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Migrálás előtt WEBVM, feladatátvételi teszt segítségével győződjön meg arról, hogy minden a várt módon működik. Contoso befejeződött a következő lépéseket:

1. Contoso fut egy feladatátvételi tesztet a legújabb elérhető pontra idő (**legutóbb feldolgozott**).
2. Contoso kiválasztja **gép leállítása a feladatátvétel megkezdése előtt**. Ezt a lehetőséget választja, a Site Recovery megkísérli leállítani a forrás virtuális gép, mielőtt elindítja a feladatátvétel. Feladatátvételi továbbra is fennáll, még akkor is, ha a Leállítás meghiúsul. 
3. Teszt feladatátvétel futtatása: 
    1. Győződjön meg arról, hogy az áttelepítéshez szükséges feltételek vannak érvényben, hogy lefuttatja az előfeltételek ellenőrzését.
    2. A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pontot választja, a helyreállítási pont létrehozása az adatokból.
    3.  Az Azure virtuális gép létrehozása az előző lépésben feldolgozott adatok használatával.
3. A feladatátvétel befejezését követően a replika Azure virtuális gép megjelenik az Azure Portalon. Contoso ellenőrzi, hogy minden megfelelően működik-e: a virtuális gép mérete megfelelő, a megfelelő hálózathoz csatlakozik, és fut-e. 
4. Miután ellenőrizte a feladatátvételi tesztet, a Contoso megtisztítja a feladatátvétel. Ezután rögzíti, és észrevételeket menti. 

### <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

1. Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon dolgozni, a Contoso egy helyreállítási terv áttelepítése hoz létre. Contoso WEBVM ad a terv:

     ![Helyreállítási terv - elem létrehozása](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Contoso feladatátvétel futtat a tervet. Contoso a legutóbbi helyreállítási pontot választja. Contoso Megadja, hogy a Site Recovery próbálja elindítja a feladatátvétel előtt állítsa le az a helyszíni virtuális gép.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. A feladatátvétel után a Contoso ellenőrzi, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure Portalon.

   ![Helyreállítási terv részletei](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Miután ellenőrizte a virtuális gép az Azure-ban, Contoso befejezi az áttelepítést az áttelepítési folyamat befejezése, állítsa le a virtuális gép replikálását, és állítsa le a virtuális gép Site Recovery-számlázását.

    ![Feladatátvétel - áttelepítés befejezése](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>A kapcsolati karakterlánc frissítése

Az áttelepítési folyamat utolsó lépését, mint a Contoso frissíti átirányítása a migrált adatbázis, amely a Contoso felügyelt példány fut-e az alkalmazás a kapcsolati karakterláncot.

1. Az Azure Portalon, a Contoso a kapcsolati karakterlánc megkeresése kiválasztásával **beállítások** > **kapcsolati karakterláncok**.

    ![Kapcsolati sztringek](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Contoso frissíti a karakterlánc a felhasználó nevét és jelszavát, az SQL Database felügyelt példányain.
3. A karakterlánc konfigurálását követően a Contoso a web.config fájlban, az alkalmazás aktuális kapcsolati karakterlánc váltja fel.
4. A fájl frissítése és a mentés után a Contoso IIS WEBVM a újraindul. Az IIS újraindításához Contoso fut `IISRESET /RESTART` egy parancssori ablakban.
5. Az IIS újraindítását követően az alkalmazás használ az adatbázis, amely az SQL Database felügyelt példányain fut-e.
6. Ezen a ponton Contoso leállíthatja a helyszínen a SQLVM gép. Az áttelepítés befejeződött.

*További segítségre van szüksége?*

- Ismerje meg, hogyan [feladatátvételi teszt futtatása](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Ismerje meg, hogyan [helyreállítási terv létrehozása](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Ismerje meg, hogyan [átadja a feladatokat az Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött a SmartHotel app-beli virtuális gépen fut, és a SmartHotel adatbázis érhető el az Azure SQL Database felügyelt példányába.  

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

- A biztonsági csapat áttekinti a hálózati biztonsági csoportok, amelyek segítségével szabályozhatja a hozzáférést a virtuális gép. Hálózati biztonsági csoportok segítségével győződjön meg arról, hogy csak érkező forgalmat, hogy az alkalmazás adhat át.
- A Contoso biztonsági csapat is a mérlegeli a lemezen lévő adatok védelme az Azure Disk Encryption és az Azure Key Vault használatával.
- A biztonsági csapat lehetővé teszi a felügyelt példány fenyegetésészlelési. A fenyegetésészlelés a Contoso biztonsági csapat vagy szolgáltatás desk rendszer egy a jegy megnyitásához a fenyegetés észlelésekor riasztást küld. Tudjon meg többet [fenyegetések észlelése a felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Felügyelt példány biztonsági - fenyegetések észlelése](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

További információ a biztonsági eljárások a virtuális gépek, lásd: [ajánlott biztonsági eljárások IaaS számítási feladatokhoz az Azure-ban](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="backups"></a>Tartalékok

Contoso biztonsági másolatot készít az adatok a WEBVM az Azure Backup szolgáltatás használatával. Tudjon meg többet [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Contoso rendelkezik egy meglévő licencelése WEBVM. Az Azure Hybrid Benefittel díjszabás előnyeit, Contoso alakítja át a meglévő Azure virtuális gép.
- Contoso lehetővé teszi, hogy a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Cost Management egy többfelhős költségkezelő felügyeleti megoldás, amellyel a Contoso használata és kezelése az Azure és egyéb felhőerőforrások. Tudjon meg többet [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Összegzés

Ebben a cikkben Contoso az Azure-ban a SmartHotel alkalmazást áthelyezi telepítse át az alkalmazás előtérbeli virtuális gép az Azure-bA a Site Recovery szolgáltatással. Contoso az Azure Database Service Management használatával a helyszíni adatbázis áttelepíti egy Azure SQL Database felügyelt példányain.

## <a name="next-steps"></a>További lépések

A következő cikkben a sorozat, a Contoso [áthelyezi az Azure virtuális gépeken SmartHotel alkalmazás](contoso-migration-rehost-vm.md) az Azure Site Recovery szolgáltatással.

