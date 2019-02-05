---
title: A Contoso Linux szolgáltatás desk alkalmazás újratárolása az Azure és az Azure-beli MySQL |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso áthelyezi telepítse át a helyszíni Linuxos alkalmazások Azure-beli virtuális gépek és Azure-beli MySQL.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 3cac893fcaafd4fe8d35aab2a10da92019d3ed42
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698962"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Contoso áttelepítése: Helyszíni Linux-alkalmazás újbóli üzembe helyezése az Azure-beli virtuális gépeken és az Azure-beli MySQL-en

Ez a cikk bemutatja, hogyan Contoso áthelyezi telepítse át a helyszíni kétrétegű Linux service ügyfélszolgálati alkalmazás (osTicket), Azure és az Azure-beli MySQL azt.

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait. A sorozat tartalmazza a háttér-információkat, és a egy áttelepítési infrastruktúra beállítását, és futtassa a különböző típusú migrálások bemutató forgatókönyvek. Forgatókönyvek egyre összetettebbé válnak. Idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető   
[5. cikk: Az Azure virtuális gépein egy alkalmazás újratárolása](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel360 alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Elérhető 
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek Azure Site Recovery használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejezése | Elérhető
8. cikk: Egy Azure virtuális gépek és az Azure MySQL Linux alkalmazás újratárolása | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Azure Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Ez a cikk
[9. cikk: Az Azure Web Apps és az Azure SQL database alkalmazás újrabontása](contoso-migration-refactor-web-app-sql.md) | A Contoso a SmartHotel360 alkalmazást áttelepíti az Azure Web Apps és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány, a Database Migration Assistant | Elérhető
[10. cikk: Az Azure Web Apps és az Azure MySQL egy Linux-alkalmazás újrabontása](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Elérhető 
[11. cikk: Refaktorovat a TFS-t az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető


Ebben a cikkben a Contoso kétrétegű Linuxos Apache MySQL PHP (LAMP-) szolgáltatás desk alkalmazás (osTicket) áttelepíti az Azure-bA. Ha szeretné a nyílt forráskódú alkalmazás használja, letöltheti azt [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és ennek eredményeképpen nincs nyomást a helyszíni rendszerek és infrastruktúra.
- **Korlátozza a kockázati**: A szolgáltatás desk app, kritikus fontosságú üzleti. A Contoso biztosítani szeretné nulla kockázattal rendelkező Azure-bA áthelyezni.
- **Kiterjesztheti**:  Contoso nem szeretné az alkalmazást most módosíthatja. Azt szeretné, hogy az alkalmazás stabil egyszerűen.


## <a name="migration-goals"></a>Migrálási célok

A Contoso felhőalapú csapat rendelkezik rögzített az áttelepítés célok le annak érdekében, hogy a legmegfelelőbb migrálási módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell teljesítmény ugyanazokat a lehetőségeket, mint jelenleg helyszíni VMware környezetben.  Az alkalmazás kritikus jelzéssel a felhőben, a helyszínen marad. 
- Nem a Contoso szeretné fektethet be ezt az alkalmazást.  Fontos, hogy az üzleti, de a jelenlegi formájában Contoso egyszerűen át szeretné helyezni azt biztonságosan a felhőben.
- Windows app áttelepítések néhány befejezését követően, a Contoso biztosítani szeretné egy Linux-alapú infrastruktúra használata az Azure-ban.
- A Contoso biztosítani szeretné az adatbázis-rendszergazdai feladatok minimalizálása érdekében, az alkalmazás a felhőbe való áthelyezése után.

## <a name="proposed-architecture"></a>Javasolt architektúra

Ebben a forgatókönyvben:

- Az alkalmazás többszintű (OSTICKETWEB és OSTICKETMYSQL) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió).
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A webalkalmazás szint a OSTICKETWEB átkerülnek az Azure IaaS virtuális gép.
- Az alkalmazás-adatbázis lesz migrálva az Azure Database for MySQL PaaS-szolgáltatás.
- Mivel a Contoso egy éles környezetbeli számítási feladatokra végez áttelepítést, az erőforrások helyezkednek el az éles erőforráscsoportban **ContosoRG**.
- Az erőforrásokat az elsődleges régióban (USA keleti RÉGIÓJA 2), és elhelyezni a termelési hálózat (VNET-ÉLES-EUS2):
    - A webkiszolgáló virtuális gép az előtérben levő alhálózathoz (ÉLES-FE-EUS2) helyezkednek el.
    - Az adatbázis-példány helyezkednek el az adatbázis-alhálózat (ÉLES-DB-EUS2).
- Az alkalmazás-adatbázis MySQL eszközök használatával az Azure MySQL-hez lesznek áttelepítve.
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


![Forgatókönyv-architektúra](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Áttelepítési folyamat

Contoso befejezi az áttelepítési folyamat a következő:

A webkiszolgáló virtuális gép áttelepítéséhez:

1. Első lépésként Contoso állítja be az Azure és a helyszíni Site Recovery üzembe helyezéséhez szükséges infrastruktúrát.
2. Után az Azure és helyszíni összetevők előkészítése, a Contoso állít be, és lehetővé teszi a webkiszolgáló virtuális gép replikációját.
3. Miután replikációs felfelé és a futó, Contoso által átvitelét az Azure-bA migrálja a virtuális Gépet.

Az adatbázis áttelepítése:

1. Contoso látja el egy MySQL-példányt az Azure-ban.
2. Contoso állítja be a MySQL workbench, és helyileg az adatbázis biztonsági mentését.
3. Contoso majd állítsa vissza az adatbázist a helyi biztonsági másolat az Azure-bA.

![Áttelepítési folyamat](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a migrálás és vész-helyreállítási kezeli az Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-ba, során Azure Storage-díjat számítunk fel.  Azure virtuális gépek jönnek létre, és a költségek, amikor feladatátvételt hajt végre. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és díjszabás.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Az adatbázis a nyílt forráskódú MySQL-kiszolgáló-összetevőjére épül. Biztosít egy teljes körűen felügyelt, nagyvállalati szintű közösségi MySQL-adatbázis szolgáltatás alkalmazások fejlesztéséhez és üzembe helyezéséhez. 

 
## <a name="prerequisites"></a>Előfeltételek

Íme, mi Contoso cégnek szüksége van ehhez a forgatókönyvhöz.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Contoso előfizetések létrehozása közben egy korábbi cikkben. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.<br/><br/> Ha részletesebb engedélyek van szüksége, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastruktúra** | Az Azure-infrastruktúra beállítása, a Contoso [áttelepítése az Azure-infrastruktúra](contoso-migration-infrastructure.md).<br/><br/> További tudnivalók a specifikus [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) a Site Recovery követelményeinek.
**Helyszíni kiszolgálók** | A helyszíni vCenter-kiszolgálón kell futtatnia egy 5.5-ös, 6.0-s vagy 6.5-ös verzió<br/><br/> Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú ESXi-gazdagép<br/><br/> Egy vagy több futtató VMware virtuális gépeket az ESXi-gazdagépen.
**A helyszíni virtuális gépek** | [Tekintse át a Linux rendszerű virtuális gépek követelményeinek](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , amely a Site Recovery áttelepítése támogatott.<br/><br/> Győződjön meg arról támogatott [Linux-fájl- és tárolási rendszerek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso-rendszergazdák az áttelepítés elvégzéséhez:

> [!div class="checklist"]
> * **1. lépés: Készítse elő az Azure Site Recovery**: Akkor hozzon létre, amely a replikált adatokat tárolja, és hozzon létre egy Recovery Services-tároló Azure storage-fiókkal.
> * **2. lépés: A Site Recovery a helyszíni VMware előkészítése**: A virtuális gépek felderítése és az ügynök telepítési fiókokat készít elő, és készítse elő az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően.
 * **3. lépés: Az adatbázis kiépítése]**: Az Azure-ban akkor az Azure MySQL-adatbázis egy példányának kiosztása.
> * **4. lépés: Virtuális gépek replikálása**: Azok a Site Recovery forrás és cél-környezet konfigurálása, állítsa be a replikációs szabályzatot, és indítsa el a virtuális gépek replikálása az Azure storage-bA.
> * **5. lépés: Az adatbázis migrálása**: MySQL-eszközök a migrálást állítsa be.
> * **6. lépés: A Site Recovery a virtuális gépek áttelepítése**: Végül, győződjön meg arról, hogy minden megfelelően működik, a feladatátvételi teszt futtatásához, és futtassa a teljes feladatátvételt az a virtuális gépek áttelepítése az Azure-bA.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>1. lépés: A Site Recovery szolgáltatással az Azure előkészítése

Contoso cégnek szüksége van a több Azure-összetevőket a Site Recovery:

- Egy virtuális hálózatot, amelyben átadta a feladatait erőforrások találhatók. A virtuális hálózat során már létrehozott contoso [az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md)
- Új Azure storage-fiókot a replikált adatok tárolásához. 
- Az Azure Recovery Services-tárolóba.

A Contoso rendszergazdák hozzon létre egy tárfiókot, és a tároló az alábbiak szerint:

1. Ezek a storage-fiók létrehozása (**contosovmsacc20180528**) az USA keleti RÉGIÓJA 2 régióban.

    - A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
    - Egy általános célú fiók, és standard szintű storage, LRS-replikációval használnak.

    ![Site Recovery-tároló](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. A helyen lévő hálózati és tárolási fiókkal, hozzon létre egy tárolót (ContosoMigrationVault) és elhelyezheti a **ContosoFailoverRG** erőforráscsoport az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Recovery Services-tároló](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) beállítása az Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>2. lépés: A Site Recovery a helyszíni VMware előkészítése

Contoso rendszergazdák készítse elő a helyszíni VMware-infrastruktúra a következő:

- A vCenter-kiszolgáló virtuális gépek felderítésének automatizálásához a fiók létrehozását.
- Fiók, amely lehetővé teszi, hogy a mobilitási szolgáltatás automatikus telepítéséhez a replikálandó VMware virtuális gépek létrehozását.
- Azok a helyszíni virtuális gépeket, előkészítése, úgy, hogy csatlakozhassanak az Azure virtuális gépek a migrálás után létrehozott.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Futtathatja a műveletek, például a létrehozása és eltávolítása a lemezeket, és ne tudják bekapcsolni a virtuális gépek fiók szükséges.

Contoso-rendszergazdák az alábbiak szerint állíthatja a fiókja:

1. A vCenter-szinten szerepkör azok létrehozása.
2. Majd rendelje hozzá a szerepkört a szükséges engedélyekkel.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie az egyes virtuális Gépeken, amely a Contoso biztosítani szeretné áttelepíteni.

- A Site Recovery összetevő egy automatikus ügyfélleküldéses telepítés hajthatja végre, ha engedélyezi a virtuális gépek replikációját.
- Az automatikus telepítéshez. A Site Recovery a virtuális gép elérésére jogosult fiók szükséges. 
- Fiók adatai vannak bemeneti replikációs telepítés során. 
- A fiók lehet tartományi vagy helyi fiókot, amíg a telepítési engedélyek rendelkezik.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Az Azure-bA a feladatátvételt követően a Contoso biztosítani szeretné az Azure virtuális gépek csatlakozni tudnak. Ehhez a Contoso rendszergazdáknak kell a következőket:

- Hozzáférhet az interneten keresztül, lehetővé teszik a helyszíni linuxos virtuális gép SSH az áttelepítés előtt.  Ubuntu rendszerre készült ez segítségével kell végrehajtani a következő parancsot: **Sudo apt-get ssh telepítése -y**.
- A feladatátvétel után ellenőriznie kell **rendszerindítási diagnosztika** , a virtuális gép képernyőképének megtekintéséhez.
- Ha ez sem működik, győződjön meg arról, hogy a virtuális gép fut, és tekintse át a szükséges [hibaelhárítási tippek](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**További segítségre van szüksége?**

- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.
- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) hozzon létre egy fiókot a mobilitási szolgáltatás leküldéses telepítéséhez.


## <a name="step-3-provision-azure-database-for-mysql"></a>3. lépés: MySQL-hez készült Azure-adatbázis kiépítése

Contoso rendszergazdák egy MySQL-adatbázispéldányban az USA keleti RÉGIÓJA 2 elsődleges régióban üzembe helyezheti.

1. Az Azure Portalon, egy Azure Database for MySQL erőforrás létrehozásához. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Adnak hozzá a név **contosoosticket** az Azure-adatbázisban. Vegye fel az adatbázist az éles erőforráscsoport **ContosoRG**, és a hitelesítő adatok megadása.
3. A helyi MySQL-adatbázis verziója 5.7, így azok válassza ki a jelenlegi kompatibilitási verziót. Az alapértelmezett méretek, amely az adatbázis esetén is megbízhatóan használnak.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. A **biztonsági másolat Redundanciabeállításai**, akkor válassza ki a használandó **Georedundáns**. Ez a beállítás lehetővé teszi őket, állítsa vissza az adatbázist a másodlagos régióban USA középső RÉGIÓJA, kimaradás esetén. Ezt a beállítást csak akkor konfigurálható, az adatbázis kiépítésekor.

     ![Redundancia](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. Az a **VNET-ÉLES-EUS2** hálózati > **Szolgáltatásvégpontokat**, adnak hozzá egy végpontot (egy adatbázis-alhálózat) az SQL szolgáltatás.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Az alhálózat hozzáadása után azok létrehozása egy virtuális hálózati szabályt, amely lehetővé teszi, hogy az adatbázis-alhálózathoz való hozzáférés az éles hálózati környezetben.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>4. lépés: A helyszíni virtuális gépek replikálása

A webkiszolgáló virtuális gép áttelepítése, lehetséges, az Azure-ba, mielőtt Contoso rendszergazdák beállítása, és engedélyezze a replikációt.

### <a name="set-a-protection-goal"></a>Egy védelmi cél beállítása

1. A tárolóban, a tároló nevét (ContosoVMVault) alatt, állítsa be a replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúraelőkészítése**.
2. Akkor adja meg, hogy a gépek a helyszínen található, az, hogy azok VMware virtuális gépek és az Azure-bA replikálni kívánt.

    ![Replikációs cél](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

A folytatáshoz, győződjön meg arról, hogy azok befejezte üzembe helyezés megtervezése, kiválasztásával **Igen, elvégeztem**. Contoso csak az ebben a forgatókönyvben egyetlen virtuális gép áttelepítése, és nem kell az üzembe helyezés megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso-rendszergazdák mostantól konfigurálja a forráskörnyezetet. Ehhez az általuk üzembe helyezett egy magas rendelkezésre állásúként, a Site Recovery konfigurációs kiszolgáló OVF-sablon használatával a helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló üzembe helyezéséig, azok regisztrálja a tárolóban.

A konfigurációs kiszolgálón néhány összetevő fut:

- A konfigurációs kiszolgáló összetevő koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.

Contoso rendszergazdák ezt a következőképpen teheti:


1. Az OVF-sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importálja a VMware virtuális gép létrehozása, és telepítse a virtuális Gépet.

    ![OVF-sablon](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. A virtuális gép első bekapcsolásakor, a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezését követően, jelentkezzen be rendszergazdaként a virtuális géphez. Első bejelentkezéskor az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszközben kell adnia egy nevet a konfigurációs kiszolgáló regisztrálása a tárolóban használni.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz.
7. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. A hitelesítő adatokat a tároló, amelyben azok fogja regisztrálni a konfigurációs kiszolgáló hozzáféréssel kell rendelkeznie.

    ![Konfigurációs kiszolgáló regisztrálása](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
9. Bejelentkeznek a gép újra, és a konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.
10. A varázsló, válassza ki a hálózati Adaptert replikációs forgalom fogadására. Ez a beállítás a konfigurálás után nem módosítható.
11. Akkor válassza ki az előfizetést, erőforráscsoportot és a tároló, amelyben a konfigurációs kiszolgálót regisztrálja.

    ![Tároló](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Most töltse le, és a MySQL-kiszolgáló és a VMWare PowerCLI telepítése. 
13. Ellenőrzést hogy a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címét adja meg. Hagyja bejelölve az alapértelmezett portot, és adja meg a vCenter-kiszolgáló rövid nevét.
14. A fiók automatikus felderítéshez létrehozott és a hitelesítő adatokat, amelynek használatával a Site Recovery automatikusan telepíti a mobilitási szolgáltatást, adjon meg. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Az Azure Portalon, a regisztráció befejezését követően, ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a a **forrás** lap a tárolóban. 15 percig vagy tovább is tarthat a felderítés. 
15. Minden helyen, a Site Recovery csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket.

### <a name="set-up-the-target"></a>A cél beállítása

Most már a Contoso rendszergazdák bemeneti cél replikációs beállítások.

1. A **infrastruktúra előkészítése** > **cél**, akkor válassza ki a célbeállítások.
2. A Site Recovery ellenőrzi, hogy nincs-e egy Azure storage-fiók és a célként megadott hálózat.


### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

A forrás és cél állítsa be a Contoso rendszergazdák készen áll egy replikációs házirend létrehozása.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozás és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: Alapértelmezett érték 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pont megőrzése**. Alapértelmezett 24 órányi. Ez az érték határozza meg, mennyi ideig őrzi az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezés szerint egy óra. Ez az érték, amellyel jönnek létre alkalmazáskonzisztens pillanatképek gyakorisága határozza meg.
 
        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs házirend társítása](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**További segítségre van szüksége?**

- Tudjon meg egy teljes forgatókönyv az alábbi lépéseket a [vészhelyreállítás beállítása helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhetők el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [használt konfigurációs kiszolgáló telepítése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikációs beállítások konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [További](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) a Linuxhoz készült Azure Vendég ügynök kapcsolatban.

### <a name="enable-replication-for-the-web-vm"></a>A webkiszolgáló virtuális gép replikálásának engedélyezése

Most a Contoso-rendszergazdák is replikáljon az **OSTICKETWEB** virtuális Gépet.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** , válassza ki az adatforrás-beállítások.
2. Ezek azt jelzik, hogy szeretnének-e engedélyezni a virtuális gépeket, és válassza ki az adatforrás beállításai, többek között a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Most, adja meg a célként megadott beállításokat. Ezek közé tartozik, az erőforráscsoport és a hálózatot, amelyben az Azure virtuális Gépen található feladatátvétel után, és a tárfiókot, amely a replikált adatokat tárolni szeretné. 

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Kiválasztják **OSTICKETWEB** replikálásra. 

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. A virtuális gép tulajdonságai között, válassza ki a fiókot, amellyel automatikusan telepíteni a mobilitási szolgáltatás a virtuális Gépet.

     ![Mobilitási szolgáltatás](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. a **replikációs beállítások** > **replikációs beállítások konfigurálása**, akkor ellenőrizze, hogy a megfelelő replikációs szabályzat-e a alkalmazott, és válassza **Replikációengedélyezése**. A mobilitási szolgáltatás automatikusan települ.
6.  A replikáció állapotát nyomon követik **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.


**További segítségre van szüksége?**

Tudjon meg egy teljes forgatókönyv az alábbi lépéseket a [engedélyezze a replikációt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>5. lépés: Az adatbázis migrálása

Contoso rendszergazdák telepítse át az adatbázis biztonsági mentése és visszaállítása, MySQL eszközök használatával. Ezek a MySQL Workbench telepítése, az adatbázis biztonsági mentése a OSTICKETMYSQL és majd állítsa vissza az Azure Database for MySQL-kiszolgálót.

### <a name="install-mysql-workbench"></a>A MySQL Workbench telepítése

1. Ellenőrizze, hogy a [Előfeltételek és a letöltéseket a MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Windows-szolgáltatást a MySQL Workbench telepítése a [telepítési utasításokat](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. A MySQL Workbench akkor hozzon létre egy MySQL kapcsolatot OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Az adatbázis-kivitel **osticket**, önálló helyi fájlba.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Után az adatbázis biztonsági helyileg, azok hozzon létre egy kapcsolatot az Azure Database for MySQL-példányt.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Most azokat is importálja (helyreállíthatja) a az Azure-beli MySQL-példányát, az önálló adatbázis fájlt. A példány létrejön egy új sémát (osticket).

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>6. lépés: A Site Recovery a virtuális gépek áttelepítése

Végül Contoso rendszergazdák gyors futtatása a feladatátvételi teszt, és ezután migrálja a virtuális Gépet.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatása segítségével ellenőrizheti, hogy minden a várt módon működik, az áttelepítés előtt. 

1. Ezek feladatátvételi teszt futtatása a legújabb elérhető pontra időben (**legutóbb feldolgozott**).
2. Kiválasztják **gép leállítása a feladatátvétel megkezdése előtt**, hogy a Site Recovery megkísérli a forrásoldali virtuális gép leállítása a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Teszt feladatátvétel futtatása: 

    - Ellenőrizze, hogy az áttelepítéshez szükséges feltételek vannak érvényben lefuttatja az előfeltételek ellenőrzését.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

3. A feladatátvétel befejezését követően a replika Azure virtuális gép megjelenik az Azure Portalon. Ellenőrizze, hogy a virtuális gép a megfelelő méret, amely a megfelelő hálózathoz csatlakoztatva van és fut-e. 
4. Miután ellenőrizte, azok a feladatátvételt, karbantartása és jegyezheti fel és mentheti kapcsolatos megfigyelések.

### <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

A virtuális gép migrálásához, Contoso rendszergazdái a helyreállítási terv, amely tartalmazza a virtuális Gépet hoz létre, és a terv az Azure-bA.

1. Hozzon létre egy csomagot, és adja hozzá **OSTICKETWEB** rá.

    ![Helyreállítási terv](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Feladatátvétel a terv számítógépen futnak. Válassza ki a legutóbbi helyreállítási pontot, és adja meg, hogy a Site Recovery kísérelje meg a helyszíni virtuális gép leállítása a feladatátvétel indítása előtt. A feladatátvételi folyamatot követve a **feladatok** lapot.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. A feladatátvétel során a vCenter-kiszolgáló problémák állítsa le a két virtuális gép az ESXi-gazdagépen futó parancsokat.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. A feladatátvétel után, győződjön meg arról, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure Portalon.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Miután ellenőrizte a virtuális Gépet, azokat az áttelepítés befejezéséhez. Ez a virtuális gép replikálását, és leállítja a virtuális gép Site Recovery-számlázását.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**További segítségre van szüksége?**

- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) feladatátvételi teszt futtatása. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) – Azure feladatátvétele.


### <a name="connect-the-vm-to-the-database"></a>A virtuális gép kapcsolódni az adatbázishoz

Az áttelepítési folyamat utolsó lépését, mint a Contoso rendszergazdák frissítse a kapcsolati karakterláncot, az alkalmazás az Azure database for MySQL-hez mutasson. 

1. Győződjön meg arról, az SSH-kapcsolatot a OSTICKETWEB virtuális géphez a Putty használatával vagy egy másik SSH-ügyfelet. A virtuális gép nem nyilvános, így a privát IP-cím használatával csatlakoznak.

    ![Csatlakozás adatbázishoz](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Csatlakozás adatbázishoz](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Ezek a beállítások frissítése, hogy a **OSTICKETWEB** virtuális gépek kommunikálhatnak a **OSTICKETMYSQL** adatbázis. A konfiguráció jelenleg szoftveresen kötött IP-címmel a helyszíni 172.16.0.43.

    **A frissítés előtt**
    
    ![IP Update](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **A frissítés után**
    
    ![IP Update](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![IP Update](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. A szolgáltatás az újraindítás **systemctl indítsa újra az apache2**.

    ![Újraindítás](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Végül frissítse a DNS-rekordok **OSTICKETWEB**, a Contoso tartományvezérlők egyik.

    ![DNS frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött a osTicket alkalmazás szinten futnak, az Azure virtuális gépekhez.

Most már van szüksége a Contoso tegye a következőket: 
- Távolítsa el a VMware virtuális gépeket a vCenter-készlet
- Távolítsa el a helyszíni virtuális gépek helyi biztonsági mentési feladatok.
- Frissítés belső dokumentáció megjelenítése az új helyek és IP-címeket. 
- Tekintse át az olyan erőforrások, a helyszíni virtuális gépek kommunikáljanak, és frissítse a bármely vonatkozó beállítások vagy dokumentáció, hogy tükrözzék az új konfigurációt.
- Contoso használt az Azure Migrate szolgáltatás és függőségi leképezés felmérheti a **OSTICKETWEB** virtuális gép migrálásra. Ezek most távolítsa el az ügynököt (Microsoft Monitoring Agent/függőségi Agent), azokat telepíteni erre a célra, a virtuális gépről.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az alkalmazás most már fut a Contoso kell teljesen üzembe helyezése és biztonságos meg új infrastruktúrára.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapat tekintse át a virtuális gép és az adatbázis bármilyen biztonsági problémák meghatározása érdekében.

- Tekintse át a hálózati biztonsági csoportok (NSG-k) a virtuális géphez való hozzáférés szabályozása. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom adhat át.
- Vegye figyelembe, hogy a Virtuálisgép-lemezek lemeztitkosítás és az Azure KeyVault használata az adatok védelme.
- A virtuális gép és az adatbázis-példány közötti kommunikáció SSL-hez nincs konfigurálva. Akkor kell, hogy, hogy a nem lesznek-e az adatbázis-forgalom.

[További információ](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) virtuális gépek biztonsági eljárásairól.

### <a name="bcdr"></a>BCDR

Contoso üzletmenet-folytonosság és vészhelyreállítás, az alábbi műveleteket hajtja végre:

- **Biztonságban tudhatja**: Contoso biztonsági másolatot készít az adatok az alkalmazásban a virtuális gép az Azure Backup szolgáltatással. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nem szükséges az adatbázis biztonsági mentésének konfigurálásához. Azure Database for MySQL automatikusan létrehozza és tárolja a kiszolgáló biztonsági mentése. Kiválasztották, a georedundáns tárolás használata az adatbázis, így rugalmas és éles használatra kész.
- **Bízhatja alkalmazások**: Contoso az alkalmazás az Azure-beli virtuális gépek Site Recovery használatával egy másodlagos régióba replikálja. [További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Után üzembe helyezni erőforrásokat, a Contoso Azure címkéket, azok során hozott döntések megfelelően hozzárendeli a [Azure-infrastruktúra](contoso-migration-infrastructure.md#set-up-tagging) központi telepítés.
- Nem tartoznak licencelési problémák a Contoso Ubuntu-kiszolgálókhoz.
- Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.


## <a name="next-steps"></a>További lépések

Ebben a forgatókönyvben a végső áthelyezési forgatókönyv megmutattuk. Contoso át az előtérbeli virtuális gép a helyszíni Linuxos osTicket alkalmazás egy Azure virtuális Gépen, és az alkalmazás-adatbázis migrálása az Azure-beli MySQL-példányhoz.

Oktatóanyagok a migrálás sorozat következő készletét fogjuk mutatni, hogyan Contoso végzett áttelepítések, összetettebb készletét használata esetén az alkalmazás újrabontása, ahelyett, hogy egyszerű lift-and-shift-áttelepítések.
