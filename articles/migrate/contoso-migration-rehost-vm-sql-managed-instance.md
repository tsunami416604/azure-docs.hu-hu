---
title: Áthelyezési Contoso-alkalmazást, az Azure-ban egy Azure virtuális gép és az Azure SQL felügyelt példánya való migrálással |} A Microsoft Docs
description: Ismerje meg, hogyan a Contoso áthelyezi az Azure virtuális gépek és Azure SQL felügyelt példánya a helyszíni alkalmazás
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: raynew
ms.openlocfilehash: 99eda135161a228fde139458de30f5120af55153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723944"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Contoso áttelepítési: egy a helyszíni alkalmazás Újratárolása az Azure virtuális gépek és Azure SQL felügyelt példánya

Ez a cikk bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazás előtérbeli virtuális gép Azure-beli virtuális gépek használata az Azure Site Recovery szolgáltatás és az alkalmazás-adatbázis egy Azure SQL felügyelt példánya a.

> [!NOTE]
> Az Azure SQL felügyelt példánya jelenleg előzetes verzióban érhető el.

Ez a dokumentum, amely dokumentálja, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait egy sorozat negyedik. A sorozat tartalmazza a háttér-információkat, és a egy áttelepítési infrastruktúra beállítását, és futtassa a különböző típusú migrálások bemutató forgatókönyvek sorozata. Forgatókönyvek egyre összetettebbé válnak, és adunk hozzá további cikkek idővel.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes Contoso áttelepítési forgatókönyvek ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Mérje fel az alkalmazás virtuális gépek a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
4. cikk: Újratárolás az Azure virtuális gépek és a egy felügyelt SQL-példány (a jelen cikkben) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Az alkalmazás előtérbeli virtuális gépet át [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az adatbázis használatával a [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás migrálása a felügyelt SQL-példányt. | Elérhető
[Cikk 5: Az Azure virtuális gépeken áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel app virtuális gépek csak a Site Recovery használatával.
[A cikk 6: Újratárolás az Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. A Site Recovery számára, hogy az alkalmazás virtuális gépeit és a egy SQL Server rendelkezésre állási csoportot az alkalmazás-adatbázis áttelepítése a Database Migration service használnak. | Elérhető
[7. cikk: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával. | Elérhető
[A cikk 8: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, valamint a MySQL Workbench használatával az Azure MySQL Server-példány. | Elérhető

Ha szeretné használni ebben a cikkben használt mintaalkalmazás SmartHotel, letöltheti azt [github](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>A helyszíni architektúra

Íme az aktuális Contoso a helyszíni infrastruktúra bemutató ábra.

![Contoso-architektúra](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso a az városa New York a kelet-Egyesült Államok található egy fő adatközpont rendelkezik.
- Három további helyszíni ágakat Egyesült államokbeli városba rendelkeznek.
- A fő adatközpont Fiber metro ethernet-kapcsolattal (500 MB/s) az internethez csatlakozik.
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

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok segítségével meghatározhatja a leginkább megfelelő áttelepítési módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell teljesítmény ugyanazokat a lehetőségeket, mint jelenleg helyszíni VMWare környezetben.  A felhőbe való áthelyezés nem jelenti azt, hogy teljesítményének kevésbé fontos.
- Nem a Contoso szeretné fektethet be ezt az alkalmazást.  Kritikus fontosságú és fontos üzleti, de a jelenlegi formájában egyszerűen szeretnének, mivel a felhőbe áthelyezni.
- Adatbázis-felügyeleti feladatok kerülendő, az alkalmazás való áttelepítése után.
- A Contoso nem szeretné használni ezt az alkalmazást az Azure SQL Database és lehetőségeket keres.

## <a name="proposed-architecture"></a>Javasolt architektúra

Ebben a forgatókönyvben:

- A Contoso biztosítani szeretné áttelepíteni a kétrétegű helyszíni utazási alkalmazást.
- Az alkalmazás osztva két virtuális gépre (WEBVM és SQLVM), és VMware ESXi-gazdagépen található **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Az alkalmazás-adatbázis (SmartHotelDB) nekik át az Azure SQL felügyelt példányát.
- A helyszíni VMware virtuális gépeket át azokat egy Azure virtuális Gépen.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.

![Forgatókönyv-architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Adatbázis-kezelési szolgáltatás](https://docs.microsoft.com/azure/dms/dms-overview) | A DMS lehetővé teszi a zökkenőmentes áttelepítések adatbázis több forrásból származó, az Azure-beli adat-platformokra, minimális állásidővel. | Ismerje meg [támogatott régiók](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS és a get [díjszabás](https://azure.microsoft.com/pricing/details/database-migration/).
[Az Azure SQL felügyelt példánya](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Felügyelt példány egy felügyelt adatbázis-szolgáltatás, amely egy teljes körűen felügyelt SQL Server-példány az Azure-felhőben jelöli. Ez ugyanazt a kódot osztanak meg a legújabb SQL Server adatbázismotor és a legújabb funkciók, a teljesítménnyel kapcsolatos fejlesztések és a biztonsági javítások. | Azure SQL Database felügyelt példányain használatával az Azure-ban futó díj terheli a kapacitást. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a migrálás és vész-helyreállítási kezeli az Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-ba, során Azure Storage-díjat számítunk fel.  Azure virtuális gépek jönnek létre, és a költségek, amikor feladatátvételt hajt végre. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és díjszabás.

 

## <a name="migration-process"></a>Áttelepítési folyamat

Contoso telepítse át a webes és az adatok rétegek a SmartHotel alkalmazás az Azure-bA.

1. Contoso már rendelkezik az Azure-infrastruktúra, így azok csak hozzá kell adnia egy adott Azure-összetevőket ebben a forgatókönyvben számos.
2. Az adatréteg lesz áttelepítve a Data Migration Service (DMS).  A DMS a Contoso-datacenter és az Azure között helyek közötti VPN-kapcsolat a helyszíni SQL Server rendszerű virtuális gép csatlakozik, és ezután áttelepíti az adatbázisban.
3. A webes szint áttelepíthetők az Azure Site Recovery egy lift-and-shift-áttelepítés használatával. Ez magában foglalja a helyszíni VMware-környezet előkészítése, beállítása és a replikáció engedélyezése és feladatátvétele őket az Azure által a virtuális gépeket migrálja.

     ![Áttelepítési architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Előfeltételek

Íme, mi Contoso (és) van szüksége az ebben a forgatókönyvben.

**Követelmények** | **Részletek**
--- | ---
**Regisztráljon az előzetes verzió** | Regisztrálni kell az SQL felügyelt példány korlátozott nyilvános előzetes verzióban szüksége. Annak érdekében, hogy Azure-előfizetés szükséges [regisztráció](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Regisztráció végrehajtásához, ezért ügyeljen arra, hogy ezt megteheti a forgatókönyv megvalósításához megkezdése előtt néhány napokat is igénybe vehet.
**Azure-előfizetés** | Kell már létrehozott egy előfizetést az értékelés az oktatóanyag-sorozatban az első cikkben végrehajtását. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.<br/><br/> Ha részletesebb engedélyek van szüksége, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**A Site recovery (helyszíni)** | A helyszíni vCenter server 5.5-ös, 6.0-s vagy 6.5-ös verzió kell futnia<br/><br/> Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú ESXi-gazdagép<br/><br/> Egy vagy több futtató VMware virtuális gépeket az ESXi-gazdagépen.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Támogatott [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurációja.
**A DMS** | A DMS szüksége lesz egy [kompatibilis a helyszíni VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Konfigurálhatja a helyszíni VPN-eszköznek kell lennie. Egy kifelé irányuló, nyilvános IPv4-címmel kell rendelkeznie, és a cím nem található a NAT-eszköz mögött.<br/><br/> Ellenőrizze, hogy hozzáférése van a helyszíni SQL Server-adatbázisát.<br/><br/> A Windows tűzfal eléréséhez a forrás adatbázismotor képesnek kell lennie. [További információk](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Ha a tűzfal elé adatbázis számítógépe, engedélyezi a hozzáférést az adatbázishoz, és a fájlok SMB 445-ös porton keresztül szabályok hozzáadása.<br/><br/> A forrás SQL-kiszolgáló csatlakozik, és a cél felügyelt példány használt hitelesítő adatokat a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie.<br/><br/> Kell egy hálózati megosztás DMS használatával a forrás-adatbázis biztonsági mentése a helyi adatbázisban.<br/><br/> Győződjön meg arról, hogy az a forrás SQL Server-példányt futtató szolgáltatásfiók írási jogosultságokkal rendelkezik a hálózati megosztáson.<br/><br/> Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes körű jogosultságokkal rendelkezik a hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat a biztonsági mentési fájlok feltöltése az Azure storage-tárolóba.<br/><br/> Az SQL Server Express telepítési folyamat beállítja a TCP/IP protokoll **letiltott** alapértelmezés szerint. Győződjön meg arról, hogy engedélyezve van.


## <a name="scenario-steps"></a>A forgatókönyv lépései

Az alábbiakban hogyan Contoso fogja az üzembe helyezés:

> [!div class="checklist"]
> * **1. lépés: Az SQL Azure által felügyelt példány beállításának**: egy előre létrehozott felügyelt példány, amelyhez a helyszíni SQL Server-adatbázis áttelepítése lesz szükségük.
> * **2. lépés: Készítse elő a DMS**: szükségük van a Database Migration-szolgáltató regisztrálásához, és hozzon létre egy példányt, majd a DMS-projekt létrehozása. Is szükségük állítsa be a DMS SA URI-t. Közös hozzáférésű jogosultságkód (SAS) egységes erőforrás-azonosító (URI) delegált hozzáférést biztosít a tárfiókban található erőforrásokhoz, hogy a tárolási objektum korlátozott engedélyek megadásához. Egy SAS URI-t, hogy a DMS hozzáférhet, amelyhez a szolgáltatás az SQL Server biztonsági mentési fájlokat tölt fel a tárfiók tárolójának azok beállítása.
> * **3. lépés: Az Azure Site Recovery előkészítése**: A Site Recovery azok kell a replikált adatok tárolásához Azure storage-fiók létrehozása, és a egy Recovery Services-tároló létrehozása.
> * **4. lépés: A Site Recovery a helyszíni VMware előkészítése**: Contoso fiókokat készít elő a virtuális gép felderítés és az ügynök telepítéséhez, és készítse elő az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően.
> * **5. lépés: A gépek replikálása**: a replikáció beállítása, a Site Recovery forrás és cél-környezet konfigurálása, állítsa be a replikációs szabályzatot és indítsa el a virtuális gépek replikálása az Azure storage-bA.
> * **6. lépés: A DMS-adatbázis áttelepítése**: most nekik is át az adatbázist.
> * **7. lépés: A Site Recovery a virtuális gépek áttelepítése az**:, győződjön meg arról, hogy minden megfelelően működik, a feladatátvételi teszt futtatásához, és futtassa a teljes feladatátvételt az a virtuális gépek áttelepítése az Azure-bA.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>1. lépés: Készítse elő a felügyelt Azure SQL-példány

Állítsa be az Azure SQL felügyelt példánya, a Contoso cégnek szüksége van egy alhálózatot:

- Az alhálózaton kell kijelölnie. Fel kell üres, és bármely más felhőalapú szolgáltatást tartalmaz, és nem lehet egy átjáró-alhálózatot.
- A felügyelt példány létrehozása után hozzá nem adja hozzá a erőforrásokat.
- Az alhálózat nem rendelkezik társított NSG-t.
- Az alhálózat rendelkeznie kell egy felhasználó útválasztási tábla (UDR) 0.0.0.0/0 következő ugrási típusú internettel, az egyetlen útvonal rendelve. 
- Egyéni DNS nem kötelező: Ha egyéni DNS a virtuális hálózaton van megadva, az Azure rekurzív feloldók IP-címet (például a 168.63.129.16) hozzá kell adni a listához. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Az alhálózat nem lehet egy végpontot (storage- vagy SQL) társítva. A Szolgáltatásvégpontok a virtuális hálózat le kell tiltani.
- Az alhálózatnak legalább 16 IP-címet kell rendelkeznie. [További](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) kapcsolatos méretezése a felügyelt példány alhálózatán.
- A hibrid környezetben egyéni DNS-beállítások szükségesek. Contoso DNS-beállítások egy vagy több, az Azure DNS-kiszolgálók használatára konfigurálja. [További](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) DNS testreszabással kapcsolatban.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>A felügyelt példány egy virtuális hálózat beállítása

Contoso a következőképpen állítja be a virtuális hálózat: 

1. Contoso létrehoz egy új virtuális hálózat (VNET-SQLMI-EU2) az elsődleges régióban USA keleti RÉGIÓJA 2, ContosoNetworkingRG az erőforráscsoportban.
2. Contoso rendeli hozzá egy címterében 10.235.0.0/24, biztosítva, hogy a tartomány minden olyan egyéb hálózatok, a Contoso vállalat nem átfedésben.
2. A hálózat két alhálózattal adnak hozzá:
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29). Ez az alhálózat címtár csatlakoztatása a felügyelt példány (SQLMI) használható.

    ![Felügyelt példány hálózat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Miután a virtuális hálózatok és alhálózatok vannak telepítve, a Contoso módon is társul hálózatok:

    - Társ virtuális hálózat – SQLMI-EUS2 a VNET-HUB-EUS2 (az agyi virtuális hálózat esetében az USA keleti RÉGIÓJA 2).
    - Társ virtuális hálózat – SQLMI-EUS2 a VNET-ÉLES-EUS2 (az éles hálózati környezetben).

    ![Hálózatok közötti társviszony](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso állítja be az egyéni DNS-beállításait. Az Azure-tartományvezérlők DNS először fog mutatni. Az Azure DNS másodlagos lesz. A Contoso Azure tartományvezérlők az alábbi helyeken találhatók:

    - Az ÉLES – DC-EUS2 alhálózaton található, az USA keleti RÉGIÓJA 2 éles környezetben hálózati (VNET-ÉLES-EUS2).
    - CONTOSODC3 cím: 10.245.42.4
    - CONTOSODC4 cím: 10.245.42.5
    - Az Azure DNS-feloldási: 168.63.129.16

     ![Hálózati DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**További segítségre van szüksége?**

- [Áttekintés](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) Azure SQL felügyelt példányok.
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) SQL felügyelt példánya a virtuális hálózat létrehozása.
- [Ismerje meg](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) társviszony-létesítés beállításához.
- [Ismerje meg](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) Azure AD DNS-beállításainak frissítése folyamatban van.



### <a name="set-up-routing"></a>Útválasztó beállítása

A felügyelt példány kerül virtuális magánhálózaton, így a Contoso cégnek szüksége van egy útválasztási táblázatot, hogy az Azure felügyeleti szolgáltatással folytatott kommunikációhoz. Ha az azt kezelő szolgáltatás nem tud kommunikálni, azt elérhetetlenné válik.

- Az útvonaltábla szabályok (útvonal) Itt adhatja meg, hogyan kell-e a felügyelt példány által küldött csomagok átirányítani a virtuális hálózat egy készletét tartalmazza.
- Az útvonaltábla társítva, amelyben a felügyelt példányok üzembe helyezése alhálózatokat. Minden egyes alhálózatot elhagyó csomag történik, a társított útvonaltábla alapján.
- Egy alhálózat csak társítható egy útválasztási táblázatot.
- Nincsenek nem vonatkozik külön díj útvonaltáblák létrehozásához a Microsoft Azure-ban.

1. Contoso-felhasználó által megadott útvonal táblát hoz létre. Az útvonaltábla ContosoNetworkingRG az erőforráscsoportban jön létre.

    ![Útválasztási táblázat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Felel meg a felügyelt példány követelményeknek, az útvonaltábla (MIRouteTable) üzembe helyezése után, a Contoso adjon hozzá egy útvonalat, 0.0.0.0/0 címelőtaggal és **következő ugrás típusa** beállítása **Internet**.

    ![Útválasztási táblázat előtagot](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso (a hálózatban VNET-SQLMI-EUS2) SQLMI-DB-EUS2 alhálózattal társít. 

    ![Útválasztási táblázat alhálózat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) útvonalak a felügyelt példány beállítása.

### <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

Most Contoso helyezhet üzembe egy SQL Database felügyelt példányain.

1. Mivel a felügyelt példány szolgál egy üzleti alkalmazás, Contoso telepítheti az elsődleges régióban USA keleti RÉGIÓJA 2, a ContosoRG erőforráscsoportban 
2. Akkor válassza ki, egy árképzési szint, és a méret számítási és tárolási példány. [További](https://azure.microsoft.com/pricing/details/sql-database/managed/) információ a díjszabásról.

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. A felügyelt példány üzembe helyezését követően két új erőforrásokat az ContosoRG erőforráscsoport jelennek meg:

    - Egy virtuális abban az esetben, ha több felügyelt példány, hogy a fürt
    - A felügyelt példány az SQL Server. 

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) kiépítése a felügyelt példány.

## <a name="step-2-prepare-dms"></a>2. lépés: Készítse elő a DMS

Készítse elő a DMS Contoso kell végeznie néhány dolgot:

- A DMS-szolgáltató regisztrálása az Azure-ban
- Adja meg a DMS hozzáférést az Azure storage-adatbázis áttelepítése a biztonsági mentési fájlokat fel. Blob storage-tároló létrehozásával teheti meg, és egy közös hozzáférésű Jogosultságkód (SAS) URI-t létrehozni hozzá. 
- A DMS-projekt létrehozása.


A következő lépéseket:

1. Contoso regisztrálja a Database Migration-szolgáltatót az előfizetésben.
    ![A DMS regisztrálása](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Storage blob-tároló létrehozása, és hozzon létre egy SAS URI-t, hogy a DMS hozzá tud férni.

    ![SAS URI-T](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Végül, hozzon létre egy DMS-példányt. 

    ![A DMS-példány](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso a DMS-példány a VNET-ÉLES – DC-EUS2 virtuális hálózat ÉLES – DC-EUS2 alhálózaton helyezi el.
    - Ezek helyezze el van, mert a virtuális hálózathoz, amelyek hozzáférhetnek a helyszíni SQL Server virtuális gép egy VPN-átjárót kell lennie.
    - A VNET-ÉLES-EUS2 a VNET-HUB-EUS2 társviszonyban áll, és a távoli átjárók használata engedélyezett.  Ez biztosítja, hogy a DMS kommunikálhatnak-e szükség szerint.

        ![A DMS-hálózat](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**További segítségre van szüksége?**
- [Ismerje meg](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) DMS beállítása.
- [További](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) létrehozásáról és a SAS használatával.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>3. lépés: A Site Recovery szolgáltatással az Azure előkészítése

Nincsenek a Contoso cégnek szüksége van ahhoz, hogy a webes réteg (WEBMV) virtuális gép áttelepítéséhez a Site Recovery beállítása az Azure elemek száma

- Egy virtuális hálózatot, amelyben átadta a feladatait erőforrások találhatók.
- A replikált adatok tárolásához Azure storage-fiókkal. 
- Az Azure Recovery Services-tárolóba.

Ezek a Site Recovery az alábbiak szerint állíthatja:

1. Mivel a virtuális gép egy webes előtérrendszer a SmartHotel alkalmazásba, hogy feladatátvételt hajt végre a virtuális Gépet a meglévő éles hálózattal (VNET-ÉLES-EUS2) és az alhálózat (ÉLES-FE-EUS2), az elsődleges régióban USA keleti RÉGIÓJA 2. Állítsa be, ha a hálózat azok [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md)
2. Akkor hozzon létre egy Azure storage-fiók (contosovmsacc20180528). Egy általános célú fiók, és standard szintű storage, LRS-replikációval használja azokat.

    ![Site Recovery-tároló](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. A helyen lévő hálózati és tárolási fiókkal Contoso mostantól létrehozhat egy tárolót (ContosoMigrationVault), és ContosoFailoverRG erőforráscsoportban az USA keleti RÉGIÓJA 2 elsődleges régióban helyezze.

    ![Recovery Services-tároló](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) beállítása az Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>4. lépés: A Site Recovery a helyszíni VMware előkészítése

A Site Recovery VMware előkészítése, a következő mi Contoso kell tennie:

- Készítsen elő egy fiók a vCenter-kiszolgáló vagy vSphere ESXi-gazdagép, virtuális gépek felderítésének automatizálásához.
- Készítsen elő egy fiókot, amely lehetővé teszi, hogy az automatikus telepíteni a mobilitási szolgáltatást a replikálni kívánt VMware virtuális gépeket.
- Készítse elő a helyszíni virtuális gépek esetében, úgy, hogy csatlakozhassanak az Azure virtuális gépek a feladatátvételt követően felálló.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Futtathatja a műveletek, például a létrehozása és eltávolítása a lemezeket, és ne tudják bekapcsolni a virtuális gépek fiók szükséges.

Contoso a következőképpen állítja be a fiók:

1. Létrehoz egy szerepkört a vCenter-szinten.
2. Szerepkörhöz rendeli hozzá a szükséges engedélyekkel.

**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt virtuális gépen.

- A Site Recovery összetevő egy automatikus ügyfélleküldéses telepítés teheti meg, ha engedélyezi a virtuális gép replikálását.
- Automatikus ügyfélleküldéses telepítéshez meg kell készíteni egy fiókot, amelyet a Site Recovery eléri a virtuális Gépet fog használni.
- Ezt a fiókot adja meg, ha az Azure-konzolon a replikáció beállítása.
- Tartomány vagy helyi fiók, amely engedéllyel rendelkezik a virtuális Gépre telepíteni kell.

**További segítségre van szüksége**

[Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) hozzon létre egy fiókot a mobilitási szolgáltatás leküldéses telepítéséhez.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Az Azure-bA a feladatátvételt követően a Contoso biztosítani szeretné az Azure-ban a replikált virtuális gépek csatlakozni tudnak. Ehhez néhány dolgot szükséges a helyszíni virtuális gépen, még az áttelepítés futtatása előtt áll: 

1. Az interneten keresztüli eléréshez, engedélyezze az RDP a helyszíni virtuális gépen a feladatátvétel előtt és győződjön meg arról, hogy a TCP és UDP-szabályokat a adják a **nyilvános** profilt, és az, hogy az RDP engedélyezve van-e a **Windows tűzfal**  >  **Engedélyezett alkalmazások** az összes profil számára.
2. A site-to-site VPN-kapcsolaton keresztüli eléréshez engedélyezze az RDP a helyszíni gépen és az RDP engedélyezése a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartományi és Privát** hálózatok.
3. Azok az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa be a helyszíni virtuális gép **OnlineAll**.

Emellett a feladatátvétel futtatásakor szükségük ellenőrizze az alábbiakat:

- Lehetnek nincsenek függőben lévő Windows-frissítések a virtuális gép feladatátvétel indítása során. Ha vannak, nem tud bejelentkezni a virtuális géphez a frissítés befejeződéséig.
- A feladatátvételt követően ellenőrizze azok **rendszerindítási diagnosztika** , a virtuális gép képernyőképének megtekintéséhez. Ha ez sem működik, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>5. lépés: A helyszíni virtuális gépek replikálása az Azure Site recoveryvel

Mielőtt futtatná az áttelepítés az Azure-ba, Contoso cégnek szüksége van, a replikáció beállítása és a helyszíni virtuális gép replikációjának engedélyezéséhez.

### <a name="set-a-replication-goal"></a>Állítsa be a replikációs cél

1. A tárolóban, a tároló nevét (ContosoVMVault) alatt, állítsa be a replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúraelőkészítése**.
2. Akkor adja meg, hogy a gépek a helyszínen található, az, hogy azok VMware virtuális gépek és az Azure-bA replikálni kívánt.

    ![Replikációs cél](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

A folytatáshoz győződjön meg arról, hogy elvégezték-üzembe helyezés megtervezése, kiválasztásával szükségük **Igen, elvégeztem**. Ebben a telepítésben Contoso csak az egyetlen virtuális gép áttelepítése, és nem kell az üzembe helyezés megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Most már van szükségük a forráskörnyezet beállítása. Ehhez, egy OVF-sablon letöltése és vele a konfigurációs kiszolgáló és a kapcsolódó összetevőket is magas rendelkezésre állású üzembe, a helyszíni VMware virtuális gép. A kiszolgálón található összetevők:

- A konfigurációs kiszolgáló koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- Miután a konfigurációs kiszolgáló virtuális gép létrejött, és elindult Contoso regisztrálhatja a tárolóban.


Contoso hajtsa végre ezeket a lépéseket a következő:

1. Az Azure Portalról az OVF-sablon letöltése (**infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**).
    
    ![OVF letöltése](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Ezek a sablon létrehozása és üzembe helyezése a virtuális gép VMware importálja.

    ![OVF-sablon](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  A virtuális gép első bekapcsolásakor, a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezését követően, jelentkezzen be rendszergazdaként a virtuális géphez. Bejelentkezéskor első az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszközben kell adnia a nevét, amikor a konfigurációs kiszolgáló regisztrálása a tárolóban.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után kiválasztják **jelentkezzen be a**, hogy jelentkezzen be az Azure-előfizetést. A hitelesítő adatokat, amelyben a konfigurációs kiszolgáló regisztrálva lesz a tárolóhoz hozzáféréssel kell rendelkeznie. 

    [Konfigurációs kiszolgáló regisztrálása](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul. Bejelentkeznek a gép újra, és a konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.
8. A varázsló, válassza ki a hálózati Adaptert replikációs forgalom fogadására. Ez a beállítás a konfigurálás után nem módosítható.
9. Akkor válassza ki az előfizetést, erőforráscsoportot és a tároló, amelyben a konfigurációs kiszolgálót regisztrálja.
        ![Tároló](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Majd töltse le és telepítse a MySQL-kiszolgáló és a VMWare powercli-t. Ezt követően, ellenőrizze a kiszolgáló beállításait.
11. Ellenőrzést hogy a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címét adja meg. Hagyja bejelölve az alapértelmezett portot, és adja meg a vCenter-kiszolgáló rövid nevét az Azure-ban.
12. Szükségük van, hogy a Site Recovery képes automatikusan felderíteni a replikáláshoz elérhető VMware virtuális gépeket adja meg a korábban létrehozott fiókot. 
13. Akkor adja meg a hitelesítő adatai automatikusan telepíteni a mobilitási szolgáltatást, ha a replikáció engedélyezve van. Windows-gépek a fióknak a virtuális gépek helyi rendszergazdai jogosultsággal kell rendelkeznie. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Az Azure Portalon, a regisztráció befejezését követően dupla Contoso ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a a **forrás** lap a tárolóban. 15 percig vagy tovább is tarthat a felderítés. 
8. Ezután a Site Recovery VMware-kiszolgálókat a megadott beállításokkal csatlakozik, és felderíti a virtuális gépeket.

### <a name="set-up-the-target"></a>A cél beállítása

Most már van szüksége a Contoso a replikálási célkörnyezet beállítása.

1. A **infrastruktúra előkészítése** > **cél**, akkor válassza ki a célbeállítások.
2. A Site Recovery ellenőrzi, hogy nincs-e egy Azure storage-fiók és a célként megadott hálózat.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

A forrás és cél beállítását követően a Contoso létrehoz egy replikációs házirendet, és társíthatja azt a konfigurációs kiszolgáló készen áll.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozás és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pont megőrzése**. Alapértelmezett 24 órányi. Ez az érték határozza meg, mennyi ideig őrzi az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezés szerint egy óra. Ez az érték, amellyel jönnek létre alkalmazáskonzisztens pillanatképek gyakorisága határozza meg.
 
        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs házirend társítása](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**További segítségre van szüksége?**

- Tudjon meg egy teljes forgatókönyv az alábbi lépéseket a [vészhelyreállítás beállítása helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhetők el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [használt konfigurációs kiszolgáló telepítése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikációs beállítások konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>A replikáció engedélyezése

Most már Contoso is replikáljon az WebVM.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** , válassza ki az adatforrás-beállítások.
2. Ezek azt jelzik, hogy szeretnének-e a virtuális gépek engedélyezéséhez válassza ki a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

 ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Most akkor adja meg a célként megadott beállításokat, beleértve az erőforráscsoport és a hálózatot, amelyben az Azure virtuális gép található a feladatátvételt követően és a tárfiókot, amely a replikált adatokat tárolni szeretné.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso kiválasztja a WebVM replikálásra. A Site Recovery telepíti a mobilitási szolgáltatást az egyes virtuális Gépeken, ha engedélyezze a replikációját. 

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso ellenőrzi, hogy a megfelelő replikációs szabályzat van kiválasztva, és lehetővé teszi, hogy WEBVM replikálása. A replikáció állapotát nyomon követik **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure Portalon, a Contoso a struktúra látható a virtuális gépek replikálása Azure-bA.

    ![Infrastruktúra-nézet](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**További segítségre van szüksége?**

Tudjon meg egy teljes forgatókönyv az alábbi lépéseket a [engedélyezze a replikációt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>6. lépés: A DMS-adatbázis áttelepítése

Hozzon létre egy DMS-projektet, és az adatbázis migrálása a Contoso cégnek szüksége van.

### <a name="create-a-dms-project"></a>A DMS-projekt létrehozása
1. Ezek a DMS-projekt létrehozása. Az SQL Server és a cél Azure SQL Database felügyelt példányába, mint a forráskiszolgáló típusa kell adnia.

     ![A DMS-projekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Miután létrehozta a projektet, megnyílik a varázsló.

### <a name="migrate-the-database"></a>Az adatbázis migrálása 

1. A varázsló, a Contoso adja meg a forrás virtuális gép, amelyen a helyszíni adatbázis található, és a hozzáférési hitelesítő adatok.

    ![A DMS-forrás](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Válassza ki az adatbázis áttelepítése (SmartHotel.Registration).

    ![A DMS forrásadatbázis](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Célként azok az Azure és a hozzáférési hitelesítő adatokat a felügyelt példány nevét adja meg.

    ![A DMS-cél](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Ezt követően a **+ új tevékenység** > **áttelepítés futtatása**, azok áttelepítés beállításainak megadása:
    - Forrás és a cél hitelesítő adatait.
    - Adatbázis áttelepítése.
    - A hálózati megosztást, ezeket létre a helyszíni virtuális gépen. A DMS forrás biztonsági mentések a megosztáshoz vesz igénybe.
        - Példány rendelkeznie kell a forrás SQL Server rendszert futtató szolgáltatásfiók írási jogosultságokkal a megosztáson található.
        - Adja meg a teljes Tartománynevet a megosztás elérési útját.
    - A SAS URI-t, amely a DMS a tárfiók tárolója, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat az áttelepítéshez hozzáférést biztosít.

        ![A DMS-beállítások](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Mentse az áttelepítést, és futtassa azt.
6. A **áttekintése**, azok a migrálási állapotának figyelése.

    ![A DMS-figyelő](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Áttelepítés befejezése után, győződjön meg arról, hogy létezik-e a céladatbázisok a felügyelt példányon.

    ![A DMS-figyelő](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>7. lépés: A Site Recovery a virtuális gép áttelepítése

Contoso egy gyors feladatátvételi tesztet futtatja, és ezután migrálja a virtuális Gépet.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Migrálás előtt WEBVM, feladatátvételi teszt segítségével győződjön meg arról, hogy minden a várt módon működik. 

1. Egy feladatátvételi teszt futtatása a legújabb elérhető időpontra való (**legutóbb feldolgozott**).
2. Kiválasztják **gép leállítása a feladatátvétel megkezdése előtt**, hogy a Site Recovery megkísérli a forrásoldali virtuális gép leállítása a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Teszt feladatátvétel futtatása: 

    - Ellenőrizze, hogy az áttelepítéshez szükséges feltételek vannak érvényben lefuttatja az előfeltételek ellenőrzését.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.
3. A feladatátvétel befejezését követően a replika Azure virtuális gép megjelenik az Azure Portalon. Ellenőrizze, hogy minden megfelelően működik. A virtuális gép mérete megfelelő, a megfelelő hálózathoz csatlakozik, és fut-e. 
4. Miután ellenőrizte a feladatátvételi tesztet, a Contoso megtisztítja a feladatátvétel és a rekordok, és kapcsolatos megfigyelések menti. 

### <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

1. Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon dolgozni, a Contoso egy helyreállítási terv áttelepítése hoz létre. A terv WEBVM adnak hozzá.

     ![Helyreállítási terv](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. A feladatátvétel, majd a csomag futnak. Válassza ki a legutóbbi helyreállítási pontot, és adja meg, hogy a Site Recovery kísérelje meg a helyszíni virtuális gép leállítása a feladatátvétel indítása előtt.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. A feladatátvétel után a Contoso ellenőrizze, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure Portalon.

   ![Helyreállítási terv](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Miután ellenőrizte a virtuális gép az Azure-ban, ezek az áttelepítéshez a migrálási folyamat befejezéséhez, állítsa le a virtuális gép replikálását, és állítsa le a virtuális gép Site Recovery-számlázását.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>A kapcsolati karakterlánc frissítése

Az áttelepítési folyamat utolsó lépését, mint a Contoso frissíti, hogy a migrált adatbázis az SQL Buszpéldány futó mutasson az alkalmazás a kapcsolati karakterlánc.

1. Az Azure Portalon, akkor keresse meg a kapcsolati karakterláncot kattintva **beállítások** > **kapcsolati karakterláncok**.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. A karakterlánc a felhasználónevet és jelszót az SQL felügyelt példányának frissítse azokat.
3. A karakterlánc konfigurálása után, cserélje le az alkalmazás web.config fájljában aktuális kapcsolati karakterlánc.
4. A fájl frissítése és a mentés után, indítsa újra az IIS a WEBVM. Teheti ezt meg **IISRESET /RESTART** cmd parancsot a parancssorba.
5. Az IIS újraindítása után, az alkalmazást használni a felügyelt SQL-példányon futó adatbázis.
6. Ezen a ponton a Contoso az SQLVM gép helyszíni leállíthatja, és az áttelepítés akkor fejeződött be.

**További segítségre van szüksége?**

- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) feladatátvételi teszt futtatása. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) – Azure feladatátvétele.

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött a SmartHotel app-beli virtuális gépen fut, és a SmartHotel adatbázis érhető el az Azure SQL felügyelt példányát.  

Most a Contoso kell néhány karbantartása, tegye a következőket:  

- Távolítsa el a WEBVM gép a vCenter-készlet.
- Az SQLVM gép eltávolítása a vCenter-készlet.
- Távolítsa el WEBVM és SQLVM helyi biztonsági mentési feladatok.
- Frissítés belső dokumentáció megjelenítése az új helyet, WEBVM IP-címet.
- Távolítsa el az SQLVM-dokumentáció. Másik lehetőségként sikerült megjelöli megjelenítése, mert törölték, és már nem a virtuális gép a készlet.
- Tekintse át az erőforrásokat, amelyek interakciót folytatni a leszerelt virtuális gépeket, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és tegye biztonságossá a új infrastruktúrára.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapat áttekinti az Azure virtuális gépek és SQL felügyelt példánya a végrehajtásával kapcsolatos biztonsági problémák meghatározása.

- Tekintse át a hálózati biztonsági csoportok (NSG-k) a virtuális géphez való hozzáférés szabályozása. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom adhat át.
- Emellett fontolgatják biztonságossá tétele az Azure Disk Encryption és az Azure KeyVault a lemezen lévő adatokat.
- A fenyegetésészlelés az az SQL felügyelt példány (SQLMI) lehetővé teszik. Ezek azok biztonsági csapat vagy szolgáltatás desk rendszer egy a jegy megnyitásához a fenyegetés észlelésekor riasztásokat küld. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Felügyelt példány biztonsági](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[További információ](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásairól.

### <a name="backups"></a>Biztonsági másolatok
Az adatok biztonsági mentése az Azure Backup szolgáltatás használatával WEBVM lévő contoso fog. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

1. Contoso WEBVM meglévő licencelésből rendelkezik, és használja az Azure Hybrid Benefit.  A meglévő Azure virtuális gép a díjszabás előnyeinek kihasználása érdekében konvertálja azokat.
2. Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amellyel a leghatékonyabb kihasználásához és kezeléséhez az Azure és egyéb felhőerőforrások.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe. 


## <a name="conclusion"></a>Összegzés

Ebben a cikkben a Contoso rehosted által az alkalmazás előtérbeli virtuális gép migrálása az Azure-bA a Site Recovery szolgáltatással az SmartHotel alkalmazást az Azure-ban. Ezek át a helyszíni adatbázisból egy Azure SQL felügyelt példányra DMS használatával.

## <a name="next-steps"></a>További lépések

A sorozat következő cikkben megtudhatja, hogyan Contoso a SmartHotel alkalmazás újratárolása az Azure virtuális gépek csak az Azure Site Recovery szolgáltatás használatával.

