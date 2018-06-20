---
title: A Contoso Linux szolgáltatás ügyfélszolgálati alkalmazást az Azure és az Azure-beli MySQL áthelyezési |} Microsoft Docs
description: Ismerje meg, hogyan Contoso áthelyezi át kell telepítenie egy helyszíni Linux-alkalmazást úgy, hogy az Azure virtuális gépek és az Azure-beli MySQL.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 15a429c033cfd1598dd01b5c8cd2743c397dacdb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225517"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Contoso áttelepítése: az a helyi Linux alkalmazásoknak az Azure virtuális gépek és az Azure-beli MySQL áthelyezési

Ez a cikk bemutatja, hogyan Contoso vannak áthelyezését a helyszíni kétrétegű Linux szolgáltatás ügyfélszolgálati app (osTicket), át kell telepítenie, hogy az Azure és az Azure-beli MySQL.

Ez okumentumtípus egy sorozat része, amelyek megjelenítik a hogyan fiktív cég Contoso áttelepíti a Microsoft Azure felhőbe a helyszíni erőforrások cikkek nyolcadik. Az adatsorozat tartalmaz háttér-információkat, és a szolgáltatásokat, amelyek bemutatják, hogyan állítson be egy áttelepítési infrastruktúra, és futtassa az áttelepítések különböző típusú lehet. Forgatókönyvek nő összetettségét, és a következőkben hozzáadott további cikkek adott idő alatt.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[A következő cikket: 1: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
[3. cikk: A helyszíni erőforrások felmérése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési Azure virtuális gépek és a felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás webes VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
[Cikk 5: Az Azure virtuális gépek áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso át a SmartHotel Azure IaaS virtuális gépeket, a Site Recovery szolgáltatással.
[Cikk 6: Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok áthelyezési](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázis egy SQL Server rendelkezésre állási csoporthoz. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépen](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso áttelepíti a osTicket Linux alkalmazásuk Azure IaaS virtuális gépeket Azure Site Recovery segítségével.
8. cikk: Áthelyezési egy Linux alkalmazást az Azure virtuális gépek és az Azure-beli MySQL Server (Ez a cikk) | Bemutatja, hogyan Contoso áttelepíti a osTicket Linux-alkalmazást. Azure-beli MySQL Server-példány át a virtuális gép áttelepítése a Site Recovery, és a MySQL munkaterület használnak. | Elérhető

Ebben a cikkben a Contoso egy kétrétegű Linux Apache MySQL PHP (LÁMPA) szolgáltatás ügyfélszolgálati app (osTicket) áttelepíti az Azure-bA. Ha azt szeretné, a nyílt forráskódú alkalmazás, letöltheti a [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>A stratégiai célok

Az informatikai vezetőségi tagja szorosan együttműködik az üzleti partnerek megértéséhez szeretnék elérése:

- **Üzleti növekedési cím**: Contoso egyre, és ennek eredményeképpen nincs a helyszíni rendszer és az infrastruktúra nehezedő.
- **Kockázatának**: A szolgáltatás ügyfélszolgálati app fontos a Contoso vállalati. Helyezze át Azure nulla kockázattal szeretnék.
- **Kiterjesztése**: Contoso nem az alkalmazás most módosítani szeretne. Egyszerűen szeretnék stabil legyen.


## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhő team van rögzítve le az áttelepítés célokat annak meghatározására, a legjobb áttelepítési módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell azonos teljesítménybeli képességeinek mint jelenleg az helyszíni VMWare környezetben.  Az alkalmazás el kritikus jelzéssel a felhőben, mert az a helyszínen marad. 
- Contoso nem szeretné, hogy az alkalmazás beruházásának.  Fontos, hogy az üzleti tevékenységre vonatkozóan, de az aktuális képernyőn egyszerűen szeretnék biztonságosan helyezze át a felhőbe.
- Windows-alkalmazás áttelepítések néhány befejezését követően, a Contoso biztosítani szeretné a Linux-alapú infrastruktúra használata az Azure-ban.
- A Contoso biztosítani az adatbázis-felügyeleti feladatok minimalizálása érdekében, miután az alkalmazást áthelyezte a felhőbe.

## <a name="proposed-architecture"></a>Javasolt architektúrája

Ebben a forgatókönyvben:

- Az alkalmazás többszintű két virtuális gépet (OSTICKETWEB OSTICKETMYSQL) között.
- A virtuális gépek a VMware ESXi-állomáson lévő **contosohost1.contoso.com** (6.5-ös verziójú).
- A VMware-környezetben kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), a virtuális gép futó.
- Contoso rendelkezik egy olyan helyszíni adatközpontban (a contoso-adatközpontban), egy a helyi tartományvezérlővel (**contosodc1**).
- A webes réteg alkalmazást az OSTICKETWEB egy Azure IaaS virtuális Gépre telepíti át.
- Az alkalmazás adatbázis telepíti át az Azure-adatbázishoz MySQL PaaS szolgáltatáshoz.
- Azok amelybe migrálna egy éles munkaterhelés, mivel az erőforrások lesz-e a termelési erőforráscsoportban **ContosoRG**.
- Az erőforrások replikálja az elsődleges régióban (USA keleti régiója 2), és az éles hálózattól (VNET-termék-EUS2) helyezve:
    - A webkiszolgáló virtuális gép lesz a frontend alhálózathoz (termék-FE-EUS2) találhatók.
    - Az adatbázispéldány fölött létre kívánja hozni az adatbázis alhálózat (termék-DB-EUS2).
- Az alkalmazás adatbázis Azure MySQL MySQL eszközökkel telepíti át.
- A helyszíni virtuális gépek a Contoso adatközpontban fog kell szerelni, az áttelepítés befejezése után.


![Forgatókönyv-architektúra](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>áttelepítési folyamat

Contoso befejezi az áttelepítési folyamat az alábbiak szerint:

A webkiszolgáló virtuális gép áttelepítése:

1. Első lépésként Contoso állít be az Azure és a helyszíni Site Recovery üzembe helyezéséhez szükséges infrastruktúrát.
2. Után az Azure és a helyszíni összetevőinek előkészítése, azok beállításához és engedélyezéséhez a web virtuális gép replikálása.
3. Miután replikációs fel és fut, a virtuális gép által az Azure-bA feladatátadás áttelepítés után.

Az adatbázis áttelepítéséhez:

1. Contoso látja el az Azure-ban a MySQL példánya.
2. MySQL-munkaterület beállítása, és készítsen biztonsági másolatot az adatbázisról helyileg.
3. Ezek ezután állítsa vissza az adatbázist a helyi biztonsági másolat az Azure-bA.

![áttelepítési folyamat](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a kezeli a áttelepítési és vészhelyreállítás Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-bA replikálás során Azure Storage díjak sem merülnek fel.  Azure virtuális gépek jönnek létre, és a függő díj terheli, feladatátvétel esetén. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és az árképzés terén.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Az adatbázis a nyílt forráskódú MySQL Server motoron alapul. Biztosít egy teljes körűen felügyelt, nagyvállalati használatra kész közösségi MySQL-adatbázis, az alkalmazások fejlesztésére és üzembe helyezés szolgáltatásként. 

 
## <a name="prerequisites"></a>Előfeltételek

Ha Ön (és a Contoso) futtatandó ebben a forgatókönyvben, itt található mit kell rendelkeznie.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetés korai cikkek során a sorozat. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha még nem a rendszergazda egy meglévő előfizetéshez használ, meg kell dolgoznia a rendszergazdától, hogy rendeljen Önhöz tulajdonosi vagy közreműködői engedélyekkel kell rendelkeznie.<br/><br/> Ha részletesebb engedélyekre van szükség, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastruktúra** | Az Azure-infrastruktúra beállítása, a Contoso [áttelepítése az Azure-infrastruktúra](contoso-migration-infrastructure.md).<br/><br/> További információ a konkrét [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) Site Recovery követelményei.
**Helyszíni kiszolgálók** | A helyszíni vCenter server 5.5, 6.0 vagy 6.5 kell futnia<br/><br/> Az ESXi-állomáson, 5.5, 6.0 vagy 6.5-ös verzióját<br/><br/> Egy vagy több VMware virtuális gépek az ESXi-állomáson futó.
**A helyszíni virtuális gépek** | [Tekintse át a Linux virtuális gép](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , amely a Site Recovery áttelepítése támogatott.<br/><br/> Győződjön meg arról támogatott [Linux fájl- és tárolási rendszerek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>A forgatókönyv lépései

Ez hogyan Azure befejezi az áttelepítést:

> [!div class="checklist"]
> * **1. lépés: Felkészülés Azure Site Recovery**: a replikált adatok tárolásához Azure storage-fiók létrehozása, és a Recovery Services-tároló létrehozása.
> * **2. lépés: A helyszíni VMware előkészítése a Site Recovery**: készítse elő a fiókok a virtuális gép felderítés és az ügynök telepítése, és készítse elő a feladatátvételt követően Azure virtuális gépeken való kapcsolódáshoz.
 * **3. lépés: az adatbázis létesítéséhez]**: Azure, az Azure-beli MySQL database egy példányának kiosztása.
> * **4. lépés: Replikálása virtuális gépek**: a Site Recovery forrása és célja a környezet beállításához, állítson be egy replikációs házirendet, és indítsa el a virtuális gépek replikálása az Azure storage.
> * **5. lépés: Az adatbázis áttelepítése**: áttelepítés a MySQL eszközök beállítása.
> * **6. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése**: Győződjön meg arról, hogy minden rendben működik-e a feladatátvételi teszt futtatása, és futtassa a teljes feladatátvételt az a virtuális gépek áttelepítése az Azure-bA.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>1. lépés: A Site Recovery szolgáltatás Azure előkészítése

A Contoso Azure összetevők néhány kell a Site Recovery:

- Egy virtuális hálózatot, amelyben a feladatátvételt találhatók az erőforrások (a Contoso fogja használni az üzemi virtuális hálózat már telepítve).
- Egy új Azure storage-fiók a replikált adatok tárolásához. 
- Az Azure Recovery Services-tároló.

A VNet során már létrehozott contoso [Azure infrastruktúra telepítése](contoso-migration-infrastructure.md), így azok csak hozzon létre egy tárfiók és tároló.


1. Contoso hoz létre egy Azure storage-fiók (**contosovmsacc20180528**) régióban USA keleti régiója 2.

    - A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
    - A Contoso egy általános célú fiókot, Standard szintű tárolót, és LRS replikációs használ.

    ![Helyreállítási tárolási hely](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Fiókkal a hálózati és tárolási helyen, a Contoso egy tároló (ContosoMigrationVault) hoz létre, és elhelyezi a a **ContosoFailoverRG** erőforráscsoport, az elsődleges régióban USA keleti régiója 2.

    ![Recovery Services-tároló](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Azure Site Recovery beállítását.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>2. lépés: A Site Recovery a helyszíni VMware előkészítése

Contoso előkészíti a helyszíni VMware-infrastruktúra az alábbiak szerint:

- Létrehoz egy fiókot a vCenter Server automatizálhatja a virtuális gép felderítése.
- Létrehoz egy fiókot, amely lehetővé teszi, hogy az automatikus telepíteni a mobilitási szolgáltatást a replikálni kívánt VMware virtuális gépeken.
- Előkészíti a helyszíni virtuális gépek, úgy, hogy csatlakozhassanak Azure virtuális gépeken, ha az áttelepítés után jönnek létre.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Virtuális gépek automatikus észlelését. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Olyan műveletek, például a létrehozása és lemezek eltávolítása, és bekapcsolja a virtuális gépeket futtató fiók szükséges.

Contoso beállítja azt a fiókot az alábbiak szerint:

1. Contoso szerepet hoz létre a vCenter szinten.
2. Contoso majd rendel hozzá, hogy a szerepkört a szükséges engedélyekkel.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatást telepítenie kell minden egyes virtuális gépen, amely a Contoso biztosítani szeretné áttelepíteni.

- A Site Recovery hajthatja végre az automatikus ügyfélleküldéses telepítést, az összetevő, ha engedélyezi a virtuális gépek replikálása.
- Automatikus telepítés. A Site Recovery a virtuális gép hozzáférési engedéllyel rendelkező fiók szükséges. 
- Fiókadatok vannak bemeneti replikálás a telepítés során. 
- A fiók lehet tartományi vagy helyi fiók, amíg a telepítési engedélyek rendelkezik.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátvétel az Azure-bA a Contoso biztosítani kell kapcsolódnia kell az Azure virtuális gépeken. Ehhez szükséges különböző dolgok állíthatók elvégzéséhez: 

- Az interneten keresztül eléréséhez lehetővé teszik a helyszíni Linux virtuális gép SSH az áttelepítés előtt.  Az Ubuntu ez hajthatók végre a következő parancsot: **Sudo apt-get ssh telepítése -y**.
- A feladatátvétel után ellenőrizze kell **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez.
- Ha ez sem működik, győződjön meg arról, hogy a virtuális gép fut, és tekintse át ezeket kell [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) a mobilitási szolgáltatás leküldéses telepítéséhez fiók létrehozása.


## <a name="step-3-provision-azure-database-for-mysql"></a>3. lépés: Az Azure adatbázis létesítéséhez a MySQL

Contoso kiosztja a MySQL adatbázis-példány az elsődleges régióban USA keleti régiója 2.

1. Az Azure portálon ezek MySQL erőforrás Azure-adatbázis létrehozása. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. A hozzáadása **contosoosticket** az Azure-adatbázis. Az adatbázis adnak hozzá az éles erőforráscsoport **ContosoRG**, és a hitelesítő adatok megadása.
3. A helyszíni MySQL-adatbázis verziója 5.7, így, és melyik kompatibilitás jelen verziójában. Az alapértelmezett méret, amely megfelel az adatbázis-követelmények használnak.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. A **biztonságimásolat-készítő redundancia beállítások**, Contoso választja ki a használandó **Georedundáns**. Ez a beállítás lehetővé teszi, hogy azokat a másodlagos központi amerikai régió adatbázis visszaállítása, nem tervezett kimaradás esetén. Ezt a beállítást csak akkor konfigurálható, az adatbázis kiépítésekor.

     ![Redundancia](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. Az a **VNET-termék-EUS2** hálózati > **szolgáltatás végpontjait**, adnak hozzá egy végpontot (adatbázis alhálózathoz) az SQL-szolgáltatás.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Miután hozzáadta az alhálózat, azok, amely lehetővé teszi a hozzáférést az adatbázis alhálózatból az éles hálózati környezetben a virtuális hálózati szabály létrehozása.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>4. lépés: A helyszíni virtuális gépek replikálása

Ahhoz, azok áttelepítheti a webkiszolgáló virtuális gép az Azure-ba, a Contoso állít be, és lehetővé teszi, hogy a replikáció.

### <a name="set-a-protection-goal"></a>Egy védelmi cél beállítása

1. A tárolóban, a tároló neve (ContosoVMVault) alatt maguk állítják be a replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúraelőkészítése**.
2. Azok a számítógépek a helyszínen található, hogy, hogy azok viselkedése VMware virtuális gépeket, és az Azure-bA replikálni kívánt adnia.

    ![Replikációs cél](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Győződjön meg róla, központi telepítésének megtervezése

Folytatja, akkor ellenőrizze, hogy azok már befejeződött üzembe helyezésének tervezése kiválasztásával **Igen, elvégeztem**. Contoso csak az ebben a forgatókönyvben egy virtuális gép áttelepítése, és nincs szüksége, központi telepítésének megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso be kell állítania a forrás környezetében. Ehhez az szükséges, azokat egy magas rendelkezésre állásúként, a Site Recovery konfigurációs kiszolgáló központi telepítése egy OVF-sablon használatával a helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló fut, akkor regisztrálja a tárolóban lévő állapottal.

A konfigurációs kiszolgáló futtatja összetevők száma:

- A konfigurációs kiszolgáló-összetevő, amely a helyszíni és az Azure közötti kommunikáció koordinálja, és adatreplikáció kezeli.
- A folyamatkiszolgáló, amelyek egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.

Contoso hajtsa végre ezeket a lépéseket az alábbiak szerint:


1. Az OVF sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importálja a sablont a VMware virtuális gép létrehozása, és telepítse a virtuális Gépet.

    ![OVF-sablon](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. A virtuális gép első bekapcsolásakor elinduló be egy Windows Server 2016 telepítési élmény. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezése után ezek jelentkezzen be a virtuális gép rendszergazdaként. Első bejelentkezéskor a az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszköz azok adja meg a nevét a kiszolgáló regisztrálása a tárolóban lévő állapottal.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz.
7. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. A hitelesítő adatokat a tárolóba, amelyben regisztrálják lesz a konfigurációs kiszolgáló hozzáféréssel kell rendelkeznie.

    ![Regisztráljon egy konfigurációs kiszolgálót](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
9. Bejelentkeznek az a gép újra, és a konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.
10. A varázslóban akkor válassza a NIC-replikációs forgalom fogadására. Ez a beállítás nem módosítható, miután van konfigurálva.
11. Akkor válassza ki, az előfizetés, erőforráscsoport és tárolót, amelyben a konfigurációs kiszolgáló regisztrálásához.

    ![Tároló](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Most töltse le, és a MySQL-kiszolgáló és a VMWare PowerCLI telepítése. 
13. Ellenőrzés után azokat a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címe adható meg. Hagyja meg az alapértelmezett portot, és adja meg a vCenter-kiszolgáló rövid nevét.
14. Ezek adjon meg a fiók automatikus felderítéshez létrehozott és a hitelesítő adatait, a Site Recovery automatikusan telepíteni a mobilitási szolgáltatást fogja használni. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Az Azure portálon, a regisztráció befejezését követően a Contoso ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. 15 perc vagy több tarthat a felderítés. 
15. Minden helyen, a Site Recovery VMware Server csatlakozik, és felderíti a virtuális gépek.

### <a name="set-up-the-target"></a>A cél beállítása

Most már a Contoso bemenetek replikációs beállítások célként.

1. A **infrastruktúra előkészítése** > **cél**, akkor adja meg a tároló beállításait.
2. A Site Recovery ellenőrzi, hogy nincs-e az Azure storage-fiók és a hálózat a megadott cél.


### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

A forrás és cél beállítása, a Contoso készen áll a hozzon létre egy replikációs házirendet.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozása és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pontok megőrzésének ideje**. Alapértelmezett 24 órás. Ez az érték határozza meg, hogy mennyi ideig a megőrzési időszak az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezett érték egy óra. Ezt az értéket adja meg, amellyel alkalmazáskonzisztens pillanatképeket jönnek létre.
 
        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs szabályzat társítása](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**További segítségre van szüksége?**

- El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [vész-helyreállítási beállítása a helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhető el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [a konfigurációs kiszolgáló telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikáció beállításainak konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [További](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) kapcsolatos Linuxos Azure vendégügynökét.

### <a name="enable-replication-for-the-web-vm"></a>A webkiszolgáló virtuális gép replikáció engedélyezése

Most már a Contoso is replikálást indítani a **OSTICKETWEB** virtuális gép.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** az adatforrás-beállítások, és melyik.
2. Azt jelzik, hogy lehetővé teszik a virtuális gép, és válassza ki az adatforrás-beállítások, például a vCenter-kiszolgáló és a konfigurációs kiszolgáló szeretné.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Most azok adja meg a célként megadott beállításokat. Ezek közé tartoznak az erőforráscsoport és hálózati, amelyben az Azure virtuális Gépen található feladatátvétel után és a tárfiókot, amelyben a replikált adatokat fog tárolni. 

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso kiválasztja **OSTICKETWEB** replikációhoz. 

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. A virtuális gép tulajdonságai akkor jelölje ki a fiókot, amellyel automatikusan telepíteni a mobilitási szolgáltatást a virtuális Gépen.

     ![Mobilitási szolgáltatás](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. a **replikációs beállítások** > **replikáció beállításainak konfigurálása**, akkor ellenőrizze, hogy a megfelelő replikációs házirend alkalmazott, és jelölje be **Replikálásengedélyezési**. A mobilitási szolgáltatás automatikusan települ.
6.  Azok a replikációs folyamatot a nyomon **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.


**További segítségre van szüksége?**

El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [engedélyezze a replikálást](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>5. lépés: Az adatbázis áttelepítése

Contoso telepítse át az adatbázist biztonsági mentés és visszaállítás, a MySQL-eszközökkel. Ezek MySQL munkaterület telepítése, az adatbázis biztonsági mentése a OSTICKETMYSQL és majd annak visszaállítására Azure adatbázis MySQL-kiszolgáló.

### <a name="install-mysql-workbench"></a>A MySQL Workbench telepítése

1. Contoso ellenőrzi a [Előfeltételek és a letöltésekkel MySQL munkaterület](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. MySQL-munkaterület összhangban Windows telepítése a [telepítési utasításokat](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. MySQL-munkaterület azok OSTICKETMYSQL MySQL kapcsolat létrehozása. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Az adatbázisba, exportálja azokat **osticket**, helyi önálló fájlba.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Az adatbázis biztonsági helyileg, akkor létre kell hoznia kapcsolat az Azure-adatbázissal, MySQL-példányhoz.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Most Contoso (visszaállítás) az adatbázis az Azure-beli MySQL-példányban, az önálló a fájl importálásához. A példány jön létre egy új séma (osticket).

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>6. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése

Futtassa egy gyors contoso feladatátvétel tesztelése, és telepítse át a virtuális gép.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatása segítségével győződjön meg arról, hogy minden a várt módon működik, az áttelepítés előtt. 

1. Contoso futtat egy feladatátvételi tesztet a legutóbbi pontnak idő (**legújabb feldolgozott**).
2. És melyik **gép leállítása a feladatátvétel megkezdése előtt**, hogy a hely helyreállítási művelet megkísérli indítására, a feladatátvétel előtt állítsa le a virtuális gép – forrásként. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Tesztelési feladatátvételi futtatják: 

    - Egy Előfeltételek ellenőrzése során, hogy az áttelepítéshez szükséges feltételek vannak érvényben.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

3. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure-portálon. Contoso ellenőrzi, hogy a virtuális gép a megfelelő, csatlakozik-e a megfelelő hálózati méretét, és fut-e. 
4. Annak ellenőrzése után, azok számolja fel a feladatátvételt, és jegyezze fel, és mentse megfigyelések.

### <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

A virtuális gép áttelepítéséhez a Contoso hoz létre a virtuális Gépet tartalmazó helyreállítási tervet, ezért sikertelen a terv keresztül az Azure-bA.

1. Contoso terv létrehozása, és hozzáadja **OSTICKETWEB** rá.

    ![Helyreállítási terv](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. A feladatátvétel a terv számítógépen futnak. Válassza ki a legutóbbi helyreállítási pontot, és adja meg, hogy a Site Recovery próbálkozzon az időt. a feladatátvétel előtt állítsa le a helyszíni virtuális gép. Azt követve a feladatátvételi folyamat előrehaladásának a **feladatok** lap.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. A feladatátvételi vCenter-kiszolgáló leállítja a két virtuális gépeket az ESXi-állomáson futó parancsokat ad ki.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. A feladatátvétel után a Contoso ellenőrzi, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure-portálon.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Miután ellenőrizte a virtuális Gépet, akkor az áttelepítéshez. Leállítja a virtuális gép replikációjának, és leállítja a Site Recovery számlázási a virtuális gép számára.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) futó feladatátvételi tesztet. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) Azure feladatátvétele.


### <a name="connect-the-vm-to-the-database"></a>A virtuális gép kapcsolódni az adatbázishoz

Az áttelepítési folyamat utolsó lépése Contoso frissítse a kapcsolati karakterlánc az alkalmazás az MySQL mutasson az Azure-adatbázishoz. 

1. Akkor az SSH-kapcsolat a OSTICKETWEB VM a Putty használatával vagy egy másik SSH-ügyfél. A virtuális gép saját, így a magánhálózati IP-cím használatával kapcsolódnak.

    ![Csatlakozás adatbázishoz](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Csatlakozás adatbázishoz](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Ezek a beállítások frissítése, hogy a **OSTICKETWEB** méretű képes kommunikálni a **OSTICKETMYSQL** adatbázis. A konfiguráció jelenleg a helyi IP-címmel 172.16.0.43 szoftveresen kötött.

    **A frissítés előtt**
    
    ![IP frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **A frissítés után**
    
    ![IP frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![IP frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Ezek indítsa újra a szolgáltatást, amely **systemctl indítsa újra a apache2**.

    ![Újraindítás](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Végül frissítse a DNS-rekordjainak **OSTICKETWEB**, a Contoso tartományvezérlők egyik.

    ![DNS frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Az áttelepítés után tisztítása

Az áttelepítés befejeződött a osTicket app rétegek Azure virtuális gépeken futnak.

Most a Contoso kell tegye a következőket: 
- Távolítsa el a VMware virtuális gépek a vCenter-készletből
- Távolítsa el a helyszíni virtuális gépek helyi biztonsági mentési feladatok.
- Frissítés belső dokumentáció jelenjen meg új helyeket és IP-címek. 
- Tekintse át minden olyan erőforrásnál, amely interakciót folytatni a helyszíni virtuális gépeket, és bármely vonatkozó beállítások vagy a dokumentációjával tükrözzék az új konfiguráció frissítése.
- A Contoso az Azure áttelepítése szolgáltatás függőségi leképezéssel segítségével mérheti fel a **OSTICKETWEB** VM az áttelepítéshez. Az ügynököt (Microsoft Monitoring Agent és területi Agent), akkor telepíteni erre a célra, a virtuális gép most távolítsa el.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az alkalmazás most már fut, a Contoso kell teljes üzemeltető, az új infrastruktúra biztonságos.

### <a name="security"></a>Biztonság

A Contoso biztonsági csoportjának tekintse át a virtuális gép és az adatbázis biztonsági problémák meghatározásához.

- Ezek tekintse át a hálózati biztonsági csoportok (NSG-k) a virtuális géphez való hozzáférést. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom teljen.
- Akkor fontolja meg a virtuális gép lemezeken lemeztitkosítás és Azure KeyVault használatával az adatok védelme.
- A virtuális gép és az adatbázis-példány közötti kommunikáció SSL nincs konfigurálva. Annak érdekében, hogy adatbázis-forgalom nem lehet megtámadott ehhez kell azokat.

[További](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásokkal kapcsolatban.

### <a name="backups"></a>Biztonsági másolatok

- Contoso készít biztonsági másolatot az adatokat a virtuális Gépre az Azure Backup szolgáltatással. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Nem kell konfigurálni az adatbázis biztonsági mentése. Azure MySQL-adatbázis a biztonsági mentések automatikusan létrehozza és eltárolja. Azok ki az adatbázis-georedundancia használja, ezért ezt a rugalmas és éles használatra kész.

### <a name="licensing-and-cost-optimization"></a>Licencelési és pluszköltséggel optimalizálása

- Erőforrások való telepítése után Contoso Azure címkék, azok során hozott döntések megfelelően hozzárendeli a [Azure-infrastruktúra](contoso-migration-infrastructure.md#set-up-tagging) központi telepítés.
- Nincsenek a Contoso Ubuntu kiszolgálók licencelési problémák.
- Contoso által Cloudyn, Microsoft leányvállalata Azure költség felügyeletét teszi lehetővé. A többszörös felhő költsége-kezelési megoldás, amely segít használata és kezelése az Azure és más felhőalapú erőforrásokat is.  [További](https://docs.microsoft.com/azure/cost-management/overview) Azure költség-kezeléssel kapcsolatos.


## <a name="next-steps"></a>További lépések

Ebben a forgatókönyvben azt bemutatta, a végső áthelyezési forgatókönyv, hogy a Contoso próbált. Az előtér osTicket alkalmazás helyszíni Linux virtuális gép áttelepítése egy Azure virtuális gépre, és az alkalmazás-adatbázis áttelepítése egy Azure-beli MySQL-példányon.

Az áttelepítés adatsorozat-kra vonatkozó következő készletét fogjuk mutatjuk be, hogyan Contoso végbe összetettebb áttelepítések, amelynek használata esetén az alkalmazás újrabontása, egyszerű növekedési és shift áttelepítések helyett.
