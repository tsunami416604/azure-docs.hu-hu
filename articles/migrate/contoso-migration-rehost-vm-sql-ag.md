---
title: Áthelyezési egy Contoso alkalmazást át kell telepítenie az Azure virtuális gépek és az SQL Server AlwaysOn rendelkezésre állási csoporthoz |} Microsoft Docs
description: Ismerje meg, hogyan Contoso áthelyezési át kell telepítenie a helyszíni alkalmazások az Azure virtuális gépek és az SQL Server AlwaysOn rendelkezésre állási csoporthoz
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 03e3aaad810f6ccd5fb376765ddbada072dedb06
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301303"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso áttelepítése: az Azure virtuális gépek és az SQL Server AlwaysOn rendelkezésre állási csoport helyi alkalmazások áthelyezési

Ez a cikk bemutatja, hogyan Contoso áthelyezési SmartHotel alkalmazásuk Azure-ban. Egy Azure virtuális Gépen, és az alkalmazás-adatbázis egy Azure SQL Server virtuális gépre, az SQL Server AlwaysOn rendelkezésre állási csoportok Windows Server feladatátvevő fürtben fut az alkalmazás előtér virtuális gép áttelepítés után.

Ez a dokumentum az egyik egy sorozat része, amelyek megjelenítik a hogyan fiktív cég Contoso áttelepíti a Microsoft Azure felhőbe a helyszíni erőforrások cikkeket. Az adatsorozat háttér-információkat, és a szolgáltatásokat, amelyek bemutatják, egy áttelepítési infrastruktúra beállításával, a helyszíni erőforrások az áttelepítéshez értékelésére, és áttelepítések különböző típusú futtató tartalmaz. Forgatókönyvek nő összetettségét, és adott idő alatt további cikkek fel kell venni.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[A következő cikket: 1: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes áttelepítési cikkek szolgál. | Elérhető
[3. cikk: A helyszíni erőforrások felmérése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut-e egy helyszíni kétrétegű SmartHotel alkalmazást a VMware rendszerben futó értékelését. Contoso értékeli az alkalmazás virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési az alkalmazások az Azure virtuális gépek és a felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut a növekedési és shift áttelepítése az Azure-bA az SmartHotel alkalmazás. Contoso áttelepíti az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányához, használja a [Azure adatbázis áttelepítési szolgáltatás](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
[5. cikk: Áthelyezési az alkalmazásoknak az Azure virtuális gépek](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso át a SmartHotel app virtuális gépek csak a Site Recovery segítségével.
Cikk 6: Áthelyezési az alkalmazások Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoportnak (Ez a cikk) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Contoso Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az alkalmazás adatbázis AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépen](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso működik a növekedési és shift áttelepítését a Linux osTicket alkalmazást az Azure virtuális gépen, a Site Recovery segítségével | Tervezve
[8. cikk: Áthelyezési egy Linux alkalmazást az Azure virtuális gépek és az Azure-beli MySQL kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso Azure virtuális gépeken futó Site Recovery segítségével a Linux osTicket alkalmazást áttelepíti, és az alkalmazás adatbázis áttelepítése egy Azure-beli MySQL Server-példányhoz MySQL munkaterület használatával. | Elérhető



Ebben a cikkben a Contoso a kétrétegű Windows telepíti át. Az Azure-bA VMware virtuális gépeken futó NET SmartHotel alkalmazást. Ha azt szeretné, az alkalmazás használatához, való nyílt forráskódú, és letöltheti a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai célok

Az informatikai vezetőségi tagja szorosan együttműködik az üzleti partnerek megértéséhez válasszon ezek közül az áttelepítés számára:

- **Üzleti növekedési cím**: Contoso egyre, és ennek eredményeképpen nincs nyomás a helyszíni rendszer és a infrastruktúra.
- **Hatékonyabbá teheti**: Contoso kell távolítsa el a felesleges eljárásokat, és folyamatok egyszerűsíthető a fejlesztők és a felhasználók számára.  Üzleti igények informatikai fogja használni, és nem hulladék idő vagy pénz, így gyorsabban továbbítása az ügyfelek igényei.
- **Növeli az agilitást**: Contoso informatikai kell lennie a gyorsabb, az üzleti igényeihez. Gyorsabb, mint a piactéren, a sikeres versenyképes engedélyezéséhez módosításokat reagálni képesnek kell lennie.  Ez nem útban, vagy egy üzleti blokkoló válnak.
- **Skála**: növekedésével az üzleti sikeresen, a Contoso informatikai biztosítania kell a korábbi rendszerek esetén képes ütemben nő.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhő team le az áttelepítés célokat van rögzítve. A legjobb áttelepítési módszer meghatározásához használt ezen célok:

- Az áttelepítés után az alkalmazás az Azure-ban kell azonos teljesítménybeli képességeinek azonban jelenleg nem VMWare.  Az alkalmazás el kritikus jelzéssel a felhőben, mert az a helyszínen marad.
- Contoso nem szeretné, hogy az alkalmazás beruházásának.  Fontos, hogy az üzleti tevékenységre vonatkozóan, de az aktuális képernyőn egyszerűen szeretnék biztonságosan helyezze át a felhőbe.
- Az alkalmazás a helyi adatbázis volt-e elérhetőségével kapcsolatos problémákat. Ezek szeretné látni, az Azure szolgáltatásba telepített feladatátvételi lehetőségeket, magas rendelkezésre állású fürtöt.
- A Contoso biztosítani az aktuális SQL Server 2008 R2 platform frissíthet az SQL Server 2017.
- Contoso nem szeretné használni ezt az alkalmazást egy Azure SQL Database, és alternatívák keres.

## <a name="proposed-architecture"></a>Javasolt architektúrája

Ebben a forgatókönyvben:

- Az alkalmazás többszintű két virtuális gépet (WEBVM SQLVM) között.
- A virtuális gépek a VMware ESXi-állomáson lévő **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezetben kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), a virtuális gép futó.
- Contoso rendelkezik egy olyan helyszíni adatközpontban (a contoso-adatközpontban), egy a helyi tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépek a Contoso adatközpontban fog kell szerelni, az áttelepítés befejezése után.

![Forgatókönyv-architektúra](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Adatbázis-kezelési szolgáltatás](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso áttelepíti az alkalmazás adatréteghez tartozó DMS használatával. DMS csatlakozzon a helyszíni SQLVM géphez pont-pont VPN-en keresztül, és lehetővé teszi, hogy zökkenőmentes áttelepítés DMS több adatbázis forrásból át az Azure data platformok, minimális állásidővel. | További tudnivalók [támogató régiók](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS és get [díjszabása](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso fogja használni a Site Recovery a növekedési és shift az alkalmazás előtér virtuális gép áttelepítését. A Site Recovery koordinálja a kezeli a áttelepítési és vészhelyreállítás Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-bA replikálás során Azure Storage díjak sem merülnek fel.  Azure virtuális gépek jönnek létre, és a függő díj terheli, feladatátvétel esetén. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és az árképzés terén.

 

## <a name="migration-process"></a>áttelepítési folyamat

- Contoso áttelepíti az alkalmazás virtuális gépek Azure-bA.
- Az előtér virtuális gép lesz áttelepítés után a virtuális gép Azure Site Recovery:
    - Első lépésként azok lesz előkészítése beállítása az Azure-összetevők és a helyszíni VMware-infrastruktúra előkészítése.
    - Az előkészített mindent elindíthatják a virtuális gép replikálása.
    - Miután replikáció engedélyezett-e, és dolgozik, áttelepítés után a virtuális gép által feladatátvételével az Azure-bA.
- Az adatbázis lesz áttelepítés után az Azure-bA az adatok áttelepítési szolgáltatás (DMS) az SQL Server-fürtöt.
    - Első lépésként kell kiépíteni az Azure SQL Server virtuális gépen a fürt és a belső terheléselosztók beállítása, és AlwaysOn rendelkezésre állási csoportok konfigurálása.
    - Ennek a helyen akkor az adatbázis áttelepítése
- Az áttelepítés után az adatbázis AlwaysOn védelmét lehetővé teszik lesz.

![áttelepítési folyamat](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Előfeltételek

Ez mit meg (és a Contoso) futtatnia kell a forgatókönyv:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetést az értékelés a sorozat első cikkében végrehajtásakor. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha még nem a rendszergazda egy meglévő előfizetéshez használ, meg kell dolgoznia a rendszergazdától, hogy rendeljen Önhöz tulajdonosi vagy közreműködői engedélyekkel kell rendelkeznie.<br/><br/> Ha részletesebb engedélyekre van szükség, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastruktúra** | [Megtudhatja, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúra.<br/><br/> További információ a konkrét [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) Site Recovery követelményei.
**A Site recovery (helyszíni)** | A helyszíni vCenter server 5.5, 6.0 vagy 6.5 kell futnia<br/><br/> Az ESXi-állomáson, 5.5, 6.0 vagy 6.5-ös verzióját<br/><br/> Egy vagy több VMware virtuális gépek az ESXi-állomáson futó.<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Támogatott [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurációs.<br/><br/> Meg kell felelnie a replikálni kívánt virtuális gépeket [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**DMS** | A DMS kell egy [kompatibilis helyszíni VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Be kell tudnia a helyszíni VPN-eszköz konfigurálásához. Külsőleg irányuló nyilvános IPv4-cím kell rendelkeznie, és a cím nem található a NAT-eszköz mögött.<br/><br/> Ellenőrizze, hogy a helyszíni SQL Server-adatbázis elérhető lesz.<br/><br/> A Windows tűzfal a forrás adatbázismotor eléréséhez képesnek kell lennie. [További információk](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Ha a tűzfal előtt az adatbázis-számítógép, vegye fel a szabályokat, hogy engedélyezi a hozzáférést az adatbázishoz, és a fájlok SMB 445-ös porton keresztül.<br/><br/> SQL Server adatforráshoz kapcsolódnak, és felügyelt célpéldányának használt hitelesítő adatokat a sysadmin (rendszergazda) kiszolgálói szerepkör tagjának kell lennie.<br/><br/> Kell egy hálózati megosztás DMS használatával a forrás-adatbázis biztonsági mentése az a helyi adatbázisban.<br/><br/> Győződjön meg arról, hogy a szolgáltatás az adatforrás SQL Server-példányt futtató fióknak írási jogosultságokkal a hálózati megosztáson.<br/><br/> Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes hozzáférési jogosultsággal rendelkezik a hálózati megosztáson. Az Azure-adatbázis áttelepítési szolgáltatás megszemélyesít ezeket a biztonsági mentési fájlok feltöltése az Azure storage-tároló felhasználói hitelesítő adatokat.<br/><br/> Az SQL Server Express telepítési folyamat beállítja a TCP/IP-protokoll **letiltott** alapértelmezés szerint. Győződjön meg arról, hogy engedélyezve van.


## <a name="scenario-steps"></a>A forgatókönyv lépései

Ez a Contoso hogyan működik az áttelepítés:

> [!div class="checklist"]
> * **1. lépés: Az SQL Server virtuális gépek létrehozása az Azure-ban**: A magas rendelkezésre állás érdekében a Contoso telepíteni kívánja az Azure-ban fürtözött adatbázis. Két SQL Server virtuális gépek és az Azure belső terheléselosztót központi telepítése.
> * **2. lépés: A fürt központi telepítése**: az SQL Server virtuális gépen való telepítése után az Azure SQL Server-fürt előkészítése.  Az adatbázis lesz áttelepítés után a korábban létrehozott fürtbe.
> * **3. lépés: Felkészülés a DMS**: készítse elő a DMS regisztrálják az adatbázist szolgáltató, hozzon létre egy DMS-példányt, és a projekt. Akkor állítsa be a közös hozzáférésű jogosultságkód (SAS) egységes erőforrás-azonosító (URI). DMS a rendszergazdai (SA) URI-azonosítója, amelyhez a szolgáltatás feltölti az SQL Server biztonsági másolat fájljait tároló fiók elérésére használt.
> * **4. lépés: Felkészülés Azure Site Recovery**: hoznak létre, és a Recovery Services-tároló replikált adatok tárolásához Azure storage-fiók.
> * **5. lépés: A helyszíni VMware előkészítése a Site Recovery**: készítse elő a fiókok a virtuális gép felderítés és az ügynök telepítése, és készítse elő a helyszíni virtuális gépeket, hogy csatlakozhassanak Azure virtuális gépek a feladatátvételt követően.
> * **6. lépés: Replikálása virtuális gépek**: replikáció beállításainak konfigurálása, és engedélyezze a virtuális gép replikációját.
> * **7. lépés: Telepítse át a DMS adatbázis**: az adatbázis áttelepítés után.
> * **8. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése**: először futtassa a feladatátvételi teszt végrehajtásához ellenőrizze, hogy minden működik, telepítse át a virtuális gép teljes feladatátvétele követ.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>1. lépés: Felkészülés az SQL Server AlwaysOn rendelkezésre állási csoport fürt

Contoso szeretné előre tervezze meg az adatbázis az Azure-ban egy fürt üzembe helyezésével. Az egyéb adatbázisok majd használhatnak ehhez a fürthöz. 

- Az SQL Server virtuális gépen (a termelési erőforrások használt) ContosoRG erőforráscsoportban telepíti.
- Egyedi nevek mellett mindkét virtuális gépek ugyanazt a beállítást használja.
- A belső terheléselosztó lesz telepítve, a ContosoNetworkingRG (használt hálózati erőforrások).
- Virtuális gépek Windows Server 2016 működik az SQL Server 2017 Enterprise Edition. Contoso nem rendelkezik az operációs rendszer licencek, hogy lesz lemezkép az Azure piactéren, amely arra a saját Azure EA előfizetési licenc biztosít.

Íme, Contoso hogyan állíthatja be a fürtöt:

1. Akkor hozzon létre két SQL Server virtuális gépen SQL Server 2017 vállalati Windows Server 2016-lemezkép kiválasztása az Azure piactéren. 

    ![SQL VIRTUÁLIS GÉP TERMÉKVÁLTOZAT](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. Az a **hozható létre virtuális gép varázsló** > **alapjai**, azok konfigurálása:

    - A virtuális gépek neveit: **SQLAOG1** és **SQLAOG2**.
    - Mivel a gépek üzleti szempontból kritikus fontosságú, lehetővé teszik az SSD a virtuális gép lemez típushoz.
    - Akkor adja meg a számítógép hitelesítő adatait.
    - Azok a központi telepítése a virtuális gépek az elsődleges USA keleti RÉGIÓJA 2 régió ContosoRG erőforráscsoportban.

3. A **mérete**, mindkét virtuális gépek elindítja a D2s_V3 Termékváltozat. Lesz méretezés később szükség van.
4. A **beállítások**, akkor tegye a következőket:

    - Mivel a virtuális gépeken az alkalmazáshoz kritikus adatbázisok, felügyelt lemezek használata.
    - Ezek helyezni a gépek a termelési hálózat az USA keleti RÉGIÓJA 2 elsődleges régió (**VNET-termék-EUS2**), az adatbázis alhálózat (**termék-DB-EUS2**).
    - Akkor hozzon létre egy új rendelkezésre állási készlet: **SQLAOGAVSET**, két tartalék tartományok és öt frissítési tartományok.

    ![SQL VIRTUÁLIS GÉP](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. A **SQL Server-beállítások**, SQL-kapcsolat a virtuális hálózathoz (személyes) korlátozzák az alapértelmezett 1433-as port. A hitelesítési azok használhatja ugyanazokat a hitelesítő adatokat használnak a helyszínen (**contosoadmin**).

    ![SQL VIRTUÁLIS GÉP](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**További segítségre van szüksége?**

- [Segítség](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) SQL Server virtuális gép kiépítése.
- [További tudnivalók](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) virtuális gépek konfigurálása az SQL Server különböző Termékváltozatai.

## <a name="step-2-deploy-the-failover-cluster"></a>2. lépés: A feladatátvevő fürtök telepítése

Íme, Contoso hogyan állíthatja be a fürtöt:

1. Azok beállítása az Azure storage-fiók a felhő tanúsító nevében járhasson el.
2. Az SQL Server virtuális gépeket adnak hozzá az Active Directory-tartomány, a Contoso helyszíni adatközpontban.
3. Azok a fürt létrehozása az Azure-ban.
4. A felhő tanúsító konfigurálják.
5. Végül lehetővé teszik az SQL Always On rendelkezésre állási csoportok.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Felhő tanúsító a storage-fiók beállítása

A felhő tanú beállításához Contoso kell egy Azure Storage-fiókot, amely tartalmazza majd a fürt egyeztetési használt fájlját. Ugyanazt a tárfiókot több fürt tanúsító felhő beállításához használható. 

Contoso hoz létre egy tárfiókot az alábbiak szerint:

1. Akkor adja meg egy felismerhető nevet a fiókhoz (**contosocloudwitness**).
2. Egy általános általános fiókhoz, amely LRS telepíteni azokat.
3. Ezek a fiók egy harmadik régióban - déli középső Régiójában helyezze el. Ezek helyezze el az elsődleges és másodlagos régióban kívül, hogy a regionális hiba esetén elérhető marad.
4. Helyezze az erőforráscsoportban, amely tárolja az infrastruktúrához kapcsolódó erőforrások - **ContosoInfraRG**.

    ![Felhő tanúsító](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Ha hoznak létre a tárfiók elsődleges és másodlagos elérési kulcsok akkor jönnek létre. Az elsődleges elérési kulcsot a felhő tanúsító létrehozásához szükséges. A tárfiók neve megjelenik a kulcs > **hívóbetűk**.

    ![Hozzáférési kulcs](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>SQL Server virtuális gépek hozzáadása a Contoso tartományhoz

1. Contoso SQLAOG1 és SQLAOG2 hozzáadja a contoso.com tartományhoz.
2. Ezt követően az egyes virtuális gépek telepítése a Windows feladatátvételi fürtszolgáltatás és eszközeinek.

## <a name="set-up-the-cluster"></a>A fürt beállítása

Mielőtt beállítaná a fürt, a Contoso pillanatképet készít a minden egyes számítógép az operációsrendszer-lemezképet.

![Pillanatkép](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Ezt követően egy parancsfájlt, azok magánjellegét, hogy futnak a Windows feladatátvevő fürt létrehozásához.

    ![Fürt létrehozása](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. A fürt is létrehozták, akkor győződjön meg arról, hogy megjelenik-e a virtuális gépek fürtcsomópontokként.

     ![Fürt létrehozása](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>A felhő tanúsító konfigurálása

1. Contoso tanúsító a felhő használatával a **kvórum konfigurációs varázsló** a Feladatátvevőfürt-kezelőben.
2. A varázslóban, és melyik felhőalapú tanúsító a storage-fiók létrehozásához.
3. A felhő tanúsító konfigurálása után jelenik meg a Feladatátvevőfürt-kezelő beépülő modul.

    ![Felhő tanúsító](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>SQL Server Always On rendelkezésre állási csoportok engedélyezése

Contoso most engedélyezheti az Always On:

1. Az SQL Server Configuration Manager, lehetővé teszik az **AlwaysOn rendelkezésre állási csoportok** a a **SQL Server (MSSQLSERVER)** szolgáltatás.

    ![Engedélyezze az AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Azok a indítsa újra a szolgáltatást, a módosítások életbe léptetéséhez.

Az AlwaysOn rendelkezésre állási csoport, amely védeni fogja a SmartHotel adatbázis AlwaysOn engedélyezése, a Contoso állíthat be.

**További segítségre van szüksége?**

- [További információ a](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) felhő tanúsító és a storage-fiók beállítása az.
- [Az utasítások megtekintéséhez](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) a fürt és egy rendelkezésre állási csoport létrehozása.

## <a name="step-3-deploy-the-azure-load-balancer"></a>3. lépés: Az Azure Load Balancer központi telepítése

Contoso most szeretne telepíteni egy belső terheléselosztóhoz, amely a fürt csomópontjai előtt helyezkedik el. A load balancer sem figyeli a forgalmat, és átirányítja a megfelelő csomópontra.

![Terheléselosztás](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Akkor hoz létre a terheléselosztó a következőképpen:

1. Az Azure portál > **hálózati** > **terheléselosztó**, üzembe egy új belső terheléselosztó: **ILB-termék-DB-EUS2-SQLAOG**.
2. Azok az éles hálózati környezetben a terheléselosztó tegyen **VNET-termék-EUS2**, az adatbázis alhálózat **termék-DB-EUS2**.
3. Ezek olyan statikus IP-címet rendeljen hozzá: 10.245.40.100.
4. Hálózati elem, a terheléselosztó hálózati erőforráscsoportban telepítése **ContosoNetworkingRG**.

    ![Terheléselosztás](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

A belső terheléselosztó telepítése után Contoso kell beállítani. Akkor hozzon létre egy háttér címkészletet, állítson be egy állapotmintáihoz és konfiguráljon egy terheléselosztási szabályt.

### <a name="add-a-backend-pool"></a>Háttérkészlet hozzáadásával

A virtuális gépeket a fürt felé irányuló forgalom terjesztéséhez Contoso beállítása egy háttér címkészletet, amely tartalmazza a hálózati adapter IP-címét a terheléselosztó hálózati forgalmat fogadó virtuális gépekhez.

1. A terheléselosztási beállításokat a portálon, a Contoso háttérkészlet hozzáadásával: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. A készlet azokat társítani SQLAOGAVSET a rendelkezésre állási csoporthoz. A virtuális gépeket, a készlet (**SQLAOG1** és **SQLAOG2**) a készlethez hozzáadott.

    ![Háttérkészlet](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Contoso hoz létre egy állapotmintáihoz, hogy a terheléselosztó figyelheti az alkalmazások állapotát. A mintavétel dinamikusan eltávolítása vagy virtuális gépek terhelés terheléselosztó elforgatási, hogyan válaszoljanak állapotellenőrzést alapján.

Akkor hozzon létre a mintavétel az alábbiak szerint: 

1. A terheléselosztási beállításokat a portálon, a Contoso létrehoz egy állapotmintáihoz: **SQLAlwaysOnEndPointProbe**.
2. Maguk állítják be a mintavétel futó virtuális gépek 59999 TCP-port figyelésére.
3. Ezek megadni a 5 másodperc közötti mintavételek menüpontban, és a küszöbértéket, a 2. Ha két mintavételt meghibásodik, a virtuális gép akkor veszi figyelembe a nem megfelelő.

    ![Hálózatfigyelő](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>A load balancer forgalom fogadására konfigurálása


Most Contoso igények olyan terheléselosztó szabályhoz történő defins hogyan adatforgalom elosztása a virtuális gépekhez.

- Az előtér-IP-cím a bejövő forgalmat kezeli.
- A háttér IP-készlet fogadja a forgalmat.

Akkor hozzon létre a szabály az alábbiak szerint:

1. A terheléselosztási beállításokat a portálon, az új terheléselosztási szabály hozzáadása: **SQLAlwaysOnEndPointListener**.
2. Contoso beállítja egy előtér-figyelő a TCP 1433 bejövő SQL ügyfél forgalom fogadására.
3. Akkor adja meg a háttérkészletben mely forgalmat továbbítja, és a portot, amelyen virtuális gépek figyeli a forgalmat.
4. Contoso lehetővé teszi, hogy a lebegőpontos IP (közvetlen kiszolgálói válasz). Ez mindig szükség az SQL AlwaysOn.

    ![Hálózatfigyelő](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**További segítségre van szüksége?**

- [Áttekintés](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) Azure terheléselosztó.
- [További tudnivalók](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) terheléselosztó létrehozása.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>4. lépés: A Site Recovery szolgáltatás Azure előkészítése

Az alábbiakban a Contoso kell a Site Recovery üzembe Azure összetevőket:

- Egy Vnetet, amelyben virtuális gépek kerülnek, ha a feladatátvétel során létrehozása folyamatban.
- A replikált adatok tárolásához Azure storage-fiók. 
- Az Azure Recovery Services-tároló.

Ezek beállítása a következőképpen:

1.  Már létrehozott egy hálózatot/alhálózatot, hogy használhassák a Site Recovery contoso ha azok [az Azure-infrastruktúra telepített](contoso-migration-rehost-vm-sql-ag.md).

    - A SmartHotel alkalmazást éles alkalmazások, és az éles Azure-hálózathoz (VNET-termék-EUS2) WEBVM telepíti át az elsődleges régióban keleti US2.
    - WEBVM kerülnek, a ContosoRG erőforráscsoportban, éles erőforrások használt, és az éles alhálózat (termék-FE-EUS2).

2. A Contoso Azure storage-fiók (contosovmsacc20180528) az elsődleges régióban hoz létre.

    - Standard szintű tárolót, és LRS replikációs egy általános célú fiók használata.
    - A fióknak és a tárolónak ugyanabban a régióban kell lennie.

    ![Helyreállítási tárolási hely](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Fiókkal a hálózati és tárolási helyen, most létrehozzák a Recovery Services-tároló (**ContosoMigrationVault**), és helyezze el a a **ContosoFailoverRG** erőforráscsoport, az elsődleges régióban USA keleti régiója 2. régiója .

    ![Recovery Services-tároló](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Azure Site Recovery beállítását.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>4. lépés: A Site Recovery a helyszíni VMware előkészítése

Íme néhány Contoso előkészíti a helyszíni:

- A vCenter-kiszolgáló vagy vSphere ESXi-állomáson, automatizálhatja a virtuális gép felderítési fiókkal.
- Egy fiók, amely lehetővé teszi, hogy az automatikus telepíteni a mobilitási szolgáltatást a replikálni kívánt VMware virtuális gépeken.
- A helyszíni virtuális gép beállításait, hogy a Contoso a replikált Azure virtuális gép a feladatátvételt követően csatlakozni tudnak.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Virtuális gépek automatikus észlelését. 
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése.
- Szükség van legalább egy csak olvasási jogokat biztosító fiókra. Olyan műveletek, például a létrehozása és lemezek eltávolítása, és bekapcsolja a virtuális gépeket futtató fiók szükséges.

Contoso beállítja azt a fiókot az alábbiak szerint:

1. Contoso szerepet hoz létre a vCenter szinten.
2. Contoso majd rendel hozzá, hogy a szerepkört a szükséges engedélyekkel.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatást telepítenie kell minden egyes virtuális gépen.

- A Site Recovery végezhető műveletek az automatikus ügyfélleküldéses telepítést, az összetevő a virtuális gép számára engedélyezve van a replikáció.
- Egy fiók, amely a Site Recovery használatával is hozzáférhetnek a virtuális Gépet a leküldéses telepítés szükséges. Ezt a fiókot az Azure-konzolon a replikációs beállításához adja meg.
- A fiók tartományi vagy helyi, a virtuális Gépre telepítéséhez szükséges engedélyekkel rendelkező lehet.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátvétel után a Contoso Azure virtuális gépek csatlakozni szeretne. Ehhez az szükséges, akkor tegye a következőket áttelepítés előtt:

1. Az interneten keresztüli eléréshez azokat:

 - A feladatátvétel előtt a helyszíni virtuális gép RDP engedélyezése
 - Győződjön meg arról, hogy a TCP és UDP-szabályok hozzáadása történik meg a a **nyilvános** profil.
 - Ellenőrizze, hogy az engedélyezve van az RDP **Windows tűzfal** > **engedélyezett alkalmazások** minden profil esetében.
 
2. Telephelyek közötti VPN, keresztüli eléréshez azokat:

 - Engedélyezze az RDP-a helyi számítógépen.
 - Az RDP engedélyezése a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások**, a **tartomány és a saját** hálózatok.
 - Az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa a helyszíni virtuális Gépre való **OnlineAll**.

Emellett a feladatátvétel futtatásakor szükségük ellenőrizze a következőket:

- Nem lehet nincs függőben lévő Windows-frissítések a virtuális Gépre feladatátvétel kiváltása. Ha vannak, akkor nem fogja tudni jelentkezzen be a virtuális gép, csak a frissítés befejeződése után.
- A feladatátvétel után ellenőrizheti **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez. Ha ez sem működik, akkor győződjön meg arról, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) a mobilitási szolgáltatás leküldéses telepítéséhez fiók létrehozása.


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>5. lépés: A helyszíni virtuális gépek replikálása az Azure Site Recovery szolgáltatással

Mielőtt azok lefutnának a áttelepítése az Azure-ba, Contoso kell beállítani, és engedélyezze a replikálást.

### <a name="set-a-replication-goal"></a>A replikációs cél beállítása

1. A tárolóban, a tároló neve (ContosoVMVault) alatt, és, melyik replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúra előkészítése** .
2. Akkor adja meg, hogy a gép-e a helyszínen található, a VMware rendszerben fut, és az Azure-bA replikál.

    ![Replikációs cél](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Győződjön meg róla, központi telepítésének megtervezése

A folytatáshoz győződjön meg arról, hogy végzi a központi telepítésének megtervezése kiválasztásával kell **Igen, elvégeztem**. Ebben a forgatókönyvben a Contoso csak a virtuális gép áttelepítése, és nem kell központi telepítésének megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso be kell állítania a forrás környezetében. Ehhez az szükséges, azok az OVF-sablon letöltése és helyezzen vele üzembe a Site Recovery konfigurációs kiszolgáló magas rendelkezésre állásúként, helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló fut, akkor regisztrálja a tárolóban lévő állapottal.

A konfigurációs kiszolgáló futtatja összetevők száma:

- A konfigurációs kiszolgáló-összetevő, amely a helyszíni és az Azure közötti kommunikáció koordinálja, és adatreplikáció kezeli.
- A folyamatkiszolgáló, amelyek egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.

Contoso hajtsa végre ezeket a lépéseket az alábbiak szerint:


1. A tárolóban, az OVF sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Azok a sablon létrehozása és telepítése a virtuális gép VMware importálja.

    ![OVF-sablon](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. A virtuális gép első bekapcsolásakor elinduló be egy Windows Server 2016 telepítési élmény. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezése után ezek jelentkezzen be a virtuális gép rendszergazdaként. Első bejelentkezéskor a az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszköz azok adja meg a nevét a kiszolgáló regisztrálása a tárolóban lévő állapottal.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.

    ![Regisztráljon egy konfigurációs kiszolgálót](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Bejelentkeznek az a gép újra, és a konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.
9. A varázslóban akkor válassza a NIC-replikációs forgalom fogadására. Ez a beállítás nem módosítható, miután van konfigurálva.
10. Akkor válassza ki, az előfizetés, erőforráscsoport és tárolót, amelyben a konfigurációs kiszolgáló regisztrálásához.
        ![Tároló](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Azokat, majd töltse le és telepítse a MySQL-kiszolgáló és a VMWare PowerCLI. 
11. Ellenőrzés után azokat a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címe adható meg. Hagyja meg az alapértelmezett portot, és adja meg a vCenter-kiszolgáló rövid nevét.
12. Akkor adja meg, a fiók automatikus felderítéshez létrehozott és a hitelesítő adatait, amely automatikusan telepíteni a mobilitási szolgáltatás segítségével. A Windows-alapú gépek a fiókot kell a helyi rendszergazdai jogosultsága a virtuális gépeken.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Az Azure portálon, a regisztráció befejezését követően a Contoso dupla ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. 15 perc vagy több tarthat a felderítés. 
8. A Site Recovery ezután csatlakozik a megadott beállítások VMware-kiszolgálók, és felderíti a virtuális gépek.

### <a name="set-up-the-target"></a>A cél beállítása

Most Contoso megadja a cél replikációs beállításait.

1. A **infrastruktúra előkészítése** > **cél**, akkor adja meg a tároló beállításait.
2. A Site Recovery ellenőrzi, hogy nincs-e az Azure storage-fiók és a hálózat a megadott cél.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Nem, a Contoso egy replikációs házirendet hozhat létre.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozása és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pontok megőrzésének ideje**. Alapértelmezett 24 órás. Ez az érték határozza meg, hogy mennyi ideig a megőrzési időszak az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezett érték egy óra. Ezt az értéket adja meg, amellyel alkalmazáskonzisztens pillanatképeket jönnek létre.
 
        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs szabályzat társítása](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>A replikáció engedélyezése

Contoso most már elindíthatja WebVM replikálása.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** az adatforrás-beállítások, és melyik.
2. Utal, hogy azok szeretné engedélyezni a virtuális gépek, válassza ki a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Most akkor adja meg a cél beállításai, többek között az erőforráscsoportot és virtuális hálózat és a tárfiókot, amelyben a replikált adatokat fog tárolni.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso replikáció WebVM kiválasztása, ellenőrzi a replikációs házirendet, és lehetővé teszi, hogy a replikáció. A Site Recovery a a mobilitási szolgáltatás a virtuális Gépre telepíti, ha engedélyezve van a replikáció.
 
    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Azok a replikációs folyamatot a nyomon **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
5. A **Essentials** az Azure-portálon, a Contoso a struktúra látható a virtuális gépek replikálása Azure-bA.

    ![Infrastruktúra nézet](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**További segítségre van szüksége?**

- El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [vész-helyreállítási beállítása a helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhető el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [a konfigurációs kiszolgáló telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikáció beállításainak konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- További tudnivalók [replikáció](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>5. lépés: Az adatbázis áttelepítési Segéd (DMA-) telepítése

A Contoso Azure virtuális gép Ezzel telepíti át a SmartHotel adatbázis **SQLAOG1** a DMA használatával. Ezek beállítása DMA az alábbiak szerint:

1. Az eszköz letöltése a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) a helyszíni SQL Server virtuális géphez (**SQLVM**).
2. A telepítő (DownloadMigrationAssistant.msi) a virtuális gép számítógépen futnak.
3. Az a **Befejezés** lapot, és melyik **indítsa el a Microsoft adatok áttelepítési Segéd** a varázsló befejezése előtt.

## <a name="step-6-migrate-the-database-with-dma"></a>6. lépés:, Telepítse át az adatbázist a DMA

1. A DMA futásuk egy új áttelepítési - **SmartHotel**.
2. Akkor válassza ki a **server Céltípust** , **SQL Server Azure virtuális gépeken**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Az áttelepítés részleteit, hozzáadása **SQLVM** a forráskiszolgáló és **SQLAOG1** céljaként. Akkor adja meg az egyes hitelesítő adatait.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Ezek helyi megosztani az adatbázis és konfigurációs információt. Írási hozzáféréssel az SQL-szolgáltatásfióknak SQLVM és SQLAOG1 elérhetőnek kell lennie.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso kiválasztja a át kell telepíteni, és az áttelepítést elindító bejelentkezések. Már befejezte a DMA az áttelepítés sikeres jeleníti meg.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Ezek győződjön meg arról, hogy az adatbázis fut **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS a Contoso datacenter és az Azure közötti pont-pont VPN-kapcsolat a helyszíni SQL Server Virtuálisgép kapcsolódik, és majd áttelepíti az adatbázisban.

## <a name="step-7-protect-the-database"></a>7. lépés: Az adatbázis védelmét.

A futó alkalmazások adatbázissal **SQLAOG1**, Contoso most védelme az AlwaysOn rendelkezésre állási csoportokat használ. Konfigurálja az AlwaysOn az SQL Management Studio használatával, és rendeljen a Windows fürtszolgáltatása figyelő. 

### <a name="create-an-alwayson-availability-group"></a>Az AlwaysOn rendelkezésre állási csoport létrehozása

1. Az SQL Management Studióban, akkor kattintson a jobb gombbal a **mindig a magas rendelkezésre állású** elindítani a **új rendelkezésre állási csoport varázsló**.
2. A **beállítások megadása**, akkor a rendelkezésre állási csoport neve **SHAOG**. A **válasszon adatbázisok**, akkor válassza ki, hogy a SmartHotel adatbázist.

    ![AlwaysOn rendelkezésre állási csoport](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. A **meg replikák**, adja hozzá a két SQL-csomópont rendelkezésre állási másodpéldányok, és konfigurálja őket a szinkron véglegesítési automatikus feladatátvétel biztosítására.

     ![AlwaysOn rendelkezésre állási csoport](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. A csoport egy figyelő konfigurálják (**SHAOG**) és port. Az IP-cím, a belső terheléselosztó statikus IP-cím (10.245.40.100) meg van adva.

    ![AlwaysOn rendelkezésre állási csoport](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. A **adatszinkronizálás kiválasztása**, lehetővé teszik az automatikus összehangolása. Ezt a beállítást az SQL Server automatikusan hoz létre a másodlagos replikák minden adatbázis csoportjában így Contoso manuálisan biztonsági mentése és visszaállítása ezeket nem kell. Ellenőrzés után a rendelkezésre állási csoport jön létre.

    ![AlwaysOn rendelkezésre állási csoport](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso problémát hibába ütközött a csoport létrehozásakor. Nem használ Active Directory integrált Windows-védelmet, és így kell megadni az engedélyeket az SQL-bejelentkezési a Windows feladatátvevő fürt szerepkör létrehozásához.

    ![AlwaysOn rendelkezésre állási csoport](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. A csoport létrehozása után a Contoso láthatja azt az SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Adja meg a figyelő a fürtön

Utolsó lépése az SQL-telepítéssel beállításához a Contoso szerint a figyelő a fürtön állítja be a belső terheléselosztó, és elérhetővé teszi a figyelő online. Ehhez a parancsfájl használata.

![Fürt-figyelő](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Ellenőrizze

Minden beállítással, a Contoso most már rendelkezik egy funkcionális rendelkezésre állási csoport, amely az áttelepített adatbázist használ az Azure-ban. Ezt a belső terheléselosztó az SQL Management Studio csatlakozva azok ellenőrizheti.

![ILB csatlakozás](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**További segítségre van szüksége?**
- Létrehozásának elsajátítása egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) és [figyelő](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Manuálisan [a load balancer IP-cím használatának beállítása a fürt](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [További](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) létrehozása és SAS használatával kapcsolatban.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>8. lépés: A Site Recovery a virtuális gép áttelepítése

Futtassa egy gyors contoso feladatátvétel tesztelése, és telepítse át a virtuális gép.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatása segít biztosítani, hogy minden a várt módon működik az áttelepítés előtt. 

1. Contoso futtat egy feladatátvételi tesztet a legutóbbi pontnak idő (**legújabb feldolgozott**).
2. És melyik **gép leállítása a feladatátvétel megkezdése előtt**, hogy a hely helyreállítási művelet megkísérli indítására, a feladatátvétel előtt állítsa le a virtuális gép – forrásként. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Tesztelési feladatátvételi futtatják: 

    - Egy Előfeltételek ellenőrzése során, hogy az áttelepítéshez szükséges feltételek vannak érvényben.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

3. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure-portálon. Contoso ellenőrzi, hogy a virtuális gép a megfelelő, csatlakozik-e a megfelelő hálózati méretét, és fut-e. 
4. Annak ellenőrzése után, Contoso számolja fel a feladatátvétel után, és jegyezze fel, és mentse a megfigyelések. 

### <a name="run-a-failover"></a>Feladatátvétel futtatása

1. Miután meggyőződött arról, hogy a feladatátvételi tesztet működőképes-e a várt módon, a Contoso áttelepítés helyreállítási terv létrehozása, majd adja hozzá WEBVM a terv.

     ![Helyreállítási terv](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. A feladatátvétel a terv számítógépen futnak. Válassza ki a legutóbbi helyreállítási pontot, és adja meg, hogy a Site Recovery próbálkozzon az időt. a feladatátvétel előtt állítsa le a helyszíni virtuális gép.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. A feladatátvétel után azok győződjön meg arról, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure-portálon.

    ![Helyreállítási terv](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Annak ellenőrzése után a virtuális Gépet az Azure-ban, akkor az áttelepítési folyamat befejeződését, állítsa le a replikációt a virtuális gép, és állítsa le a Site Recovery számlázási a virtuális gép áttelepítésének befejezéséhez.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Frissítse a kapcsolati karakterlánc

Az áttelepítési folyamat utolsó lépése Contoso frissítse úgy, hogy az áttelepített adatbázis a SHAOG figyelő futó mutasson az alkalmazás a kapcsolati karakterláncot. Ez a konfiguráció az Azure-ban már futó WEBVM módosul.  Ez a konfiguráció az ASP-alkalmazás web.config található. 

1. Keresse meg a fájlban a következő C:\inetpub\SmartHotelWeb\web.config.  Módosítsa megfelelően a AOG teljes Tartománynevét a kiszolgáló nevét: shaog.contoso.com.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. A fájl frissítése, és mentse, azok újraindítása után a WEBVM IIS. Akkor ehhez használja az IISRESET /RESTART cmd parancssorból.
2. IIS újraindítása után, az alkalmazás most használja az adatbázis fut az SQL MI.


**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) futó feladatátvételi tesztet. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) Azure feladatátvétele.

## <a name="clean-up-after-migration"></a>Az áttelepítés után tisztítása

Az áttelepítés után a SmartHotel alkalmazást egy Azure virtuális gépen fut, és az Azure SQL-fürt a SmartHotel adatbázis található.

Most van szüksége a Contoso tisztítás lépések elvégzéséhez:  

- Távolítsa el a helyszíni virtuális gépek a vCenter-készlet.
- Távolítsa el a virtuális gépek helyi biztonsági mentési feladatok.
- Frissítse a virtuális gépek új helyeket és IP-címek megjeleníteni kívánt belső dokumentációt.
- Tekintse át minden olyan erőforrásnál, amely interakciót folytatni a leszerelt virtuális gépeket, és bármely vonatkozó beállítások vagy a dokumentációjával tükrözzék az új konfiguráció frissítése.
- Adja hozzá a két új virtuális gépet (SQLAOG1 SQLAOG2) rendszerek figyelése termelési hozzá kell adni.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban a Contoso kell teljesen üzemeltető, és az új infrastruktúra biztonságáról.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapata nem ellenőrzi az Azure virtuális gépek WEBVM, SQLAOG1 és SQLAOG2 biztonsági problémák meghatározásához. 

- Ezek tekintse át a hálózati biztonsági csoportokkal (NSG-k) a virtuális géphez való hozzáférést. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom teljen.
- Ezek figyelembe véve, az Azure Disk Encryption és KeyVault lemezen lévő adatok védelme.
- Ezek kell kiértékelni átlátható adattitkosítás (TDE), és a majd engedélyezni a SmartHotel adatbázis az új SQL-AOG futó. [További információk](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[További](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásokkal kapcsolatban.

### <a name="backups"></a>Biztonsági másolatok

Az adatok biztonsági mentésének WEBVM, SQLAOG1 és SQLAOG2 contoso lesz az Azure Backup szolgáltatással. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és pluszköltséggel optimalizálása

1. Contoso rendelkezik, azok WEBVM a meglévő licencelésből és fogja használni, az Azure hibrid juttatásra.  Ezek lesz alakítsa át a meglévő Azure virtuális gépeken, a díjszabás előnyeit.
2. Contoso által Cloudyn, Microsoft leányvállalata Azure költség felügyeletét teszi lehetővé. A többszörös felhő költsége-kezelési megoldás, amely segít használata és kezelése az Azure és más felhőalapú erőforrásokat is.  [További](https://docs.microsoft.com/azure/cost-management/overview) Azure költség-kezeléssel kapcsolatos. 

## <a name="conclusion"></a>Összegzés

Ebben a cikkben a Contoso rehosted az alkalmazás előtér virtuális gép áttelepítésével a Site Recovery szolgáltatás használatával az SmartHotel alkalmazást az Azure-ban. Az alkalmazás adatbázis át az Azure-ban kiépített SQL Server-fürtöt, és egy SQL Server AlwaysOn rendelkezésre állási csoport védi.

## <a name="next-steps"></a>További lépések

A sorozat következő cikkben azt kiderül, milyen Contoso áthelyezési szolgáltatás ügyfélszolgálati osTicket alkalmazásuk Linux rendszeren fut, és a MySQL-adatbázis telepítve.


