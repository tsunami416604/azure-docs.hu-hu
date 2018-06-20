---
title: Áthelyezési át, és az Azure virtuális gépen a helyszíni Linux alkalmazás áthelyezési |} Microsoft Docs
description: Ismerje meg, hogyan Contoso áthelyezési Azure virtuális gépek áttelepítése révén a helyszíni Linux alkalmazás.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 8f039884ca0ea46c128078984d6cab6fd29ac9af
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220550"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Contoso áttelepítése: az Azure virtuális gépen a helyszíni Linux alkalmazás áthelyezési

Ez a cikk bemutatja, hogyan Contoso van áthelyezését a Linux-alapú szolgáltatás a helyi ügyfélszolgálati alkalmazás (**osTicket**), az Azure IaaS virtuális gépen.

Ez a dokumentum egy sorozat része, amely dokumentálja a hogyan fiktív cég Contoso áttelepíti a Microsoft Azure felhőbe a helyszíni erőforrások cikkek hetedik. Az adatsorozat háttér-információkat, és a forgatókönyvek olyan készlete, amelyek azt ábrázolja, hogyan állítson be egy áttelepítési infrastruktúra, és futtassa a különböző típusú áttelepítések tartalmaz. Forgatókönyvek nő összetettségét, és a következőkben hozzáadott további cikkek adott idő alatt.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[A következő cikket: 1: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
[3. cikk: A helyszíni erőforrások felmérése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési Azure virtuális gépek és a felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
[Cikk 5: Az Azure virtuális gépek áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso át SmartHotel alkalmazásuk csak a Site Recovery segítségével virtuális gépeket.
[Cikk 6: Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok áthelyezési](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázis egy SQL Server rendelkezésre állási csoporthoz. | Elérhető
7. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépen (Ez a cikk) | Bemutatja, hogyan Contoso áttelepíti a osService Linux-alkalmazásokhoz az Azure Site Recovery használatával.
[8. cikk: Áthelyezési egy Linux alkalmazást az Azure virtuális gépek és az Azure-beli MySQL kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti az osService Linux-alkalmazást, a virtuális gép áttelepítése a Site Recovery és MySQL munkaterület használatával történő áttelepítésének (Azure MySQL Server-példány. | Elérhető

Ebben a cikkben Contoso telepítse át a kétrétegű **osTicket** alkalmazást, és a Linux Apache MySQL PHP (LÁMPA) fut az Azure-bA. Az alkalmazás virtuális gépek áttelepíthetők az Azure Site Recovery szolgáltatással. Ha azt szeretné, a nyílt forráskódú alkalmazás, letöltheti a [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>A stratégiai célok

Az informatikai vezetőségi tagja szorosan együttműködik az üzleti partnerek megértéséhez válasszon ezek közül az áttelepítés számára:

- **Üzleti növekedési cím**: Contoso egyre, és ennek eredményeképpen nincs a helyszíni rendszer és az infrastruktúra nehezedő.
- **Kockázatának**: A szolgáltatás ügyfélszolgálati app fontos a Contoso vállalati. Helyezze át Azure nulla kockázattal szeretnék.
- **Kiterjesztése**: ezek nem szeretné, hogy az alkalmazás most módosítani. Egyszerűen szeretnék győződjön meg arról, hogy az állandó.


## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhő csapat az áttelepítés, a legjobb áttelepítési módszer meghatározásához célokat le van rögzítve:

- Az áttelepítés után az alkalmazás az Azure-ban kell azonos teljesítménybeli képességeinek mint jelenleg az helyszíni VMWare környezetben.  Az alkalmazás el kritikus jelzéssel a felhőben, mert az a helyszínen marad. 
- Contoso nem szeretné, hogy az alkalmazás beruházásának.  Fontos, hogy az üzleti tevékenységre vonatkozóan, de az aktuális képernyőn egyszerűen szeretnék biztonságosan helyezze át a felhőbe.
- Contoso nem szeretné, hogy az alkalmazás a ops modell módosításához. Szeretnék használni a felhőben, hogy most már ugyanúgy.
- Contoso nem szeretné módosítani az alkalmazások működését. Csak az alkalmazás helye módosul.
- Windows-alkalmazás áttelepítések néhány befejezését követően, a Contoso biztosítani szeretné a Linux-alapú infrastruktúra használata az Azure-ban.

## <a name="proposed-architecture"></a>Javasolt architektúrája

Ebben a forgatókönyvben:

- Az alkalmazás többszintű két virtuális gépet (OSTICKETWEB OSTICKETMYSQL) között.
- A virtuális gépek a VMware ESXi-állomáson lévő **contosohost1.contoso.com** (6.5-ös verziójú).
- A VMware-környezetben kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), a virtuális gép futó.
- Contoso rendelkezik egy helyszíni adatközpontot (**contoso-datacenter**), egy a helyi tartományvezérlővel (**contosodc1**).
- Mivel az alkalmazás egy éles munkaterhelés, a virtuális gépeket az Azure-ban legyen elhelyezve a termelési erőforráscsoportban **ContosoRG**.
- A virtuális gépeket a rendszer át az elsődleges régióban (USA keleti régiója 2), majd az éles hálózattól (VNET-termék-EUS2) helyezve:
    - A webkiszolgáló virtuális gép lesz a frontend alhálózathoz (termék-FE-EUS2) találhatók.
    - Az adatbázis VM létre kívánja hozni az adatbázis alhálózat (termék-DB-EUS2).
- A helyszíni virtuális gépek a Contoso adatközpontban fog kell szerelni, az áttelepítés befejezése után.

![Forgatókönyv-architektúra](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>áttelepítési folyamat

Contoso áttelepíti a következőképpen:

1. Első lépésként Contoso állít be az Azure és a helyszíni Site Recovery üzembe helyezéséhez szükséges infrastruktúrát.
2. Után előkészítése az Azure-és a helyszíni, akkor beállítása és a virtuális gépek replikálásának engedélyezése.
3. Miután a replikálás működik-e, a virtuális gépek feladatátadás őket az Azure által áttelepítés után.

![áttelepítési folyamat](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a kezeli a áttelepítési és vészhelyreállítás Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-bA replikálás során Azure Storage díjak sem merülnek fel.  Azure virtuális gépek jönnek létre, és a függő díj terheli, feladatátvétel esetén. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és az árképzés terén.

 
## <a name="prerequisites"></a>Előfeltételek

Ez milyen meg (és a Contoso) kell ehhez a forgatókönyvhöz.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetés korai cikkek során a sorozat. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha még nem a rendszergazda egy meglévő előfizetéshez használ, meg kell dolgoznia a rendszergazdától, hogy rendeljen Önhöz tulajdonosi vagy közreműködői engedélyekkel kell rendelkeznie.<br/><br/> Ha részletesebb engedélyekre van szükség, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastruktúra** | Az Azure-infrastruktúra beállítása, a Contoso [áttelepítése az Azure-infrastruktúra](contoso-migration-infrastructure.md).<br/><br/> További információ a konkrét [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) Site Recovery követelményei.
**Helyszíni kiszolgálók** | A helyszíni vCenter server 5.5, 6.0 vagy 6.5 kell futnia<br/><br/> Az ESXi-állomáson, 5.5, 6.0 vagy 6.5-ös verzióját<br/><br/> Egy vagy több VMware virtuális gépek az ESXi-állomáson futó.
**A helyszíni virtuális gépek** | [Tekintse át a Linux-gépek](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , amely a Site Recovery áttelepítése támogatott.<br/><br/> Győződjön meg arról támogatott [Linux fájl- és tárolási rendszerek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Meg kell felelnie a virtuális gépek [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>A forgatókönyv lépései

Ez hogyan Azure befejezi az áttelepítést:

> [!div class="checklist"]
> * **1. lépés: Felkészülés Azure Site Recovery**: a replikált adatok tárolásához Azure storage-fiók létrehozása, és a Recovery Services-tároló létrehozása.
> * **2. lépés: A helyszíni VMware előkészítése a Site Recovery**: készítse elő a virtuális gép felderítés és az ügynök telepítéséhez használandó fiókot, és készítse elő a feladatátvételt követően Azure virtuális gépeken való kapcsolódáshoz.
> * **3. lépés: Replikálása virtuális gépek**: azok a forrás és cél áttelepítési környezet kialakítása, hozzon létre egy replikációs házirendet, és indítsa el a virtuális gépek replikálása az Azure storage.
> * **4. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése**: Győződjön meg arról, hogy minden rendben működik-e a feladatátvételi teszt futtatása, és futtassa a teljes feladatátvételt a virtuális gépek áttelepítése az Azure-bA.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>1. lépés: A Site Recovery szolgáltatás Azure előkészítése

A Contoso Azure összetevők néhány kell a Site Recovery:

- Egy virtuális hálózatot, amelyben a feladatátvételt találhatók az erőforrások (a Contoso fogja használni az üzemi virtuális hálózat már telepített)
- Egy új Azure storage-fiók a replikált adatok tárolásához. 
- Az Azure Recovery Services-tároló.

A VNet során már létrehozott contoso [Azure infrastruktúra telepítése](contoso-migration-infrastructure.md), így azok csak hozzon létre egy tárfiók és tároló.

1. A Contoso Azure storage-fiók (contosovmsacc20180528) 2 USA keleti régiójában hoz létre.

    - A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
    - Standard szintű tárolót, és LRS replikációs egy általános célú fiók használatát.

    ![Helyreállítási tárolási hely](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Hálózati és tárolási helyen fiókhoz, Contoso hozzon létre egy tárolót (ContosoMigrationVault), és helyezze el a a **ContosoFailoverRG** erőforráscsoport, az elsődleges régióban USA keleti régiója 2.

    ![Recovery Services-tároló](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Azure Site Recovery beállítását.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>2. lépés: A Site Recovery a helyszíni VMware előkészítése

Contoso előkészíti a helyszíni VMware-infrastruktúra az alábbiak szerint:

- Hozzon létre egy fiókot a vCenter-kiszolgáló vagy vSphere ESXi-állomáson, automatizálhatja a virtuális gép felderítése.
- Hozzon létre egy fiókot, amely lehetővé teszi, hogy az automatikus telepíteni a mobilitási szolgáltatást a replikálni kívánt VMware virtuális gépeken.
- Készítse elő a helyszíni virtuális gépek, úgy, hogy csatlakozhassanak Azure virtuális gépeken, ha az áttelepítés után jönnek létre.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Virtuális gépek automatikus észlelését. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Olyan műveletek, például a létrehozása és lemezek eltávolítása, és bekapcsolja a virtuális gépeket futtató fiók szükséges.

Contoso beállítja azt a fiókot az alábbiak szerint:

1. Contoso szerepet hoz létre a vCenter szinten.
2. Contoso majd rendel hozzá, hogy a szerepkört a szükséges engedélyekkel.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatást telepíteni kell a Contoso áttelepítése a Linux virtuális gépek:

- A Site Recovery hajthatja végre az automatikus ügyfélleküldéses telepítést, az összetevő, ha engedélyezi a virtuális gépek replikálása.
- Az automatikus ügyfélleküldéses telepítést elő kell készíteni a Site Recovery a virtuális gépek elérésére használt fiókkal.
- Fiókok részletek vannak bemeneti replikálás a telepítés során. 
- A fiók lehet tartományi vagy helyi fiók, a virtuális gépek telepítéséhez szükséges engedélyekkel.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátvétel az Azure-bA a Contoso biztosítani kell kapcsolódnia kell az Azure-ban a replikált virtuális gépek. Ehhez nincs elolvashatja, azok kell tennie:

- Az interneten keresztül eléréséhez lehetővé teszik a helyszíni Linux virtuális gép SSH áttelepítés előtt.  Az Ubuntu ez hajthatók végre a következő parancsot: **Sudo apt-get ssh telepítése -y**.
- Miután az áttelepítés (feladatátvétel) futnak, akkor ellenőrizze **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez.
- Ha ez sem működik, akkor ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) létrehozása és hozzárendelése egy szerepkört a automatikus felderítése.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) a mobilitási szolgáltatás leküldéses telepítéséhez fiók létrehozása.


## <a name="step-3-replicate-the-on-premises-vms"></a>3. lépés: A helyszíni virtuális gépek replikálása

Ahhoz, azok áttelepítheti a webkiszolgáló virtuális gép az Azure-ba, a Contoso állít be, és lehetővé teszi, hogy a replikáció.

### <a name="set-a-protection-goal"></a>Egy védelmi cél beállítása

1. A tárolóban, a tároló neve (ContosoVMVault) alatt maguk állítják be a replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúraelőkészítése**.
2. Azok a számítógépek a helyszínen található, hogy, hogy azok viselkedése VMware virtuális gépeket, és az Azure-bA replikálni kívánt adnia.
    ![Replikációs cél](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Győződjön meg róla, központi telepítésének megtervezése

Folytatja, akkor ellenőrizze, hogy azok már befejeződött üzembe helyezésének tervezése kiválasztásával **Igen, elvégeztem**. Contoso csak az ebben a forgatókönyvben egy virtuális gép áttelepítése, és nincs szüksége, központi telepítésének megtervezése.

### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso be kell állítania a forrás környezetében. Ehhez az szükséges, azok az OVF-sablon letöltése és helyezzen vele üzembe a Site Recovery konfigurációs kiszolgáló magas rendelkezésre állásúként, helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló fut, akkor regisztrálja a tárolóban lévő állapottal.

A konfigurációs kiszolgáló futtatja összetevők száma:

- A konfigurációs kiszolgáló-összetevő, amely a helyszíni és az Azure közötti kommunikáció koordinálja, és adatreplikáció kezeli.
- A folyamatkiszolgáló, amelyek egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.

Contoso hajtsa végre ezeket a lépéseket az alábbiak szerint:

1. Az OVF sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Importálja a sablont a VMware virtuális gép létrehozása, és telepítse a virtuális Gépet.

    ![OVF-sablon](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. A virtuális gép első bekapcsolásakor elinduló be egy Windows Server 2016 telepítési élmény. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezése után ezek jelentkezzen be arra a virtuális gép rendszergazdaként. Első bejelentkezéskor a az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszköz azok adja meg a nevét a kiszolgáló regisztrálása a tárolóban lévő állapottal.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.

    ![Regisztráljon egy konfigurációs kiszolgálót](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Bejelentkeznek az a gép újra, és a konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.
9. A varázslóban akkor válassza a NIC-replikációs forgalom fogadására. Ez a beállítás nem módosítható, miután van konfigurálva.
10. Akkor válassza ki, az előfizetés, erőforráscsoport és tárolót, amelyben a konfigurációs kiszolgáló regisztrálásához.

    ![Tároló](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Azokat, majd töltse le és telepítse a MySQL-kiszolgáló és a VMWare PowerCLI. 
12. Ellenőrzés után azokat a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címe adható meg. Hagyja meg az alapértelmezett portot, és adja meg a vCenter-kiszolgáló rövid nevét.
13. Akkor adja meg a fiók automatikus felderítéshez létrehozott és a hitelesítő adatait, amelynek használatával automatikusan telepíteni a mobilitási szolgáltatást.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Az Azure portálon, a regisztráció befejezését követően a Contoso ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. 15 perc vagy több tarthat a felderítés. 
15. A Site Recovery ezután csatlakozik a VMware Server, és felderíti a virtuális gépek.

### <a name="set-up-the-target"></a>A cél beállítása

Most már a Contoso a cél replikációs beállítások konfigurálása.

1. A **infrastruktúra előkészítése** > **cél**, akkor adja meg a tároló beállításait.
2. A Site Recovery ellenőrzi, hogy nincs-e az Azure storage-fiók és a hálózat a megadott cél.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

A forrás és cél beállítását követően a Contoso készen áll a replikációs házirend létrehozásához.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozása és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pontok megőrzésének ideje**. Alapértelmezett 24 órás. Ez az érték határozza meg, hogy mennyi ideig a megőrzési időszak az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezett érték egy óra. Ezt az értéket adja meg, amellyel alkalmazáskonzisztens pillanatképeket jönnek létre.
 
        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs szabályzat társítása](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**További segítségre van szüksége?**

- El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [vész-helyreállítási beállítása a helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhető el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [a konfigurációs kiszolgáló telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikáció beállításainak konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [További](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) kapcsolatos Linuxos Azure vendégügynökét.

**További segítségre van szüksége?**

- El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [vész-helyreállítási beállítása a helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhető el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [a konfigurációs kiszolgáló telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikáció beállításainak konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [További](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) kapcsolatos Linuxos Azure vendégügynökét.



### <a name="enable-replication-for-osticketweb"></a>Engedélyezze a replikációját OSTICKETWEB

Most már a Contoso is replikálást indítani a **OSTICKETWEB** virtuális gép.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** az adatforrás-beállítások, és melyik.
2. Akkor válassza ki, hogy szeretnének-e virtuális gépek engedélyezéséhez jelölje be az adatforrás-beállítások, például a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Akkor adja meg a tárolóbeállítások, beleértve az erőforráscsoport és virtuális hálózatot, amelyben az Azure virtuális Gépen található feladatátvétel után, és a tárfiókot, amelyben a replikált adatokat fog tárolni.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Contoso kiválasztja **OSTICKETWEB** replikációhoz. 

    - Ebben a szakaszban Contoso választja ki csak **OSTICKETWEB** mert VNet és alhálózat meg kell adni, és a virtuális gép nem ugyanazon az alhálózaton.
    - A Site Recovery automatikusan telepíti a mobilitási szolgáltatást, ha a virtuális gép engedélyezve van a replikáció.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. A virtuális gép tulajdonságai, a Contoso választja ki a fiókot, amellyel a folyamatkiszolgáló automatikusan telepítse a mobilitási szolgáltatás a számítógépen.

     ![Mobilitási szolgáltatás](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. a **replikációs beállítások** > **replikáció beállításainak konfigurálása**, akkor ellenőrizze, hogy a megfelelő replikációs házirend alkalmazott, és jelölje be **Replikálásengedélyezési**.
6.  Azok a replikációs folyamatot a nyomon **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.



### <a name="enable-replication-for-osticketmysql"></a>Engedélyezze a replikációját OSTICKETMYSQL

Most már a Contoso is replikálást indítani **OSTICKETMYSQL**.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** akkor adja meg a forrás- és beállításait.
2. Contoso kiválasztja **OSTICKETMYSQL** replikációhoz, és kiválasztja a mobilitási szolgáltatás telepítéséhez használandó fiókot.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Contoso OSTICKETWEB volt használva, amely lehetővé teszi, hogy a replikáció ugyanazt replikációs házirendet alkalmazza.  

**További segítségre van szüksége?**

El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [engedélyezze a replikálást](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>4. lépés: A virtuális gépek áttelepítése 

Futtassa egy gyors contoso feladatátvétel tesztelése, és telepítse át a virtuális gépeket.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatása segít biztosítani, hogy minden a várt módon működik az áttelepítés előtt. 

1. Contoso futtat egy feladatátvételi tesztet a legutóbbi pontnak idő (**legújabb feldolgozott**).
2. És melyik **gép leállítása a feladatátvétel megkezdése előtt**, hogy a hely helyreállítási művelet megkísérli indítására, a feladatátvétel előtt állítsa le a virtuális gép – forrásként. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Tesztelési feladatátvételi futtatják: 
    - Egy Előfeltételek ellenőrzése során, hogy az áttelepítéshez szükséges feltételek vannak érvényben.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pontot választja, a adatokból létrehozott helyreállítási pontot.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.
3. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure-portálon. Azok az ellenőrizze, hogy a virtuális gép a megfelelő méretűek, hogy csatlakozik-e a megfelelő hálózati és fut-e. 
4. Annak ellenőrzése után, azok számolja fel a feladatátvételt, és jegyezze fel, és mentse megfigyelések.

### <a name="create-and-customize-a-recovery-plan"></a>Hozzon létre, és a helyreállítási terv testreszabása

 Miután meggyőződött arról, hogy a feladatátvételi tesztet működőképes-e a várt módon, a Contoso áttelepítés helyreállítási terv létrehozása. 

- A helyreállítási terv sorrendje határozza meg a feladatátvételt végző következik be, hogyan Azure virtuális gépeken futó állapotba kerül az Azure-ban.
- Szeretnék áttelepíteni egy kétrétegű alkalmazást, mivel azok lesz testre szabhatja a helyreállítási terv úgy, hogy az adatokat a virtuális gép (SQLVM) az előtér (WEBVM) előtt.


1. A **helyreállítási tervek (helyreállítás)** > **+ a helyreállítási terv**, hozzon létre egy csomagot, és a virtuális gépek hozzáadása.

    ![Helyreállítási terv](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Miután létrehozta a terv, akkor jelölje ki a testreszabáshoz (**helyreállítási tervek** > **OsTicketMigrationPlan** > **Testreszabás**.
3.  Eltávolítják az **OSTICKETWEB** a **csoport 1: Start**.  Ez biztosítja, hogy az első indítási műveletet érinti **OSTICKETMYSQL** csak.

    ![Helyreállítási csoport](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  A **+ csoport** > **Hozzáadás védett elemek**, adnak hozzá **OSTICKETWEB** való **csoport 2: Start**.  Contoso kell ezeket a két különböző csoportokhoz.

    ![Helyreállítási csoport](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>A virtuális gépek áttelepítése


Contoso készen áll a helyreállítási terv áttelepítése a virtuális gépek a feladatátvételt.

1. Akkor válassza ki a tervet > **feladatátvételi**.
2.  Feladatok átadása a legutóbbi helyreállítási pontot, és adja meg, hogy a Site Recovery próbálkozzon az időt. a feladatátvétel előtt állítsa le a helyszíni virtuális gép válassza ki. Azt követve a feladatátvételi folyamat előrehaladásának a **feladatok** lap.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover1.png)

3. A feladatátvételi vCenter-kiszolgáló leállítja a két virtuális gépeket az ESXi-állomáson futó parancsokat ad ki.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. A feladatátvétel után a Contoso ellenőrizze, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure-portálon.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Annak ellenőrzése után a virtuális Gépet az Azure-ban, akkor az áttelepítési folyamat befejezéséhez az egyes virtuális gépek áttelepítésének befejezéséhez. Leállítja a virtuális gép replikációjának, és leállítja a Site Recovery számlázási a virtuális gép számára.

    ![Feladatátvétel](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>A virtuális gép kapcsolódni az adatbázishoz

Az áttelepítési folyamat utolsó lépése, Contoso frissíteni, hogy a futó alkalmazások adatbázis mutasson az alkalmazás a kapcsolati karakterláncot a **OSTICKETMYSQL** virtuális gép. 

1. Az SSH-kapcsolatot akkor a **OSTICKETWEB** Putty virtuális gép vagy egy másik SSH-ügyfél. A virtuális gép saját, így a magánhálózati IP-cím használatával kapcsolódnak.

    ![Csatlakozás adatbázishoz](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Csatlakozás adatbázishoz](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Győződjön meg arról, hogy van szükségük a **OSTICKETWEB** méretű képes kommunikálni a **OSTICKETMYSQL** virtuális gép. A konfiguráció jelenleg a helyi IP-címmel 172.16.0.43 szoftveresen kötött.

    **A frissítés előtt**
    
    ![IP frissítése](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **A frissítés után**
    
    ![IP frissítése](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Ezek indítsa újra a szolgáltatást, amely **systemctl indítsa újra a apache2**.

    ![Újraindítás](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Végül frissítse a DNS-rekordjainak **OSTICKETWEB** és **OSTICKETMYSQL**, a Contoso tartományvezérlők egyik.

    ![DNS frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![DNS frissítése](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) futó feladatátvételi tesztet. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) Azure feladatátvétele.

## <a name="clean-up-after-migration"></a>Az áttelepítés után tisztítása

Az áttelepítés befejeződött a osTicket app rétegek futnak Azure virtuális gépeken.

Most Contoso kell néhány karbantartása:  

- A helyszíni virtuális gépek azok eltávolítása a vCenter-készlet.
- A helyszíni virtuális gépek eltávolítják a helyi biztonsági mentési feladatok.
- Frissítse a belső dokumentációjának megjelenítése az új helyre, és az IP-címeket OSTICKETWEB és OSTICKETMYSQL.
- Tekintse át minden olyan erőforrásnál, amely interakciót folytatni a virtuális gépeket, és bármely vonatkozó beállítások vagy a dokumentációjával tükrözzék az új konfiguráció frissítése.
- A Contoso az Azure áttelepítése szolgáltatás függőségi leképezéssel segítségével mérheti fel a virtuális gépekről az áttelepítésre. Akkor távolítsa el a Microsoft-Figyelőügynök és a függőségi ügynök még telepítve erre a célra, a virtuális gépről.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az alkalmazás most már fut a Contoso kell teljes üzemeltető, az új infrastruktúra secure.

### <a name="security"></a>Biztonság

A Contoso biztonsági csoportjának biztonsági problémák meghatározásához OSTICKETWEB és OSTICKETMYSQLVMs áttekintése.

- Ezek tekintse át a hálózati biztonsági csoportokkal (NSG-k) a virtuális géphez való hozzáférést. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom teljen.
- Ezek vannak is fontolóra veheti, hogy a virtuális gép lemezeken lemeztitkosítás és Azure KeyVault használatával az adatok védelme.

[További](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásokkal kapcsolatban.

### <a name="backups"></a>Biztonsági másolatok

Contoso adatainak biztonsági mentése az a virtuális gépeket az Azure Backup szolgáltatással. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és pluszköltséggel optimalizálása

- Erőforrások való telepítése után a Contoso Azure címkék rendeli, során meghatározott a [Azure infrastruktúra telepítése](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso rendelkezik a Ubuntu Server licencelési probléma nélkül.
- Contoso által Cloudyn, Microsoft leányvállalata Azure költség felügyeletét teszi lehetővé. A többszörös felhő költsége-kezelési megoldás, amely segít használata és kezelése az Azure és más felhőalapú erőforrásokat is.  [További](https://docs.microsoft.com/azure/cost-management/overview) Azure költség-kezeléssel kapcsolatos. 


## <a name="next-steps"></a>További lépések

Ebben a cikkben azt bemutatta, hogyan telepíti át a Contoso egy helyi service ügyfélszolgálati alkalmazást az Azure IaaS virtuális gépekhez, Azure Site Recovery segítségével két Linux virtuális gépeken futó rétegzett.

A sorozat következő cikkben mutat be, hogyan Contoso át az Azure szolgáltatás ügyfélszolgálati ugyanahhoz az alkalmazáshoz. Most a Contoso használ a Site Recovery az alkalmazás az előtér virtuális gép áttelepítése, és telepítse át az app-adatbázis biztonsági mentéssel és MySQL, a MySQL munkaterület eszköz használata az Azure-adatbázis visszaállítása. [Első lépések](contoso-migration-rehost-linux-vm-mysql.md).
