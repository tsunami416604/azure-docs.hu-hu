---
title: Contoso-alkalmazást, az Azure-beli virtuális gépek az Azure Site Recoveryvel való migrálás újratárolása |} A Microsoft Docs
description: Ismerje meg, hogyan áthelyezési egy helyszíni alkalmazást egy lift-and-shift áttelepítését a helyszíni gépek az Azure-ba, az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118391"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso áttelepítése: Helyszíni alkalmazás újbóli üzembe helyezése Azure-beli virtuális gépeken


Ez a cikk bemutatja, hogyan Contoso áthelyezi az alkalmazás virtuális gépek áttelepítése az Azure virtuális gépek által az Azure-ban, a helyszíni SmartHotel360 alkalmazást.


Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak. Idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
5. cikk: Alkalmazás áthelyezése Azure-beli virtuális gépre | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Ez a cikk
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel360 alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Elérhető
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek Azure Site Recovery használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejezése | Elérhető
[8. cikk: Egy Azure virtuális gépek és az Azure MySQL Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Azure Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Az Azure Web Apps és az Azure SQL database alkalmazás újrabontása](contoso-migration-refactor-web-app-sql.md) | A Contoso a SmartHotel360 alkalmazást áttelepíti az Azure Web Apps és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány, a Database Migration Assistant | Elérhető
[10. cikk: Az Azure Web Apps és az Azure MySQL egy Linux-alkalmazás újrabontása](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Elérhető
[11. cikk: Refaktorovat a TFS-t az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető


Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel360 a VMware virtuális gépeket, az Azure-ban futó alkalmazást. Ha szeretné használni ezt az alkalmazást, azt biztosítja, nyílt forráskódú, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és ennek eredményeképpen nincs a helyszíni rendszerek és infrastruktúra nyomás.
- **Korlátozza a kockázati**: A SmartHotel360 alkalmazást, a Contoso vállalat kritikus fontosságú. Az alkalmazás áthelyezése az Azure-bA nulla kockázattal rendelkező szeretné.
- **Kiterjesztheti**: Contoso nem szeretné, módosítsa az alkalmazást, de szeretné, hogy stabil.


## <a name="migration-goals"></a>Migrálási célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok segítségével meghatározhatja a leginkább megfelelő áttelepítési módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell teljesítmény ugyanazokat a lehetőségeket, mint jelenleg VMware-ben. Az alkalmazás kritikus jelzéssel a felhőben, a helyszínen marad.
- Nem a Contoso szeretné fektethet be ezt az alkalmazást. Fontos, hogy az üzleti, de a jelenlegi formájában Contoso egyszerűen szeretne biztonságosan áthelyezése a felhőbe.
- Contoso nem szeretné módosítani az alkalmazás a ops modellt. A Contoso szeretne mobiljelentésre annak a felhőben ugyanúgy, mint korábban.
- Nem a Contoso szeretné módosíthatja bármely alkalmazás funkcióit. Az alkalmazás csak a hely változik.


## <a name="solution-design"></a>Megoldásterv

Után rögzíthet célokat és követelményeket állapította meg, a Contoso tervez és a egy üzembehelyezési megoldással tekintse át, és azonosítja az áttelepítési folyamat, beleértve a Contoso az áttelepítés által használt Azure-szolgáltatások.

### <a name="current-app"></a>Aktuális alkalmazás

- Az alkalmazás két virtuális gép között többszintű (**WEBVM** és **SQLVM**).
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió).
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).

### <a name="proposed-architecture"></a>Javasolt architektúra

- Mivel az alkalmazás egy éles környezetbeli számítási feladatokra, az alkalmazás az Azure-beli virtuális gépek éles erőforráscsoportban ContosoRG helyezkednek el.
- Az alkalmazás virtuális gépeit fog kell áttelepíteni az elsődleges Azure-régióba (USA keleti RÉGIÓJA 2) és a termelési hálózat (VNET-ÉLES-EUS2) kerülnek.
- A webes előtérrendszer VM helyezkednek el az előtérbeli alhálózat (ÉLES-FE-EUS2) az éles hálózati környezetben.
- A virtuális gép adatbázis az adatbázis alhálózat (ÉLES-DB-EUS2) az éles hálózati környezetben helyezkednek el.
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.

![Forgatókönyv-architektúra](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>Adatbázis használata

A megoldástervezési folyamat részeként a Contoso volt az Azure SQL Database és az SQL Server szolgáltatás összehasonlítását. Az alábbi szempontokat lerövidítették úgy dönt, hogy látogassa meg az Azure IaaS virtuális gépen futó SQL Server:

- Az SQL Servert futtató Azure virtuális gép használatával úgy tűnik, hogy az optimális megoldást Contoso cégnek szüksége van, az operációs rendszer vagy az adatbázis-kiszolgáló testreszabásához, vagy ha érdemes elhelyezése és a külső alkalmazások futtatása ugyanazon a virtuális Gépen.
- Frissítési garanciával működő a jövőben Contoso is exchange kedvezményes díjszabást kínál a egy SQL Database felügyelt példányain használatával az Azure Hybrid Benefittel az SQL Server meglévő licenceinek. Felügyelt példány akár 30 %-os ez is mentheti.



### <a name="solution-review"></a>Megoldás áttekintése

Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Consideration** | **Részletek**
--- | ---
**Szakemberek számára** | Az alkalmazás virtuális gépek mindkét átkerül az Azure-bA sem kell módosítani, így egyszerű a migrálás.<br/><br/> Mivel a Contoso lift-and-shift mindkét alkalmazás virtuális gép nem használja, speciális konfigurációs vagy az áttelepítési eszközök nélkül az alkalmazás-adatbázis szükséges.<br/><br/> Contoso kihasználhatják a frissítési garanciával rendelkező, az Azure Hybrid Benefit használatával befektetése jelenti.<br/><br/> Contoso teljes hozzáférés az alkalmazás az Azure-beli virtuális gépek megtartja.
**Hátrányai** | WEBVM és SQLVM futnak a Windows Server 2008 R2. Az operációs rendszer egyes szerepkörök (2018 július) az Azure által támogatott. [További információk](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> A webes és az adatok szintek, amelyek szükségesek az alkalmazás feladatátvételt egyetlen pont marad.</br><br/> SQLVM fut az SQL Server 2008 R2, amely nem szerepel a alapvető technikai támogatása. Azonban támogatott az Azure virtuális gépek (2018 július). [További információk](https://support.microsoft.com/en-us/help/956893).<br/><br/> Contoso kell az alkalmazást az Azure virtuális gépeket támogató, nem pedig egy felügyelt szolgáltatás, például az Azure App Service és az Azure SQL-adatbázis áthelyezése a folytatáshoz.



### <a name="migration-process"></a>Áttelepítési folyamat

Contoso Site recoveryvel Azure virtuális gépek áttelepítése az alkalmazás előtér- és adatbázis virtuális gépek:

- Első lépésként a Contoso előkészíti és állít be Azure-összetevőket a Site Recoveryhez, és előkészíti a helyszíni VMware-infrastruktúra.
- Már van a [Azure-infrastruktúra](contoso-migration-infrastructure.md) helyen, ezért Contoso ugyanúgy kell kifejezetten a Site Recovery több Azure-összetevők hozzáadása.
- Minden előkészített, a Contoso megkezdheti a virtuális gépek replikálása.
- Miután a replikáció engedélyezve van és működik, Contoso telepítse át a virtuális gép által az Azure-bA feladatátvétele.

![Áttelepítési folyamat](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a migrálás és vész-helyreállítási kezeli az Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-ba, során Azure Storage-díjat számítunk fel. Azure virtuális gépek jönnek létre, és a költségek, amikor feladatátvételt hajt végre. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és díjszabás.


## <a name="prerequisites"></a>Előfeltételek

Íme a Contoso kell futtatni az ebben a forgatókönyvben.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | A Contoso egy korábbi cikkben, az oktatóanyag-sorozatban létrehozott előfizetések. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.<br/><br/> Ha részletesebb engedélyek van szüksége, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md).
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.<br/><br/> További tudnivalók a specifikus [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) a Site Recovery követelményeinek.
**Helyszíni kiszolgálók** | A helyszíni vCenter-kiszolgálók futnia kell egy 5.5-ös, 6.0-s vagy 6.5-ös verzió<br/><br/> ESXi-gazdagépek egy 5.5-ös, 6.0-s vagy 6.5-ös verzió fusson.<br/><br/> Egy vagy több VMware virtuális gépeken kell futtatnia az ESXi-gazdagépen.
**A helyszíni virtuális gépek** | Meg kell felelnie a virtuális gépek [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso rendszergazdák fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Készítse elő az Azure Site Recovery**: Akkor hozzon létre, amely tárolja a replikált adatok, és a egy Recovery Services-tároló Azure storage-fiókkal.
> * **2. lépés: A Site Recovery a helyszíni VMware előkészítése**: A virtuális gépek felderítése és az ügynök telepítési fiókokat készít elő, és készítse elő az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően.
> * **3. lépés: Virtuális gépek replikálása**: Beállítja a replikációt, és indítsa el a virtuális gépek replikálása az Azure storage-bA.
> * **4. lépés: A Site Recovery a virtuális gépek áttelepítése**: Győződjön meg arról, hogy minden megfelelően működik, a feladatátvételi teszt futtatásához, és futtassa a teljes feladatátvételt az a virtuális gépek áttelepítése az Azure-bA.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>1. lépés: A Site Recovery szolgáltatással az Azure előkészítése

Az alábbiakban a Contoso cégnek szüksége van a virtuális gépek áttelepítése az Azure-bA az Azure-összetevők:

- Egy virtuális hálózatot, amelyben az Azure virtuális gépeket fogja tartalmazni, amikor a feladatátvétel során jönnek létre.
- A replikált adatok tárolásához Azure storage-fiókkal.
- Az Azure Recovery Services-tárolóba.

Hogy ezek az alábbiak szerint állíthatja:

1. Állítsa be a hálózati – Contoso már beállított egy hálózatot, amely a Site Recovery amikor azok [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md)

    - A SmartHotel360 alkalmazás éles alkalmazások, és a virtuális gépek migrálása az Azure éles hálózati (VNET-ÉLES-EUS2) az elsődleges régióban USA keleti RÉGIÓJA 2.
    - Mindkét virtuális gép kerülnek az éles erőforrásait használt ContosoRG erőforráscsoport.
    - Az alkalmazás előtérbeli virtuális gép (WEBVM) során migrálja az előtérben levő alhálózathoz (ÉLES-FE-EUS2), az éles hálózati környezetben.
    - Az alkalmazás-adatbázis VM (SQLVM) az adatbázis-alhálózathoz (ÉLES-DB-EUS2), telepítse át az éles hálózati környezetben található.

2. Egy storage-fiók-Contoso beállítása az Azure storage-fiók (contosovmsacc20180528) az elsődleges régióban hoz létre.
   - A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
   - Egy általános célú fiók, és standard szintű storage, LRS-replikációval használnak.

     ![Site Recovery-tároló](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Egy tároló az a hálózat és tárfiók létrehozása helyben, a Contoso most létrehoz egy Recovery Services-tároló (ContosoMigrationVault), és elhelyezi a ContosoFailoverRG erőforráscsoportban az USA keleti RÉGIÓJA 2 elsődleges régióban.

    ![Recovery Services-tároló](./media/contoso-migration-rehost-vm/asr-vault.png)

**További segítségre van szüksége?**

[Ismerje meg](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) beállítása az Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>2. lépés: A Site Recovery a helyszíni VMware előkészítése

Mi Contoso előkészíti a helyszíni:

- Egy fiók a vCenter-kiszolgáló vagy vSphere ESXi-gazdagép, virtuális gépek felderítésének automatizálásához.
- Egy fiók, amely lehetővé teszi, hogy a mobilitási szolgáltatás automatikus telepítését a VMware virtuális gépeken.
- A helyszíni virtuális gép beállításait, úgy, hogy a Contoso az Azure a replikált virtuális gépek a feladatátvételt követően csatlakozhatnak.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése.
- Virtuális gépek replikálása, feladatátvétele és feladat-visszavétel vezénylésére.
- Szükség van legalább egy csak olvasási jogokat biztosító fiókra. A fiók műveletek, például a létrehozása és eltávolítása a lemezeket, és ne tudják bekapcsolni a virtuális gépek futtatásához képesnek kell lennie.

Contoso-rendszergazdák az alábbiak szerint állíthatja a fiókja:

1. A vCenter-szinten szerepkör azok létrehozása.
2. Ezek a szerepkör hozzárendelése a szükséges engedélyekkel.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatás minden virtuális Géphez telepítve kell lennie.

- A Site Recovery a mobilitási szolgáltatás egy automatikus ügyfélleküldéses telepítés teheti meg, ha engedélyezve van a virtuális gép replikációja.
- Egy fiókra akkor szükség, hogy a Site Recovery férhessenek hozzá a virtuális gépek, a leküldéses telepítéshez. Ezt a fiókot adja meg, amikor a replikáció beállítása.
- A fiók lehet tartomány vagy helyi engedélyekkel a virtuális gépekre kíván telepíteni.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

A feladatátvételt követően a Contoso biztosítani szeretné az Azure virtuális géphez való kapcsolódásra. Ehhez a Contoso rendszergazdák tegye a következőket, áttelepítés előtt:

1. Az interneten keresztüli eléréshez azokat:

   - Engedélyezze az RDP-t a helyszíni virtuális Gépet a feladatátvétel előtt.
   - Győződjön meg arról, hogy a TCP és UDP-szabályokat a adják a **nyilvános** profilt.
   - Ellenőrizze, hogy az RDP engedélyezve van-e **Windows tűzfal** > **engedélyezett alkalmazások** az összes profil számára.

2. Site-to-site VPN, keresztüli eléréshez azokat:

   - Engedélyezze az RDP a helyszíni gépen.
   - Az RDP engedélyezése a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások**, a **tartomány és privát** hálózatok.
   - Az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa a helyszíni virtuális gép **OnlineAll**.

Emellett a feladatátvétel futtatásakor szükségük ellenőrizze az alábbiakat:

- Lehetnek nincsenek függőben lévő Windows-frissítések a virtuális gép feladatátvétel indítása során. Ha vannak, be tud jelentkezni a virtuális Gépet a frissítés befejeződéséig nem.
- A feladatátvételt követően ellenőrizheti **rendszerindítási diagnosztika** , a virtuális gép képernyőképének megtekintéséhez. Ha ez sem működik, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**További segítségre van szüksége?**

- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.
- [Ismerje meg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) hozzon létre egy fiókot a mobilitási szolgáltatás leküldéses telepítéséhez.


## <a name="step-3-replicate-the-on-premises-vms"></a>3. lépés: A helyszíni virtuális gépek replikálása

Contoso-rendszergazdák a migrálás futtatható az Azure-ba, azok kell beállítását, és engedélyezze a replikációt.

### <a name="set-a-replication-goal"></a>Állítsa be a replikációs cél

1. A tárolóban, a tároló nevét (ContosoVMVault) alatt, replikációs cél kiválasztása (**bevezetés** > **Site Recovery** > **infrastruktúra előkészítése** .
2. Akkor adja meg, hogy gépeik a helyszínen található, VMware-en futó, és az Azure-bA replikálja.

    ![Replikációs cél](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

A folytatáshoz, győződjön meg arról, hogy elvégezték-üzembe helyezés megtervezése, kiválasztásával **Igen, elvégeztem**. Ebben a forgatókönyvben a Contoso csak a két virtuális gépet telepít át, és nem kell az üzembe helyezés megtervezése.


### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso rendszergazdák kell konfigurálnia a forráskörnyezetet. Ehhez, egy OVF-sablon letöltése és használatával a Site Recovery konfigurációs kiszolgálónak a magas rendelkezésre állásúként telepíteni, a helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló üzembe helyezéséig, azok regisztrálja a tárolóban.

A konfigurációs kiszolgálón néhány összetevő fut:

- A konfigurációs kiszolgáló összetevő koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.



Contoso-rendszergazdák a következő tegye a következőket:

1. A tárolóban, az OVF-sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-vm/add-cs.png)

2. Ezek a sablon létrehozása és üzembe helyezése a virtuális gép VMware importálja.

    ![OVF-sablon](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. A virtuális gép első bekapcsolásakor, a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezését követően, jelentkezzen be rendszergazdaként a virtuális géphez. Első bejelentkezéskor az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszközben kell adnia egy nevet a konfigurációs kiszolgálót regisztrálja a tárolóban.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. A hitelesítő adatokat a tároló, amelyben azok fogja regisztrálni a konfigurációs kiszolgáló hozzáféréssel kell rendelkeznie.

    ![Konfigurációs kiszolgáló regisztrálása](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Bejelentkeznek a gép újra, és a konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.
9. A varázsló, válassza ki a hálózati Adaptert replikációs forgalom fogadására. Ez a beállítás a konfigurálás után nem módosítható.
10. Akkor válassza ki az előfizetést, erőforráscsoportot és a tároló, amelyben a konfigurációs kiszolgálót regisztrálja.
        ![vault](./media/contoso-migration-rehost-vm/cswiz1.png)

10. Töltse le és telepítse a MySQL-kiszolgáló és a VMWare powercli-t.
11. Ellenőrzést hogy a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címét adja meg. Hagyja bejelölve az alapértelmezett portot, és adjon meg egy rövid nevet a kiszolgáló az Azure-ban.
12. A fiók automatikus felderítéshez létrehozott és a hitelesítő adatok a mobilitási szolgáltatás automatikus telepítéséhez használt kell adnia. Windows-gépek a fióknak a virtuális gépek helyi rendszergazdai jogosultsággal kell rendelkeznie.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Az Azure Portalon, a regisztráció befejezését követően, dupla ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a a **forrás** lap a tárolóban. 15 percig vagy tovább is tarthat a felderítés.
8. Ezután a Site Recovery VMware-kiszolgálókat a megadott beállításokkal csatlakozik, és felderíti a virtuális gépeket.

### <a name="set-up-the-target"></a>A cél beállítása

Most már a Contoso rendszergazdák cél replikációs beállításainak megadása.

1. A **infrastruktúra előkészítése** > **cél**, akkor válassza ki a célbeállítások.
2. A Site Recovery ellenőrzi, hogy nincs-e egy Azure storage-fiók és a hálózat a célként megadott helyen.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Most már a Contoso rendszergazdák hozhatnak létre replikációs szabályzatot.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozás és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: Alapértelmezett érték 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pont megőrzése**. Alapértelmezett 24 órányi. Ez az érték határozza meg, mennyi ideig őrzi az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezés szerint egy óra. Ez az érték, amellyel jönnek létre alkalmazáskonzisztens pillanatképek gyakorisága határozza meg.

        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-vm/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.

    ![Replikációs házirend társítása](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>WEBVM a replikáció engedélyezése

Minden helyen, a Contoso-rendszergazdák mostantól engedélyezheti a virtuális gépek replikációját. A WebVM indítása.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** , válassza ki az adatforrás-beállítások.
2. Ezek azt jelzik, hogy szeretnének-e a virtuális gépek engedélyezéséhez válassza ki a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Akkor válassza ki a cél beállításai, többek között az erőforráscsoport és az Azure-hálózat és a storage-fiók.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Kiválasztják **WebVM** a replikáció, ellenőrizze a replikációs házirendet, és engedélyezze a replikációt.

   - Ebben a szakaszban azokat csak kiválasztja WEBVM mert virtuális hálózatot és alhálózatot kell kijelölni, és az alkalmazás virtuális gépek külön alhálózatokon kerülnek.
   - Ha a replikáció engedélyezve van a Site Recovery automatikusan telepíti a mobilitási szolgáltatást a virtuális gépen.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. A replikáció állapotát nyomon követik **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure Portalon meghatározhatják, hogy a struktúra a virtuális gépek replikálása Azure-bA.


### <a name="enable-replication-for-sqlvm"></a>Az SQLVM replikáció engedélyezése

Contoso rendszergazdák most már megkezdheti a SQLVM gép replikálásához, a fenti ugyanilyen módon.

1. Akkor válassza ki az adatforrás-beállítások.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Majd adja meg a célként megadott beállításokat.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Replikáció kiválasztják SQLVM.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. A alkalmazni az azonos replikációs szabályzathoz WEBVM használt, és engedélyezze a replikációt.

    ![Infrastruktúranézet](./media/contoso-migration-rehost-vm/essentials.png)

**További segítségre van szüksége?**

- Tudjon meg egy teljes forgatókönyv az alábbi lépéseket a [vészhelyreállítás beállítása helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhetők el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [használt konfigurációs kiszolgáló telepítése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikációs beállítások konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- További információ [replikálást](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>4. lépés: A virtuális gépek áttelepítése

Contoso rendszergazdák futtassa egy gyors feladatátvételi tesztet, majd egy teljes feladatátvételt a virtuális gépek áttelepítéséhez.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt segítségével győződjön meg arról, hogy minden a várt módon működik.

1. Ezek feladatátvételi teszt futtatása a legújabb elérhető pontra időben (**legutóbb feldolgozott**).
2. Kiválasztják **gép leállítása a feladatátvétel megkezdése előtt**, hogy a Site Recovery megkísérli a forrásoldali virtuális gép leállítása a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul.
3. Teszt feladatátvétel futtatása:

    - Ellenőrizze, hogy az áttelepítéshez szükséges feltételek vannak érvényben lefuttatja az előfeltételek ellenőrzését.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

3. A feladatátvétel befejezését követően a replika Azure virtuális gép megjelenik az Azure Portalon. Ellenőrizze, hogy a virtuális gép mérete megfelelő, a megfelelő hálózathoz csatlakozik, és fut-e.
4. Ellenőrizte, hogy a feladatátvételi tesztet, azokat a feladatátvételt, karbantartása és jegyezheti fel és mentheti kapcsolatos megfigyelések.

### <a name="create-and-customize-a-recovery-plan"></a>Hozzon létre, és a helyreállítási terv testreszabása

 Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon dolgozni, a Contoso-rendszergazdák az áttelepítéshez helyreállítási terv létrehozásához.

- A helyreállítási terv sorrendben adja meg a feladatátvételt akkor fordul elő, és azt jelzi, hogy Azure virtuális gépek kerül online az Azure-ban.
- Mivel az alkalmazás kétrétegű, akkor a helyreállítási terv testreszabása, hogy az adatokat a virtuális gép (SQLVM) az előtér (WEBVM) előtt elindul.

1. A **helyreállítási tervek (Site Recovery)** > **+ a helyreállítási terv**, hozzon létre egy csomagot, és a hozzá tud adni a virtuális gépeket.

    ![Helyreállítási terv](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Miután létrehozta a tervet, azok Testreszabás (**helyreállítási tervek** > **SmartHotelMigrationPlan** > **Testreszabás**).
2.  Azok eltávolítása a WEBVM **1. csoport: Indítsa el**. Ez biztosítja, hogy az első indítási műveletet SQLVM érinti.
3.  A **+ csoport** > **adja hozzá a védett elemek**, WEBVM adnak hozzá a 2. csoport: Indítsa el. A virtuális gépeket kell két különféle csoportokba.


### <a name="migrate-the-vms"></a>A virtuális gépek áttelepítése


Contoso-rendszergazdák mostantól az áttelepítés befejezéséhez teljes feladatátvétel futtatása.

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

Az áttelepítés befejeződött a SmartHotel360 alkalmazás szinten futnak az Azure virtuális gépekhez.

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

- Elérésének szabályozására, a csapat áttekinti a hálózati biztonsági csoportok (NSG-k) a virtuális gépek. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom elérhetővé válik.
- A csapat is vegye figyelembe a lemezen, KeyVault és az Azure Disk Encryption használatával az adatok védelme.

[További információ](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) virtuális gépek biztonsági eljárásairól.

## <a name="bcdr"></a>BCDR

Az üzletmenet-folytonossági és vészhelyreállítási (bcdr) funkciók a Contoso fogadja a következő műveleteket:

- Adatok biztonsága: Contoso biztonsági másolatot készít az adatok a virtuális gépeken az Azure Backup szolgáltatással. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Bízhatja alkalmazások: Contoso az alkalmazás az Azure-beli virtuális gépek Site Recovery használatával egy másodlagos régióba replikálja. [További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).



### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

1. Contoso rendelkezik a virtuális gépek meglévő licencelésből, és használja az Azure Hybrid Benefit. Contoso konvertálja a meglévő Azure virtuális gépek, a díjszabás előnyeinek kihasználása érdekében.
2. Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Fontos, hogy az Azure és egyéb felhőerőforrások kezelése, és a egy többfelhős költségkezelő felügyeleti megoldás. [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.

## <a name="conclusion"></a>Összegzés

Ez a cikk a Contoso az alkalmazás virtuális gépeit az Azure virtuális gépeket a Site Recovery szolgáltatással való migrálással rehosted a SmartHotel360 alkalmazást az Azure-ban.


## <a name="next-steps"></a>További lépések

Az a [következő cikkre](contoso-migration-rehost-vm-sql-ag.md) a sorozatban megtudhatja, hogyan Contoso áthelyezi a SmartHotel360 alkalmazás előtérbeli virtuális gép egy Azure-beli virtuális gépen, és áttelepíti a az adatbázis egy SQL Server AlwaysOn rendelkezésre állási csoportot az Azure-ban.

