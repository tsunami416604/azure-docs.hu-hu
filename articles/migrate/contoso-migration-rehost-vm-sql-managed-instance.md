---
title: Az Azure-ban egy Contoso alkalmazást át kell telepítenie egy Azure virtuális Gépen, és az Azure SQL felügyelt példány áthelyezési |} Microsoft Docs
description: Ismerje meg, hogyan Contoso áthelyezi az Azure virtuális gépek és az Azure SQL felügyelt példány a helyi alkalmazás
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: c7dc9e8406494739aa5d8f21397a606e0b74a617
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301252"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Contoso áttelepítése: az a helyszíni alkalmazások Azure virtuális gépek és az Azure SQL felügyelt példány áthelyezési

A cikkből megtudhatja, hogyan Contoso áttelepíti a SmartHotel alkalmazás előtér virtuális gép Azure virtuális gépek az Azure Site Recovery szolgáltatásban, és az alkalmazás adatbázis egy Azure SQL-felügyelt példányon.

> [!NOTE]
> Az Azure SQL felügyelt példány jelenleg előzetes verzió.

Ez a dokumentum egy sorozat része, amely dokumentálja a hogyan fiktív cég Contoso áttelepíti a Microsoft Azure felhőbe a helyszíni erőforrások cikkek negyedik. Az adatsorozat magában foglalja a háttér-információkat és szolgáltatásokat, amelyek bemutatják, hogyan lehet egy áttelepítési infrastruktúra beállításához, majd futtassa a különböző típusú áttelepítések sorozata. Forgatókönyvek nő összetettségét, és a következőkben hozzáadott további cikkek adott idő alatt.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[A következő cikket: 1: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
[3. cikk: A helyszíni erőforrások felmérése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
4. cikk: Áthelyezési Azure virtuális gépek és egy SQL felügyelt példány (Ez a cikk) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
[Cikk 5: Az Azure virtuális gépek áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel app virtuális gépek csak a Site Recovery segítségével.
[Cikk 6: Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok áthelyezési](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázis egy SQL Server rendelkezésre állási csoporthoz. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépen](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso áttelepíti a Linux osTicket app virtuális gépek Azure Site Recovery. | Elérhető
[8. cikk: Áthelyezési egy Linux alkalmazást az Azure virtuális gépek és az Azure-beli MySQL kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso a Linux osTicket alkalmazást az Azure virtuális gépen a Site Recovery segítségével, és áttelepítése egy Azure-beli MySQL Server-példányhoz MySQL munkaterület használatával. | Elérhető

Ha szeretné használni a SmartHotel mintaalkalmazást a cikk ezt használja, letöltheti a [github](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>A helyszíni architektúrája

Itt található ábra: az aktuális Contoso helyszíni infrastruktúrát.

![Contoso-architektúra](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso található az a New York Város keleti Amerikai Egyesült Államokban lévő egyik fő datacenter rendelkezik.
- Három további helyi ágak keresztül az Amerikai Egyesült Államokban rendelkeznek.
- A fő adatközpontok csatlakozik az internethez a részszálas metro ethernet-kapcsolat (500 MB/s).
- Minden fiókiroda helyi csatlakozik az internethez, üzleti osztály kapcsolatok használata IPSec VPN-alagutat a fő adatközpontok vissza a. Ez lehetővé teszi, hogy a teljes hálózat véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpontok teljesen virtualizált a VMware. Két ESXi 6.5 virtualizációs gazdagépeket, kezeli a vCenter Server 6.5 rendelkeznek.
- Contoso az Active Directory, az Identitáskezelés és a DNS-kiszolgálók a belső hálózaton.
- A tartományvezérlők, az adatközpontban, a VMware virtuális gépek futtatásához. A helyi ágak: tartományvezérlő fizikai kiszolgálókon.



## <a name="business-drivers"></a>A stratégiai célok

Az informatikai vezetőségi tagja szorosan együttműködik az üzleti partnerek megérteni az üzleti szeretné ezt az áttelepítést a elérése:

- **Üzleti növekedési cím**: Contoso egyre, és ennek eredményeképpen nincs nyomás a helyszíni rendszer és a infrastruktúra.
- **Hatékonyabbá teheti**: Contoso kell távolítsa el a felesleges eljárásokat, és a folyamatok egyszerűsíthető a fejlesztők és a felhasználók.  Üzleti igények informatikai fogja használni, és nem hulladék idő vagy pénz, így gyorsabban továbbítása az ügyfelek igényei.
- **Növeli az agilitást**: Contoso informatikai kell lennie a gyorsabb, az üzleti igényeihez. Gyorsabb, mint a piactéren, a sikeres versenyképes engedélyezéséhez módosításokat reagálni képesnek kell lennie.  Ez nem útban, vagy egy üzleti blokkoló válnak.
- **Skála**: növekedésével az üzleti sikeresen, a Contoso informatikai biztosítania kell a korábbi rendszerek esetén képes ütemben nő.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhő team le az áttelepítés célokat van rögzítve. Ezen célok segítségével meghatározhatja a legjobb áttelepítési módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell azonos teljesítménybeli képességeinek mint jelenleg az helyszíni VMWare környezetben.  A felhőre történő nem jelenti azt, hogy alkalmazás teljesítménye kevésbé fontos.
- Contoso nem szeretné, hogy az alkalmazás beruházásának.  Kritikus fontosságú és fontos üzleti, de az aktuális képernyőn egyszerűen szeretne áthelyezni, mert a felhőhöz.
- Adatbázis-felügyeleti feladatok az alkalmazáshoz való áttelepítése után kell tartani.
- Contoso nem szeretné használni ezt az alkalmazást egy Azure SQL Database, és alternatívák keres.

## <a name="proposed-architecture"></a>Javasolt architektúrája

Ebben a forgatókönyvben:

- Contoso szeretné áttelepíteni a helyszíni kétrétegű utazás alkalmazást.
- Az alkalmazás két virtuális gépet (WEBVM SQLVM) keresztül rétegzett és VMware ESXi-állomáson található **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezetben kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), a virtuális gép futó.
- Az alkalmazás-adatbázis (SmartHotelDB) Azure SQL felügyelt példányhoz áttelepítés után.
- A helyszíni VMware virtuális gépek számára egy Azure virtuális gép áttelepítés után.
- Contoso rendelkezik egy olyan helyszíni adatközpontban (a contoso-adatközpontban), egy a helyi tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépek a Contoso adatközpontban fog kell szerelni, az áttelepítés befejezése után.

![Forgatókönyv-architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Adatbázis-kezelési szolgáltatás](https://docs.microsoft.com/azure/dms/dms-overview) | DMS lehetővé teszi, hogy az Azure data platformok, minimális állásidővel több adatbázis forrásból zökkenőmentes áttelepítés. | További tudnivalók [támogató régiók](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS és get [díjszabása](https://azure.microsoft.com/pricing/details/database-migration/).
[Az Azure SQL felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Felügyelt példány egy felügyelt adatbázis-szolgáltatás, amely egy teljes körűen felügyelt SQL Server-példány Azure felhőben jelöli. Osztja meg ugyanazt a kódot az SQL Server adatbázismotor legújabb verzióját, és a legújabb funkciókat, a teljesítménnyel kapcsolatos fejlesztések és a biztonsági javítások. | Azure SQL adatbázis felügyelt példányok használatával az Azure-ban futó függő díj terheli kapacitása alapján. [További információk](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a kezeli a áttelepítési és vészhelyreállítás Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-bA replikálás során Azure Storage díjak sem merülnek fel.  Azure virtuális gépek jönnek létre, és a függő díj terheli, feladatátvétel esetén. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és az árképzés terén.

 

## <a name="migration-process"></a>áttelepítési folyamat

Contoso telepítse át a webes és az adatok rétegek SmartHotel alkalmazás az Azure-bA.

1. Contoso már rendelkezik az Azure-infrastruktúra, így azok csak kell néhány meghatározott Azure összetevők ehhez a forgatókönyvhöz.
2. Az adatréteg lesz áttelepítve az adatok áttelepítési szolgáltatás (DMS).  DMS a Contoso datacenter és az Azure közötti pont-pont VPN-kapcsolat a helyszíni SQL Server Virtuálisgép kapcsolódik, és majd áttelepíti az adatbázisban.
3. A webes réteg lesz áttelepítve a növekedési és shift áttelepítése az Azure Site Recovery szolgáltatással. Ez magában foglalja a előkészítése a helyszíni VMware-környezetben, a beállítása és a replikáció és a feladatátadás őket az Azure-ba történő áttelepítésére a virtuális gépeket.

     ![Áttelepítési architektúra](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Előfeltételek

Ez mit Contoso (és) kell ehhez a forgatókönyvhöz.

**Követelmények** | **Részletek**
--- | ---
**A képen regisztrálása** | Módosítania kell az SQL által felügyelt példány korlátozott nyilvános előzetes verzióban igényelt. Annak érdekében, hogy az Azure-előfizetésre van szüksége [regisztráljon](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Előfizetési is igénybe vehet néhány napon belül végezze el, ezért győződjön meg arról, hogy lehetővé teszi a forgatókönyv alkalmazása előtt.
**Azure-előfizetés** | Kell már létrehozott egy előfizetést az értékelés a sorozat első cikkében végrehajtásakor. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha még nem a rendszergazda egy meglévő előfizetéshez használ, meg kell dolgoznia a rendszergazdától, hogy rendeljen Önhöz tulajdonosi vagy közreműködői engedélyekkel kell rendelkeznie.<br/><br/> Ha részletesebb engedélyekre van szükség, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**A Site recovery (helyszíni)** | A helyszíni vCenter server 5.5, 6.0 vagy 6.5 kell futnia<br/><br/> Az ESXi-állomáson, 5.5, 6.0 vagy 6.5-ös verzióját<br/><br/> Egy vagy több VMware virtuális gépek az ESXi-állomáson futó.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Támogatott [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurációs.
**DMS** | A DMS kell egy [kompatibilis helyszíni VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Be kell tudnia a helyszíni VPN-eszköz konfigurálásához. Külsőleg irányuló nyilvános IPv4-cím kell rendelkeznie, és a cím nem található a NAT-eszköz mögött.<br/><br/> Ellenőrizze, hogy a helyszíni SQL Server-adatbázis elérhető lesz.<br/><br/> A Windows tűzfal a forrás adatbázismotor eléréséhez képesnek kell lennie. [További információk](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Ha a tűzfal előtt az adatbázis-számítógép, vegye fel a szabályokat, hogy engedélyezi a hozzáférést az adatbázishoz, és a fájlok SMB 445-ös porton keresztül.<br/><br/> SQL Server adatforráshoz kapcsolódnak, és felügyelt célpéldányának használt hitelesítő adatokat a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie.<br/><br/> Kell egy hálózati megosztás DMS használatával a forrás-adatbázis biztonsági mentése az a helyi adatbázisban.<br/><br/> Győződjön meg arról, hogy a szolgáltatás az adatforrás SQL Server-példányt futtató fióknak írási jogosultságokkal a hálózati megosztáson.<br/><br/> Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes hozzáférési jogosultsággal rendelkezik a hálózati megosztáson. Az Azure-adatbázis áttelepítési szolgáltatás megszemélyesít ezeket a biztonsági mentési fájlok feltöltése az Azure storage-tároló felhasználói hitelesítő adatokat.<br/><br/> Az SQL Server Express telepítési folyamat beállítja a TCP/IP-protokoll **letiltott** alapértelmezés szerint. Győződjön meg arról, hogy engedélyezve van.


## <a name="scenario-steps"></a>A forgatókönyv lépései

Az alábbiakban hogyan Contoso lesz az üzemelő példány beállításához:

> [!div class="checklist"]
> * **1. lépés: Állítson be egy SQL Azure felügyelt példány**: egy korábban létrehozott felügyelt példány, amelyre áttelepíti a helyszíni SQL Server-adatbázis szükséges.
> * **2. lépés: Felkészülés a DMS**: az adatbázis áttelepítési szolgáltató regisztrálása, hozzon létre egy példányt, és ezután a DMS-projekt létrehozása szükséges. Akkor is be kell állítania a DMS SA URI. A közös hozzáférésű jogosultságkód (SAS) egységes erőforrás-azonosító (URI) delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz, hogy a tárolási objektum korlátozott engedélyek megadásához. Ezek állíthat be egy SAS URI, így a DMS férhetnek hozzá a fiókot a tároló, amelyhez a szolgáltatás feltölti az SQL Server biztonsági másolat fájljait.
> * **3. lépés: Felkészülés Azure Site Recovery**: A hely helyreállítása után kell a replikált adatok tárolásához Azure storage-fiók létrehozása, és a Recovery Services-tároló létrehozása.
> * **4. lépés: A helyszíni VMware előkészítése a Site Recovery**: Contoso készíti elő a fiókok a virtuális gép felderítés és az ügynök telepítésére, és készítse elő a feladatátvételt követően Azure virtuális gépeken való kapcsolódáshoz.
> * **5. lépés: Replikálása virtuális gépek**: a replikáció beállítása azokat a Site Recovery forrása és célja környezet konfigurálása, állítson be egy replikációs házirendet, és indítsa el a virtuális gépek replikálása az Azure storage.
> * **6. lépés: Telepítse át a DMS adatbázis**: most az áttelepítés után az adatbázis.
> * **7. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése**: Győződjön meg arról, hogy minden rendben működik-e a feladatátvételi teszt futtatása, és futtassa a teljes feladatátvételt a virtuális gépek áttelepítése az Azure-bA.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>1. lépés: Egy felügyelt Azure SQL-példány előkészítése

Egy Azure SQL-felügyelt példány beállításához Contoso alhálózat van szüksége:

- Az alhálózaton kell kijelölnie. Üres kell, és nem tartalmazhat más felhőalapú szolgáltatás, és nem lehet egy átjáró-alhálózatot.
- A felügyelt példány létrehozása után hozzá nem adja hozzá a erőforrásokat.
- Az alhálózat nem lehet vele társított NSG-t.
- Az alhálózat rendelkeznie kell egy felhasználó útvonal tábla (UDR) 0.0.0.0/0 következő ugrásaként internetes rendelve csak útvonalként. 
- Nem kötelező egyéni DNS: Ha egyéni DNS meg van adva a virtuális hálózaton, Azure rekurzív feloldókat IP-cím (például 168.63.129.16) hozzá kell adni a listához. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Az alhálózat nem lehet egy végpontot (tároló vagy SQL) társítva. A virtuális hálózaton Szolgáltatásvégpontok le kell tiltani.
- Az alhálózat rendelkeznie kell legalább 16 IP-címet. [További](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) a felügyelt példány alhálózati méretezésével kapcsolatban.
- A hibrid környezetben egyéni DNS-beállítások szükségesek. Contoso konfigurál DNS-beállítások közül egy vagy több, az Azure DNS-kiszolgálók. [További](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) DNS testreszabással kapcsolatban.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>A felügyelt példány virtuális hálózat beállítása

Contoso a következőképpen állít be a virtuális hálózat: 

1. Contoso elsődleges USA keleti régiója 2 régióban, ContosoNetworkingRG erőforráscsoportban hoz létre egy új virtuális hálózatot (VNET-SQLMI-EU2).
2. Contoso hozzárendel egy címterében 10.235.0.0/24, győződjön meg arról, hogy a tartomány a Contoso vállalati bármely más hálózatokkal nem átfedésben.
2. Két alhálózat adnak hozzá a hálózathoz:
    - SQLMI – DS-EUS2 (10.235.0.0.25)
    - SQLMI-FŰRÉSZ-EUS2 (10.235.0.128/29). Ez az alhálózat használandó directory csatolni a felügyelt példányhoz (SQLMI).

    ![Felügyelt példány hálózati](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Miután a hálózatok és alhálózatok vannak telepítve, a Contoso az alábbiak szerint állomásokhoz hálózatok:

    - Társak VNET-SQLMI-EUS2 a VNET-HUB-EUS2 (a virtuális hálózat, az USA keleti régiója 2 hub).
    - Társak VNET-SQLMI-EUS2 a VNET-termék-EUS2 (a termelési hálózat esetén).

    ![Hálózati társviszony-létesítés](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso beállítja az egyéni DNS-beállításait. DNS először mutasson az Azure-tartományvezérlők. Az Azure DNS másodlagos lesz. A Contoso Azure tartományvezérlők az alábbiak szerint találhatók:

    - A termék-DC-EUS2 alhálózaton található, az USA keleti régiója 2 éles hálózati (VNET-termék-EUS2).
    - CONTOSODC3 cím: 10.245.42.4
    - CONTOSODC4 cím: 10.245.42.5
    - Az Azure DNS-feloldási: 168.63.129.16

     ![Hálózati DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**További segítségre van szüksége?**

- [Áttekintés](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) Azure SQL-felügyelt példánya.
- [További tudnivalók](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) egy virtuális hálózat létrehozása kezelt SQL-példány.
- [További tudnivalók](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) társviszony-létesítés beállítása.
- [További tudnivalók](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) Azure AD DNS-beállítások frissítése.



### <a name="set-up-routing"></a>Útválasztás beállítása

A felügyelt példány kerül egy titkos virtuális hálózat, a Contoso kell egy útválasztási táblázatot, amíg az Azure felügyeleti szolgáltatással való kommunikációra. Ha nem tud kommunikálni a szolgáltatás, amely akkor kezeli, akkor elérhetetlenné válik.

- Az útvonaltábla tartalmaz szabályok (útvonalak), amely meghatározza, hogyan felügyelt példányból küldött csomagok irányítani kell a Vneten belül.
- Az útválasztási táblázatban, amelyben felügyelt példányok telepített alhálózattal hozzá rendelve. Minden egyes alhálózatot elhagyó csomag kezeli a társított útvonaltábla alapján.
- Egy alhálózat csak társítható egy útválasztási táblázatot.
- Nincsenek további költségek az útvonal-táblázatok létrehozása a Microsoft Azure-ban.

1. A Contoso egy felhasználó által definiált útválasztási táblázatot hoz létre. Az útvonaltábla ContosoNetworkingRG erőforráscsoportban jön létre.

    ![Útválasztási táblázat](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Követelményeinek való megfeleléshez példány felügyelt, az útvonaltábla (MIRouteTable) telepítése után, Contoso adjon hozzá egy útvonalat a 0.0.0.0/0, egy címelőtagot és **a következő ugrás típusa** beállítása **Internet**.

    ![Útvonal tábla előtagja](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso útvonaltábla társítani a SQLMI-DB-EUS2 alhálózathoz (a VNET-SQLMI-EUS2-hálózaton). 

    ![Útválasztási táblázat alhálózati](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) felügyelt példány útvonalak beállítása.

### <a name="create-a-managed-instance"></a>Egy felügyelt példányának létrehozása

Most a Contoso SQL felügyelt adatbázispéldány hozhat létre.

1. A felügyelt példány egy üzleti alkalmazás szolgál, mert a Contoso telepítse azt ContosoRG az erőforráscsoporthoz tartozik, az elsődleges USA keleti régiója 2 régió 
2. Akkor válassza ki, egy árképzési szint, és méretének számítási és tárolási példány. [További](https://azure.microsoft.com/pricing/details/sql-database/managed/) árazással kapcsolatos.

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. A felügyelt példányt telepít, a rendszer két új erőforrások ContosoRG erőforráscsoportban jelennek meg:

    - Egy virtuális fürtön, abban az esetben, ha több felügyelt-példány
    - Az SQL Server példány kezeli. 

    ![Felügyelt példány](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) kiépítése a felügyelt példánya.

## <a name="step-2-prepare-dms"></a>2. lépés: Felkészülés a DMS

Készítse elő a DMS Contoso kell tennie a különböző dolgok állíthatók:

- A DMS-szolgáltató regisztrálása az Azure-ban
- Adja meg a DMS hozzáférést használt adatbázis áttelepítése a biztonsági mentési fájlok feltöltése az Azure storage. Ehhez hozzon létre egy blob storage tárolót, és egy közös hozzáférésű Jogosultságkód (SAS) URI létrehozni hozzá. 
- Hozzon létre egy DMS projektet.


Hajtsa végre a lépéseket az alábbiak szerint:

1. Contoso regisztrálja a adatbázis áttelepítési szolgáltatót az előfizetésen belül.
    ![DMS regisztrálása](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Hozzon létre a tárolóra, és létrehozni egy SAS URI-t, úgy, hogy a DMS-e hozzáférési engedélye.

    ![SAS URI-T](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Végül akkor hozzon létre DMS-példányt. 

    ![DMS példány](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso a DMS-példány a termék-DC-EUS2 alhálózati a VNET-termék-DC-EUS2 vnet helyezi.
    - Ezek elhelyezheti van, mert a helyszíni SQL Server virtuális gép egy VPN-átjárón keresztül elérő a Vneten belül kell lennie.
    - A VNET-termék-EUS2 a VNET-HUB-EUS2 társviszonyban van, és a távoli átjárókat használ.  Ez biztosítja, hogy a DMS kommunikálhatnak-e szükség szerint.

        ![DMS hálózati](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**További segítségre van szüksége?**
- [További tudnivalók](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) DMS beállítása.
- [További](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) létrehozása és SAS használatával kapcsolatban.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>3. lépés: A Site Recovery szolgáltatás Azure előkészítése

A Contoso szüksége, hogy a webes réteg (WEBMV) virtuális gép áttelepítéséhez a Site Recovery beállítása az Azure elemek több

- Egy virtuális hálózatot, amelyben a feladatátvételt erőforrások találhatók.
- A replikált adatok tárolásához Azure storage-fiók. 
- Az Azure Recovery Services-tároló.

Akkor állítsa be a Site Recovery az alábbiak szerint:

1. Mivel a virtuális gép egy webes előtér SmartHotel alkalmazásba, akkor hajt végre feladatátvételt a virtuális Gépet a meglévő éles hálózathoz (VNET-termék-EUS2) és az alhálózat (termék-FE-EUS2), az elsődleges régióban USA keleti régiója 2. Maguk állítják be ehhez a hálózathoz, ha be azok [az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md)
2. Akkor hozzon létre egy Azure storage-fiók (contosovmsacc20180528). Standard szintű tárolót, és LRS replikációs egy általános célú fiók használatát.

    ![Helyreállítási tárolási hely](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Fiókkal a hálózati és tárolási helyen Contoso mostantól hozzon létre egy tárolót (ContosoMigrationVault), és naplózza azt a ContosoFailoverRG erőforráscsoport, az elsődleges régióban USA keleti régiója 2.

    ![Recovery Services-tároló](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Azure Site Recovery beállítását.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>4. lépés: A Site Recovery a helyszíni VMware előkészítése

A VMware előkészítése a Site Recovery, itt: Contoso kell tegye:

- Készítse elő a vCenter-kiszolgáló vagy vSphere ESXi-állomáson, automatizálhatja a virtuális gép felderítési fiókkal.
- Készítsen elő egy fiókot, amely lehetővé teszi, hogy az automatikus telepíteni a mobilitási szolgáltatást a replikálni kívánt VMware virtuális gépeken.
- Készítse elő a helyszíni virtuális gépek, úgy, hogy csatlakozhassanak Azure virtuális gépeken, ha a feladatátvételt követően jönnek létre.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Virtuális gépek automatikus észlelését. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Olyan műveletek, például a létrehozása és lemezek eltávolítása, és bekapcsolja a virtuális gépeket futtató fiók szükséges.

Contoso beállítja azt a fiókot az alábbiak szerint:

1. Szerepet hoz létre a vCenter szinten.
2. Ehhez a szerepkörhöz rendeli hozzá a szükséges engedélyekkel.

**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt virtuális gépen.

- A Site Recovery hajthatja végre az automatikus ügyfélleküldéses telepítést, az összetevő, ha engedélyezi a virtuális gép replikációs.
- Az automatikus ügyfélleküldéses telepítést elő kell készíteni egy Site Recovery eléréséhez a virtuális gép által használt fiók.
- Ezt a fiókot az Azure-konzolon a replikációs beállításához adja meg.
- Tartomány vagy helyi fiók telepítése a virtuális Gépre van szüksége.

**További segítségre van szüksége**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) a mobilitási szolgáltatás leküldéses telepítéséhez fiók létrehozása.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátvétel az Azure-bA a Contoso biztosítani kell kapcsolódnia kell az Azure-ban a replikált virtuális gépek. Ehhez az szükséges, van néhány dolgot még az áttelepítés futtatása előtt hajtsa végre a helyszíni virtuális Gépre van szükségük: 

1. Elérés az interneten keresztül, ezek engedélyezze az RDP a feladatátvétel előtt a helyszíni virtuális Gépre, és győződjön meg arról, hogy a TCP és UDP-szabályok hozzáadása történik meg a a **nyilvános** profilt, és, hogy engedélyezi-e az RDP a **Windows tűzfal**  >  **Engedélyezett alkalmazások** minden profil esetében.
2. A pont-pont VPN-kapcsolaton keresztüli eléréshez engedélyezze az RDP-a helyi számítógépen és az RDP engedélyezése a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartomány és Személyes** hálózatok.
3. Azok az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa be a helyszíni virtuális Gépre való **OnlineAll**.

Emellett a feladatátvétel futtatásakor szükségük ellenőrizze a következőket:

- Nem lehet nincs függőben lévő Windows-frissítések a virtuális Gépre feladatátvétel kiváltása. Ha vannak, akkor nem fogja tudni jelentkezzen be a virtuális gép csak a frissítés befejeződése után.
- A feladatátvétel után ellenőrizze kell **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez. Ha ez sem működik, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>5. lépés: A helyszíni virtuális gépek replikálása az Azure Site Recovery szolgáltatással

Áttelepítés fut az Azure-ba, mielőtt a Contoso kell a replikáció beállítása, és engedélyezze a replikálást a helyszíni virtuális gép számára.

### <a name="set-a-replication-goal"></a>A replikációs cél beállítása

1. A tárolóban, a tároló neve (ContosoVMVault) alatt maguk állítják be a replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúraelőkészítése**.
2. Azok a számítógépek a helyszínen található, hogy, hogy azok viselkedése VMware virtuális gépeket, és az Azure-bA replikálni kívánt adnia.

    ![Replikációs cél](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Győződjön meg róla, központi telepítésének megtervezése

A folytatáshoz győződjön meg arról, hogy végzi a központi telepítésének megtervezése kiválasztásával kell **Igen, elvégeztem**. Ebben a telepítésben Contoso csak az egyetlen virtuális gép áttelepítése, és nem kell központi telepítésének megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Most már van szükségük a forráskörnyezet beállítása. Ehhez azokat egy OVF-sablon letöltése és helyezzen vele üzembe, a konfigurációs kiszolgáló és a magas rendelkezésre állásúként társított összetevőit, helyszíni VMware virtuális gép. A kiszolgálón található összetevők:

- A konfigurációs kiszolgáló, amely a helyszíni és az Azure közötti kommunikáció koordinálja, és adatreplikáció kezeli.
- A folyamatkiszolgáló, amelyek egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A konfigurációs kiszolgálón virtuális gép jön létre, és a lépések után Contoso is regisztrálja a tárolóban lévő állapottal.


Contoso hajtsa végre ezeket a lépéseket az alábbiak szerint:

1. Az OVF-sablon Azure-portálról letöltött (**infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**).
    
    ![OVF letöltése](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Azok a sablon létrehozása és telepítése a virtuális gép VMware importálja.

    ![OVF-sablon](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  A virtuális gép első bekapcsolásakor elinduló be egy Windows Server 2016 telepítési élmény. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezése után ezek jelentkezzen be a virtuális gép rendszergazdaként. Első bejelentkezéskor a az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszközt akkor adja meg a nevét, amikor a kiszolgáló regisztrálása a tárolóban lévő állapottal.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után, és melyik **bejelentkezés**, hogy jelentkezzen be az Azure-előfizetés. A hitelesítő adatokat a tárolóba, ahol a konfigurációs kiszolgáló regisztrálva lesz, hozzáféréssel kell rendelkeznie. 

    [Regisztráljon egy konfigurációs kiszolgálót](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul. Bejelentkeznek az a gép újra, és a konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.
8. A varázslóban akkor válassza a NIC-replikációs forgalom fogadására. Ez a beállítás nem módosítható, miután van konfigurálva.
9. Akkor válassza ki, az előfizetés, erőforráscsoport és tárolót, amelyben a konfigurációs kiszolgáló regisztrálásához.
        ![Tároló](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Azokat, majd töltse le és telepítse a MySQL-kiszolgáló és a VMWare PowerCLI. Majd akkor a kiszolgáló beállításainak ellenőrzéséhez.
11. Ellenőrzés után azokat a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címe adható meg. Hagyja meg az alapértelmezett portot, és adjon meg egy rövid nevet a vCenter-kiszolgáló az Azure-ban.
12. Adja meg a korábban létrehozott fiókot, hogy a Site Recovery automatikusan fel tud deríteni VMware virtuális gépek, amelyek érhető(k) el replikálásra van szükségük. 
13. Azok az automatikusan telepíteni a mobilitási szolgáltatást, ha a replikáció engedélyezett-e hitelesítő adatokat adjon meg. A Windows-alapú gépek a fiókot kell a helyi rendszergazdai jogosultsága a virtuális gépeken. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Az Azure portálon, a regisztráció befejezését követően a Contoso dupla ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. 15 perc vagy több tarthat a felderítés. 
8. A Site Recovery majd a megadott beállítások VMware Server csatlakozik, és felderíti a virtuális gépek.

### <a name="set-up-the-target"></a>A cél beállítása

Most már a Contoso be kell állítania a cél replikálási környezetet.

1. A **infrastruktúra előkészítése** > **cél**, akkor adja meg a tároló beállításait.
2. A Site Recovery ellenőrzi, hogy nincs-e az Azure storage-fiók és a hálózat a megadott cél.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

A forrás és cél beállítását követően a Contoso hozzon létre egy replikációs házirendet, és rendelje hozzá azt a konfigurációs kiszolgáló készen áll.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozása és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pontok megőrzésének ideje**. Alapértelmezett 24 órás. Ez az érték határozza meg, hogy mennyi ideig a megőrzési időszak az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezett érték egy óra. Ezt az értéket adja meg, amellyel alkalmazáskonzisztens pillanatképeket jönnek létre.
 
        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs szabályzat társítása](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**További segítségre van szüksége?**

- El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [vész-helyreállítási beállítása a helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhető el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [a konfigurációs kiszolgáló telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikáció beállításainak konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>A replikáció engedélyezése

Contoso most már megkezdheti a WebVM replikálása.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** az adatforrás-beállítások, és melyik.
2. Utal, hogy azok szeretné engedélyezni a virtuális gépek, válassza ki a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

 ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Most hogy adja meg a tárolóbeállítások, beleértve az erőforráscsoportot, amelyben az Azure virtuális Gépen található feladatátvételt követően hálózati és a tárfiókot, amelyben a replikált adatokat fog tárolni a.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso-replikációhoz WebVM választja ki. A Site Recovery telepíti a mobilitási szolgáltatás egyes virtuális gépek replikációját engedélyezésekor. 

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso ellenőrzi, hogy a megfelelő replikációs házirend van kiválasztva, és lehetővé teszi, hogy a replikáció WEBVM. Azok a replikációs folyamatot a nyomon **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure-portálon, a Contoso a struktúra látható a virtuális gépek replikálása Azure-bA.

    ![Infrastruktúra nézet](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**További segítségre van szüksége?**

El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [engedélyezze a replikálást](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>6. lépés: A DMS adatbázis áttelepítése

Contoso kell DMS-projekt létrehozása, majd telepítse át az adatbázist.

### <a name="create-a-dms-project"></a>A DMS-projekt létrehozása
1. Azok a DMS-projekt létrehozása. Akkor adja meg a kiszolgáló típusa az SQL Server és a cél Azure SQL adatbázis felügyelt példányt.

     ![DMS projekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Miután létrehozta a projektet, a varázsló megnyílik.

### <a name="migrate-the-database"></a>Az adatbázis áttelepítése 

1. A varázsló, a Contoso határozza meg a forrás virtuális gép, amelyen a helyi adatbázis található, és hitelesítő adatokat a hozzáférési jogosultsága.

    ![DMS forrás](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Az adatbázis áttelepítése (SmartHotel.Registration), és melyik.

    ![DMS forrásadatbázis](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Célként azok Azure-ban, és hozzáférési hitelesítő adatok a felügyelt példány nevét adja meg.

    ![DMS cél](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Ezt követően a **+ új tevékenység** > **áttelepítés futtatása**, azok áttelepítés beállításainak megadása:
    - Forrás- és hitelesítő adatokat.
    - Adatbázis áttelepítéséhez.
    - A hálózati megosztás hozza létre őket a helyszíni virtuális Gépre. DMS forrás biztonsági mentések erre a megosztásra vesz igénybe.
        - Kell rendelkeznie a forrás SQL Server rendszert futtató szolgáltatásfiók írási jogosultságokkal a megosztás.
        - Adja meg a teljes Tartománynevet a megosztás elérési útvonalát.
    - A DMS hozzáférést biztosít számára, amelyhez a szolgáltatás az áttelepítéshez a biztonságimásolat-fájlok feltöltését fiók tároló SAS URI.

        ![DMS beállításai](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Mentse az áttelepítést, és futtassa azt.
6. A **áttekintése**, azok az áttelepítési állapotának figyelésére.

    ![DMS figyelője](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Az áttelepítés befejezése után ezek győződjön meg arról, hogy létezik-e a céladatbázisokhoz felügyelt-példányon.

    ![DMS figyelője](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>7. lépés: A Site Recovery a virtuális gép áttelepítése

A Contoso egy gyors feladatátvételi fut, és telepítse át a virtuális Gépet.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Áttelepítése előtt WEBVM, a feladatátvételi teszt segítségével győződjön meg arról, hogy minden a várt módon működik. 

1. A legújabb elérhető pontot időt a feladatátvételi tesztet, futtatás (**legújabb feldolgozott**).
2. És melyik **gép leállítása a feladatátvétel megkezdése előtt**, hogy a hely helyreállítási művelet megkísérli indítására, a feladatátvétel előtt állítsa le a virtuális gép – forrásként. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Tesztelési feladatátvételi futtatják: 

    - Egy Előfeltételek ellenőrzése során, hogy az áttelepítéshez szükséges feltételek vannak érvényben.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.
3. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure-portálon. Ellenőrzik, hogy minden megfelelően működik. A virtuális gép mérete megfelelő, a megfelelő hálózathoz csatlakozik, és fut-e. 
4. Annak ellenőrzése után a feladatátvételi tesztet, a Contoso törli azokat a feladatátvételi és a rekordok, és menti a megfigyelések. 

### <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

1. Miután meggyőződött arról, hogy a feladatátvételi tesztet működőképes-e a várt módon, a Contoso áttelepítési helyreállítási tervet hoz létre. A terv WEBVM adnak hozzá.

     ![Helyreállítási terv](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. A feladatátvétel, majd a terv számítógépen futnak. Válassza ki a legutóbbi helyreállítási pontot, és adja meg, hogy a Site Recovery próbálkozzon az időt. a feladatátvétel előtt állítsa le a helyszíni virtuális gép.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. A feladatátvétel után a Contoso ellenőrizze, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure-portálon.

   ![Helyreállítási terv](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Annak ellenőrzése után a virtuális Gépet az Azure-ban, akkor az áttelepítési folyamat befejeződését, állítsa le a replikációt a virtuális gép, és állítsa le a Site Recovery számlázási a virtuális gép áttelepítésének befejezéséhez.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Frissítse a kapcsolati karakterlánc

Az áttelepítési folyamat utolsó lépése Contoso frissíti a kapcsolati karakterlánc az alkalmazás, hogy az áttelepített adatbázis az SQL MI futó mutasson.

1. Az Azure portálon, akkor keresse meg a kapcsolati karakterláncot kattintva **beállítások** > **kapcsolati karakterláncok**.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. A karakterlánc a felhasználó nevét és jelszavát, amely felügyelt SQL-példány frissítése azokat.
3. A karakterlánc konfigurálása után lecserélik a web.config fájlban, az alkalmazás aktuális kapcsolati karakterlánc.
4. A fájl frissítése, és mentse, azok újraindítása után a WEBVM IIS. Erre a **IISRESET /RESTART** cmd parancssorból.
5. IIS újraindítása után, az alkalmazás használni a felügyelt SQL-példányon futó adatbázis.
6. Ezen a ponton a Contoso a SQLVM gép helyszíni leállíthat, és az áttelepítés akkor fejeződött be.

**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) futó feladatátvételi tesztet. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) Azure feladatátvétele.

## <a name="clean-up-after-migration"></a>Az áttelepítés után tisztítása

Az áttelepítés befejeződött a SmartHotel alkalmazást egy Azure virtuális gépen fut, és a SmartHotel adatbázis SQL-példányon az Azure Managed érhető el.  

Most a Contoso kell néhány karbantartása, tegye a következőket:  

- A WEBVM gép eltávolítása a vCenter-készlet.
- A SQLVM gép eltávolítása a vCenter-készlet.
- Távolítsa el a WEBVM és SQLVM a helyi biztonsági mentési feladatok.
- Frissítés belső dokumentációjának megjelenítése az új helyre, WEBVM IP-címet.
- Távolítsa el a SQLVM a dokumentációt. Azt is megteheti azok sikerült jelölje meg megjeleníteni, mivel a törölt és már nem a virtuális gép készlet.
- Tekintse át minden olyan erőforrásnál, amely interakciót folytatni a leszerelt virtuális gépeket, és bármely vonatkozó beállítások vagy a dokumentációjával tükrözzék az új konfiguráció frissítése.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban a Contoso kell teljesen üzemeltető, és az új infrastruktúra biztonságáról.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapata nem ellenőrzi az Azure virtuális gépek és SQL felügyelt példány, a végrehajtásával kapcsolatos biztonsági problémák meghatározásához.

- Ezek tekintse át a hálózati biztonsági csoportok (NSG-k) a virtuális géphez való hozzáférést. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom teljen.
- Akkor is fontolgatja biztonságossá tétele az Azure Disk Encryption és Azure KeyVault lemezen lévő adatokat.
- A fenyegetésészlelés meg az SQL által felügyelt példány (SQLMI) lehetővé teszik. Riasztást küld a biztonsági csoport/szolgáltatás ügyfélszolgálati rendszerhez jegy megnyitni, ha egy fenyegetést észlelte. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Felügyelt példány biztonsági](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[További](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásokkal kapcsolatban.

### <a name="backups"></a>Biztonsági másolatok
Az adatok biztonsági mentésének az Azure Backup szolgáltatás használatával WEBVM a Contoso lesz. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és pluszköltséggel optimalizálása

1. Contoso WEBVM a meglévő licencelésből rendelkezik, és fogja használni, az Azure hibrid juttatásra.  Ezeket átkonvertálja a meglévő Azure VM a díjszabás előnyeit.
2. Contoso által Cloudyn, Microsoft leányvállalata Azure költség felügyeletét teszi lehetővé. A többszörös felhő költsége-kezelési megoldás, amely segít a használatára, és Azure és a más felhőalapú erőforrások kezelésére is.  [További](https://docs.microsoft.com/azure/cost-management/overview) Azure költség-kezeléssel kapcsolatos. 


## <a name="conclusion"></a>Összegzés

Ebben a cikkben a Contoso rehosted az alkalmazás előtér virtuális gép áttelepítésével a Site Recovery szolgáltatás használatával az SmartHotel alkalmazást az Azure-ban. Egy Azure SQL felügyelt példányhoz használatával DMS ezek át a helyi adatbázis.

## <a name="next-steps"></a>További lépések

A következő cikk az adatsorozat mutat hogyan Contoso áthelyezési az Azure virtuális gépek SmartHotel alkalmazás csak az Azure Site Recovery szolgáltatás használatával.

