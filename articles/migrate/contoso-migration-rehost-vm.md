---
title: Contoso-alkalmazást, az Azure-beli virtuális gépek az Azure Site Recoveryvel való migrálás újratárolása |} A Microsoft Docs
description: Ismerje meg, hogyan egy helyszíni alkalmazás újratárolása, és a egy lift-and-shift migrálást az Azure-bA az Azure Site Recovery a helyszíni gépek migrálásra szolgáltatás.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: e152a95a392fd503e027591a4930fbeef744d6f4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009419"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso áttelepítési: egy a helyszíni alkalmazás Újratárolása az Azure virtuális gépek


Ez a cikk bemutatja, hogyan Contoso áthelyezi az alkalmazás virtuális gépek áttelepítése az Azure virtuális gépek által az Azure-ban, a helyszíni SmartHotel alkalmazást.


Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe n helyszíni erőforrásokhoz. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak, és idővel további cikkek adunk hozzá.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso alkalmazás rendelkező virtuális gépek értékeli a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési egy alkalmazást az Azure virtuális gépeken és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut egy lift-and-shift-migrálás az Azure-bA a helyszíni SmartHotel alkalmazás. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányra, használja a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken | Bemutatja, hogyan Contoso migrálása az Azure virtuális gépeire, a Site Recovery szolgáltatással az SmartHotel alkalmazás virtuális gépeit. | Ez a cikk.
[A cikk 6: Egy alkalmazást az Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoport áthelyezési](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. Contoso számára, hogy az alkalmazás virtuális gépeit és a Database Migration service, az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használja. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso hajtja végre a Linux osTicket alkalmazás lift-and-shift áttelepítés Azure virtuális gépekre, a Site Recovery | Elérhető
[A cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure Web Apps és az Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a TFS-t a vsts-ben](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a saját helyi Team Foundation Server (TFS) központi, migrálás, a Visual Studio Team Services (VSTS) az Azure-ban. | Elérhető
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető



Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel a VMware virtuális gépeket, az Azure-ban futó alkalmazást. Ha szeretné használni ezt az alkalmazást, azt biztosítja, nyílt forráskódú, és töltheti le a [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és ennek eredményeképpen nincs a helyszíni rendszerek és infrastruktúra nyomás.
- **Korlátozza a kockázati**: A SmartHotel alkalmazás alapvető fontosságú a Contoso vállalat. Szeretné áthelyezni az Azure-bA nulla kockázattal rendelkező.
- **Kiterjesztheti**: Contoso nem szeretné módosítani az alkalmazást. Egyszerűen csak szeretné, hogy stabil.


## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok segítségével meghatározhatja a leginkább megfelelő áttelepítési módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell teljesítmény ugyanazokat a lehetőségeket, mint jelenleg VMware-ben.  Az alkalmazás kritikus jelzéssel a felhőben, a helyszínen marad. 
- Nem a Contoso szeretné fektethet be ezt az alkalmazást.  Fontos, hogy az üzleti, de a jelenlegi formájában egyszerűen szeretnék biztonságosan áthelyezése a felhőbe.
- Contoso nem szeretné módosítani az alkalmazás a ops modellt. Szeretné mobiljelentésre annak a felhőben ugyanúgy, mint korábban.
- Nem a Contoso szeretné módosíthatja bármely alkalmazás funkcióit. Az alkalmazás csak a hely változik.

## <a name="proposed-architecture"></a>Javasolt architektúra

Itt van az aktuális környezet

- Az alkalmazás két virtuális gép között többszintű (**WEBVM** és **SQLVM**).
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió).
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.

![Forgatókönyv-architektúra](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Áttelepítési folyamat

Contoso Site recoveryvel Azure virtuális gépek áttelepítése az alkalmazás előtér- és adatbázis virtuális gépek:

- Első lépésként azok fog előkészítése Azure-összetevők beállítása a Site Recovery és a helyszíni VMware-infrastruktúra előkészítése.
- Már van azok [Azure-infrastruktúra](contoso-migration-infrastructure.md) helyen tudhatja, így azok csak hozzá kell adnia néhány Azure-összetevőket kifejezetten a Site Recovery.
- Az előkészített mindent elindíthatják a virtuális gépek replikálása.
– Miután a replikáció engedélyezve van és működik, azok áttelepítése a virtuális gép – Azure feladatátvétele.

![Áttelepítési folyamat](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a migrálás és vész-helyreállítási kezeli az Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-ba, során Azure Storage-díjat számítunk fel.  Azure virtuális gépek jönnek létre, és a költségek, amikor feladatátvételt hajt végre. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és díjszabás.


## <a name="prerequisites"></a>Előfeltételek

Itt látható, milyen meg (és Contoso) kell futtatni az ebben a forgatókönyvben.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetés során korai cikkek az oktatóanyag-sorozatban. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.<br/><br/> Ha részletesebb engedélyek van szüksége, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.<br/><br/> További tudnivalók a specifikus [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) a Site Recovery követelményeinek.
**Helyszíni kiszolgálók** | A helyszíni vCenter-kiszolgálók futnia kell egy 5.5-ös, 6.0-s vagy 6.5-ös verzió<br/><br/> ESXi-gazdagépek egy 5.5-ös, 6.0-s vagy 6.5-ös verzió fusson.<br/><br/> Egy vagy több VMware virtuális gépeken kell futtatnia az ESXi-gazdagépen.
**A helyszíni virtuális gépek** | Meg kell felelnie a virtuális gépek [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Készítse elő az Azure Site Recovery**: hoznak létre, amely tárolja a replikált adatok, és a egy Recovery Services-tároló Azure storage-fiókkal.
> * **2. lépés: A Site Recovery a helyszíni VMware előkészítése**: a virtuális gépek felderítése és az ügynök telepítési fiókokat készít elő, és készítse elő az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően.
> * **3. lépés: A gépek replikálása**: Beállítja a replikációt, és indítsa el a virtuális gépek replikálása az Azure storage-bA.
> * **4. lépés: A Site Recovery a virtuális gépek áttelepítése az**:, győződjön meg arról, hogy minden megfelelően működik, a feladatátvételi teszt futtatásához, és futtassa a teljes feladatátvételt az a virtuális gépek áttelepítése az Azure-bA.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>1. lépés: A Site Recovery szolgáltatással az Azure előkészítése

Az alábbiakban a Contoso cégnek szüksége van a virtuális gépek áttelepítése az Azure-bA az Azure-összetevők:

- Egy virtuális hálózatot, amelyben az Azure virtuális gépeket fogja tartalmazni, amikor a feladatátvétel során jönnek létre.
- A replikált adatok tárolásához Azure storage-fiókkal. 
- Az Azure Recovery Services-tárolóba.

Hogy ezek az alábbiak szerint állíthatja:

1. A Contoso már beállított egy hálózatot használhat, a Site Recovery amikor azok [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md)

    - A SmartHotel alkalmazás éles alkalmazások, és a virtuális gépek migrálása az Azure éles hálózati (VNET-ÉLES-EUS2) az elsődleges régióban USA keleti RÉGIÓJA 2.
    - Mindkét virtuális gép kerülnek az éles erőforrásait használt ContosoRG erőforráscsoport.
    - Az alkalmazás előtérbeli virtuális gép (WEBVM) során migrálja az előtérben levő alhálózathoz (ÉLES-FE-EUS2), az éles hálózati környezetben.
    - Az alkalmazás-adatbázis VM (SQLVM) az adatbázis-alhálózathoz (ÉLES-DB-EUS2), telepítse át az éles hálózati környezetben található.

2. Contoso az elsődleges régióban hoz létre egy Azure storage-fiók (contosovmsacc20180528).
    - A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
    - Egy általános célú fiók, és standard szintű storage, LRS-replikációval használnak. 

    ![Site Recovery-tároló](./media/contoso-migration-rehost-vm/asr-storage.png)

3. A helyen lévő hálózati és tárolási fiókkal Contoso most létrehoz egy Recovery Services-tároló (ContosoMigrationVault), és elhelyezi ContosoFailoverRG erőforráscsoportban az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Recovery Services-tároló](./media/contoso-migration-rehost-vm/asr-vault.png)

**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) beállítása az Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>2. lépés: A Site Recovery a helyszíni VMware előkészítése

Mi Contoso előkészíti a helyszíni:

- Egy fiók a vCenter-kiszolgáló vagy vSphere ESXi-gazdagép, virtuális gépek felderítésének automatizálásához.
- Egy fiók, amely lehetővé teszi, hogy a mobilitási szolgáltatás automatikus telepítését a VMware virtuális gépeken. 
- A helyszíni virtuális gép beállításait, úgy, hogy a Contoso az Azure a replikált virtuális gépek a feladatátvételt követően csatlakozhatnak.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. 
- Virtuális gépek replikálása, feladatátvétele és feladat-visszavétel vezénylésére.
- Szükség van legalább egy csak olvasási jogokat biztosító fiókra. A fiók műveletek, például a létrehozása és eltávolítása a lemezeket, és ne tudják bekapcsolni a virtuális gépek futtatásához képesnek kell lennie.

Contoso a következőképpen állítja be a fiók:

1. A vCenter-szinten szerepkör azok létrehozása.
2. Ezek a szerepkör hozzárendelése a szükséges engedélyekkel.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatás minden virtuális Géphez telepítve kell lennie.

- A Site Recovery a mobilitási szolgáltatás egy automatikus ügyfélleküldéses telepítés teheti meg, ha engedélyezve van a virtuális gép replikációja.
- Egy fiókra akkor szükség, hogy a Site Recovery férhessenek hozzá a virtuális gépek, a leküldéses telepítéshez. Ezt a fiókot adja meg, amikor a replikáció beállítása.
- A fiók lehet tartomány vagy helyi engedélyekkel a virtuális gépekre kíván telepíteni.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

A feladatátvételt követően a Contoso biztosítani szeretné az Azure virtuális géphez való kapcsolódásra. Ehhez, tegye a következőket áttelepítés előtt:

1. Az interneten keresztüli eléréshez azokat:

 - A feladatátvétel előtt a helyszíni virtuális gép RDP engedélyezése
 - Győződjön meg arról, hogy a TCP és UDP-szabályokat a adják a **nyilvános** profilt.
 - Ellenőrizze, hogy az RDP engedélyezve van-e **Windows tűzfal** > **engedélyezett alkalmazások** az összes profil számára.
 
2. Site-to-site VPN, keresztüli eléréshez azokat:

 - Engedélyezze az RDP a helyszíni gépen.
 - Az RDP engedélyezése a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások**, a **tartomány és privát** hálózatok.
 - Az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa a helyszíni virtuális gép **OnlineAll**.

Emellett a feladatátvétel futtatásakor szükségük ellenőrizze az alábbiakat:

- Lehetnek nincsenek függőben lévő Windows-frissítések a virtuális gép feladatátvétel indítása során. Ha vannak, be tud jelentkezni a virtuális Gépet a frissítés befejeződéséig nem.
- A feladatátvételt követően ellenőrizheti **rendszerindítási diagnosztika** , a virtuális gép képernyőképének megtekintéséhez. Ha ez sem működik, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**További segítségre van szüksége?**

- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.
- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) hozzon létre egy fiókot a mobilitási szolgáltatás leküldéses telepítéséhez.


## <a name="step-3-replicate-the-on-premises-vms"></a>3. lépés: A helyszíni virtuális gépek replikálása

Mielőtt azok lefutnának a migrálás az Azure-ba, a Contoso cégnek szüksége van, és engedélyezze a replikációt meg.

### <a name="set-a-replication-goal"></a>Állítsa be a replikációs cél

1. A tárolóban, a tároló nevét (ContosoVMVault) alatt, replikációs cél kiválasztása (**bevezetés** > **Site Recovery** > **infrastruktúra előkészítése** .
2. Akkor adja meg, hogy gépeik a helyszínen található, VMware-en futó, és az Azure-bA replikálja.

    ![Replikációs cél](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

A folytatáshoz, győződjön meg arról, hogy elvégezték-üzembe helyezés megtervezése, kiválasztásával **Igen, elvégeztem**. Ebben a forgatókönyvben a Contoso csak a két virtuális gépet telepít át, és nem kell az üzembe helyezés megtervezése.


### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet beállítása a Contoso cégnek szüksége van. Ehhez, egy OVF-sablon letöltése és használatával a Site Recovery konfigurációs kiszolgálónak a magas rendelkezésre állásúként telepíteni, a helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló üzembe helyezéséig, azok regisztrálja a tárolóban.

A konfigurációs kiszolgálón néhány összetevő fut:

- A konfigurációs kiszolgáló összetevő koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.



Contoso ezeket a lépéseket a következők szerint végzi el:

1. A tárolóban, az OVF-sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-vm/add-cs.png)

2. Ezek a sablon létrehozása és üzembe helyezése a virtuális gép VMware importálja.

    ![OVF-sablon](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  A virtuális gép első bekapcsolásakor, a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezését követően, jelentkezzen be rendszergazdaként a virtuális géphez. Első bejelentkezéskor az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszközben kell adnia egy nevet a konfigurációs kiszolgálót regisztrálja a tárolóban.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. A hitelesítő adatokat a tároló, amelyben azok fogja regisztrálni a konfigurációs kiszolgáló hozzáféréssel kell rendelkeznie.

    ![Konfigurációs kiszolgáló regisztrálása](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Bejelentkeznek a gép újra, és a konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.
9. A varázsló, válassza ki a hálózati Adaptert replikációs forgalom fogadására. Ez a beállítás a konfigurálás után nem módosítható.
10. Akkor válassza ki az előfizetést, erőforráscsoportot és a tároló, amelyben a konfigurációs kiszolgálót regisztrálja.
        ![Tároló](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Töltse le és telepítse a MySQL-kiszolgáló és a VMWare powercli-t. 
11. Ellenőrzést hogy a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címét adja meg. Hagyja bejelölve az alapértelmezett portot, és adjon meg egy rövid nevet a kiszolgáló az Azure-ban.
12. A fiók automatikus felderítéshez létrehozott és a hitelesítő adatok a mobilitási szolgáltatás automatikus telepítéséhez használt kell adnia. Windows-gépek a fióknak a virtuális gépek helyi rendszergazdai jogosultsággal kell rendelkeznie.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Az Azure Portalon, a regisztráció befejezését követően dupla Contoso ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a a **forrás** lap a tárolóban. 15 percig vagy tovább is tarthat a felderítés. 
8. Ezután a Site Recovery VMware-kiszolgálókat a megadott beállításokkal csatlakozik, és felderíti a virtuális gépeket.

### <a name="set-up-the-target"></a>A cél beállítása

Most már Contoso adja meg a cél replikálási beállításait.

1. A **infrastruktúra előkészítése** > **cél**, akkor válassza ki a célbeállítások.
2. A Site Recovery ellenőrzi, hogy nincs-e egy Azure storage-fiók és a hálózat a célként megadott helyen.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Most már a Contoso egy replikációs házirendet hozhat létre.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozás és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pont megőrzése**. Alapértelmezett 24 órányi. Ez az érték határozza meg, mennyi ideig őrzi az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezés szerint egy óra. Ez az érték, amellyel jönnek létre alkalmazáskonzisztens pillanatképek gyakorisága határozza meg.

        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-vm/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs házirend társítása](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>WEBVM a replikáció engedélyezése

Minden helyen, a Contoso engedélyezheti a virtuális gépek replikációját. A WebVM indítása.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** , válassza ki az adatforrás-beállítások.
2. Ezek azt jelzik, hogy szeretnének-e a virtuális gépek engedélyezéséhez válassza ki a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Akkor válassza ki a cél beállításai, többek között az erőforráscsoport és az Azure-hálózat és a storage-fiók.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Contoso kiválasztja **WebVM** a replikáció, ellenőrzi a replikációs házirendet, és lehetővé teszi a replikációt.

    - Ezen a ponton Contoso csak választja ki WEBVM, mert a virtuális hálózatot és alhálózatot kell kijelölni, és a Contoso helyezi az alkalmazást, virtuális gépek külön alhálózatokon.
    - Ha a replikáció engedélyezve van a Site Recovery automatikusan telepíti a mobilitási szolgáltatást a virtuális gépen.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. A replikáció állapotát nyomon követik **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure Portalon, a Contoso a struktúra látható a virtuális gépek replikálása Azure-bA.


### <a name="enable-replication-for-sqlvm"></a>Az SQLVM replikáció engedélyezése

Contoso most már megkezdheti a SQLVM gép replikálásához, a fenti ugyanilyen módon.

1. Akkor válassza ki az adatforrás-beállítások.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Majd adja meg a célként megadott beállításokat.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Replikáció kiválasztják SQLVM. 

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. A alkalmazni az azonos replikációs szabályzathoz WEBVM használt, és engedélyezze a replikációt.

    ![Infrastruktúra-nézet](./media/contoso-migration-rehost-vm/essentials.png)

**További segítségre van szüksége?**

- Tudjon meg egy teljes forgatókönyv az alábbi lépéseket a [vészhelyreállítás beállítása helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhetők el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [használt konfigurációs kiszolgáló telepítése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikációs beállítások konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- További információ [replikálást](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>4. lépés: A virtuális gépek áttelepítése 

Contoso fut egy gyors feladatátvételi tesztet, majd egy teljes feladatátvételt a virtuális gépek áttelepítéséhez.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt segítségével győződjön meg arról, hogy minden a várt módon működik. 

1. Contoso fut egy feladatátvételi tesztet a legújabb elérhető pontra idő (**legutóbb feldolgozott**).
2. Kiválasztják **gép leállítása a feladatátvétel megkezdése előtt**, hogy a Site Recovery megkísérli a forrásoldali virtuális gép leállítása a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Teszt feladatátvétel futtatása: 

    - Ellenőrizze, hogy az áttelepítéshez szükséges feltételek vannak érvényben lefuttatja az előfeltételek ellenőrzését.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.
    
3. A feladatátvétel befejezését követően a replika Azure virtuális gép megjelenik az Azure Portalon. Contoso ellenőrzi, hogy a virtuális gép mérete megfelelő, a megfelelő hálózathoz csatlakozik, és fut-e. 
4. Ellenőrizte, hogy a feladatátvételi tesztet, azokat a feladatátvételt, karbantartása és jegyezheti fel és mentheti kapcsolatos megfigyelések. 

### <a name="create-and-customize-a-recovery-plan"></a>Hozzon létre, és a helyreállítási terv testreszabása

 Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon dolgozni, a Contoso egy helyreállítási terv áttelepítése hoz létre. 

- A helyreállítási terv sorrendben adja meg a feladatátvételt akkor fordul elő, és azt jelzi, hogy Azure virtuális gépek kerül online az Azure-ban.
- Mivel az alkalmazás kétrétegű, akkor a helyreállítási terv testreszabása, hogy az adatokat a virtuális gép (SQLVM) az előtér (WEBVM) előtt elindul.

1. A **helyreállítási tervek (Site Recovery)** > **+ a helyreállítási terv**, hozzon létre egy csomagot, és a hozzá tud adni a virtuális gépeket.

    ![Helyreállítási terv](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Miután létrehozta a tervet, azok Testreszabás (**helyreállítási tervek** > **SmartHotelMigrationPlan** > **Testreszabás**).
2.  Azok eltávolítása a WEBVM **csoport 1: Start**.  Ez biztosítja, hogy az első indítási műveletet SQLVM érinti.
3.  A **+ csoport** > **adja hozzá a védett elemek**, WEBVM adnak hozzá a csoport 2: Indítsa el.  A virtuális gépeket kell két különféle csoportokba.


### <a name="migrate-the-vms"></a>A virtuális gépek áttelepítése


Most már a Contoso futtathatja a teljes feladatátvételt, az áttelepítés befejezéséhez.

1. Akkor válassza ki a helyreállítási terv > **feladatátvételi**.
2. Kiválasztásával átadja a feladatokat a legutóbbi helyreállítási pontot, és próbálja meg, hogy a Site Recovery a helyszíni virtuális gép leállítása a feladatátvétel indítása előtt. A feladatátvételi folyamatot követve a **feladatok** lapot.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover1.png)


3. A feladatátvétel után, győződjön meg arról, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure Portalon.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover2.png)  

3. Ellenőrzés után minden virtuális géphez az áttelepítés befejezésekor. A virtuális gép replikálását, és leáll, a Site Recovery-számlázását.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover3.png)

**További segítségre van szüksége?**

- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) feladatátvételi teszt futtatása. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) – Azure feladatátvétele.

## <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött a SmartHotel alkalmazás szinten futnak az Azure virtuális gépekhez.

Most már van szüksége a Contoso karbantartása a lépések elvégzéséhez:  

- Távolítsa el a WEBVM gép a vCenter-készlet.
- Az SQLVM gép eltávolítása a vCenter-készlet.
- Távolítsa el WEBVM és SQLVM helyi biztonsági mentési feladatok.
- Frissítés megjelenítése az új helyet, és az IP-címek a virtuális gépek belső dokumentációjában.
- Tekintse át az olyan erőforrások, a virtuális gépek kommunikáljanak, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az alkalmazás most már fut, Contoso most már teljes mértékben üzembe helyezése és kell biztonságossá tenni az Azure-ban.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapat áttekinti az Azure virtuális gépek minden olyan biztonsági problémák meghatározása érdekében.

- Hozzáférés szabályozásához, tekintse át a hálózati biztonsági csoportok (NSG-k) a virtuális gépek. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom elérhetővé válik.
- Akkor is vegye figyelembe a lemezen, KeyVault és az Azure Disk Encryption használatával az adatok védelme.

[További információ](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásairól.

### <a name="backups"></a>Biztonsági másolatok

Contoso adatok biztonsági mentéséhez a virtuális gépeken az Azure Backup szolgáltatás használatával történik. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

1. Contoso rendelkezik a virtuális gépek meglévő licencelésből, és használja az Azure Hybrid Benefit.  A meglévő Azure virtuális gépek, a díjszabás előnyeinek kihasználása érdekében konvertálja azokat.
2. Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Fontos, hogy az Azure és egyéb felhőerőforrások kezelése, és a egy többfelhős költségkezelő felügyeleti megoldás. [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe. 

## <a name="conclusion"></a>Összegzés

Ebben a cikkben a Contoso rehosted által az alkalmazás virtuális gépek áttelepítése az Azure virtuális gépeket a Site Recovery szolgáltatással az SmartHotel alkalmazást az Azure-ban. 


## <a name="next-steps"></a>További lépések

Az a [következő cikkre](contoso-migration-rehost-vm-sql-ag.md) a sorozatban megtudhatja, hogyan Contoso áthelyezi a SmartHotel alkalmazás előtérbeli virtuális gép egy Azure-beli virtuális gépen, és áttelepíti a az adatbázis egy SQL Server AlwaysOn rendelkezésre állási csoportot az Azure-ban.

