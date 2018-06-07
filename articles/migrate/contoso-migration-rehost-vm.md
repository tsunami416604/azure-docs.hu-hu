---
title: Egy Azure virtuális gépek az Azure Site Recovery szolgáltatással történő áttelepítés Contoso alkalmazást áthelyezési |} Microsoft Docs
description: Megtudhatja, hogyan áthelyezési a helyszíni alkalmazások, és a növekedési és shift áttelepítéssel áttelepítéséhez a helyszíni gépeket az Azure Site Recovery segítségével az Azure-szolgáltatás.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2018
ms.author: raynew
ms.openlocfilehash: 11b5e2a408d3ba514753f3510b36fce02470c6e9
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825161"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso áttelepítése: az Azure virtuális gépek a helyszíni alkalmazások áthelyezési


Ez a cikk bemutatja, hogyan Contoso áthelyezési által az alkalmazás virtuális gépek áttelepítése az Azure virtuális gépen az Azure SmartHotel alkalmazásaikról.


Ez a dokumentum az egyik egy sorozat része, amelyek megjelenítik a hogyan fiktív cég Contoso áttelepíti a Microsoft Azure felhőbe a helyszíni erőforrások cikkeket. Az adatsorozat háttér-információkat, és a szolgáltatásokat, amelyek bemutatják, egy áttelepítési infrastruktúra beállításával, a helyszíni erőforrások az áttelepítéshez értékelésére, és áttelepítések különböző típusú futtató tartalmaz. Forgatókönyvek nő összetettségét, és adott idő alatt további cikkek fel kell venni.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
A következő cikket: 1: áttekintés | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
2. cikk: Az Azure-infrastruktúra telepítése | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
3. cikk: A helyszíni erőforrások felmérése  | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
4. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és egy SQL felügyelt példány (Ez a cikk) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
5. cikk: Azonosítóterületen (növekedési-és-shift) az Azure virtuális gépen (Ez a cikk) | Bemutatja, hogyan Contoso át SmartHotel alkalmazásuk csak a Site Recovery segítségével virtuális gépeket.
Cikk 6: Azonosítóterületen (növekedési-és-shift) és az Azure virtuális gépeken futó SQL Server rendelkezésre állási csoportok | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázis egy SQL Server rendelkezésre állási csoporthoz. | Elérhető
7. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és az Azure-beli MySQL kiszolgáló | Bemutatja, hogyan Contoso áttelepíti a SmartHotel app virtuális gépek áttelepítéséhez a Site Recovery és MySQL munkaterület használatával (biztonsági mentése és visszaállítása) Azure-beli MySQL Server-példány. | Elérhető

Ebben a cikkben a Contoso a kétrétegű Windows kell áttelepítenie. NET SmartHotel alkalmazást futtató VMware virtuális gépeken, az Azure-bA. Ha az alkalmazás használatához, való nyílt forráskódú, és letöltheti a [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>A stratégiai célok

Az informatikai vezetőségi tagja szorosan együttműködik az üzleti partnerek megértéséhez válasszon ezek közül az áttelepítés számára:

- **Üzleti növekedési cím**: Contoso egyre, és ennek eredményeképpen nincs nyomás a helyszíni rendszer és a infrastruktúra.
- **Kockázatának**: A SmartHotel app fontos a Contoso vállalati. Helyezze át Azure nulla kockázattal szeretnék.
- **Kiterjesztése**: Contoso nem kívánja módosítani a az alkalmazás. Egyszerűen szeretnék győződjön meg arról, hogy az állandó.


## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhő team le az áttelepítés célokat van rögzítve. Ezen célok segítségével meghatározhatja a legjobb áttelepítési módszer:

- Az áttelepítés után az alkalmazás az Azure-ban kell azonos teljesítménybeli képességeinek azonban jelenleg nem VMware.  Az alkalmazás el kritikus jelzéssel a felhőben, mert az a helyszínen marad. 
- Contoso nem szeretné, hogy az alkalmazás beruházásának.  Fontos, hogy az üzleti tevékenységre vonatkozóan, de az aktuális képernyőn egyszerűen szeretnék biztonságosan helyezze át a felhőbe.
- Contoso nem szeretné, hogy az alkalmazás a ops modell módosításához. Szeretnék használni a felhőben, hogy most már ugyanúgy.
- Contoso nem szeretné, hogy bármely alkalmazás funkció megváltoztatása. Csak az alkalmazás helye módosul.

## <a name="proposed-architecture"></a>Javasolt architektúrája

Ez a jelenlegi környezet

- Az alkalmazás többszintű két virtuális gépek között (**WEBVM** és **SQLVM**).
- A virtuális gépek a VMware ESXi-állomáson lévő **contosohost1.contoso.com** (6.5-ös verziójú).
- A VMware-környezetben kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), a virtuális gép futó.
- Contoso rendelkezik egy olyan helyszíni adatközpontban (a contoso-adatközpontban), egy a helyi tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépek a Contoso adatközpontban fog kell szerelni, az áttelepítés befejezése után.

![Forgatókönyv-architektúra](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>áttelepítési folyamat

Contoso Site Recovery segítségével Azure virtuális gépek áttelepítése az alkalmazás előtér- és adatbázis virtuális gépek:

- Első lépésként azok lesz előkészítése és beállítani az Azure Site Recovery, és a helyszíni VMware-infrastruktúra előkészítése.
- Már rendelkeznek az [Azure-infrastruktúra](contoso-migration-infrastructure.md) helyen, ezért csak szükséges néhány Azure összetevők hozzáadásához kifejezetten a Site Recovery.
- Az előkészített mindent elindíthatják a virtuális gépek replikálása.
-Után a replikáció engedélyezett-e, és dolgozik, áttelepítés után a virtuális gép által feladatátvételével az Azure-bA.

![áttelepítési folyamat](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | A szolgáltatás koordinálja a kezeli a áttelepítési és vészhelyreállítás Azure virtuális gépek és a helyszíni virtuális gépek és fizikai kiszolgálók.  | Az Azure-bA replikálás során Azure Storage díjak sem merülnek fel.  Azure virtuális gépek jönnek létre, és a függő díj terheli, feladatátvétel esetén. [További](https://azure.microsoft.com/pricing/details/site-recovery/) kapcsolatos díjakat és az árképzés terén.


## <a name="prerequisites"></a>Előfeltételek

Ez mit meg (és a Contoso) futtatásához szükséges ebben a forgatókönyvben.

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetés korai cikkek során a sorozat. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha még nem a rendszergazda egy meglévő előfizetéshez használ, meg kell dolgoznia a rendszergazdától, hogy rendeljen Önhöz tulajdonosi vagy közreműködői engedélyekkel kell rendelkeznie.<br/><br/> Ha részletesebb engedélyekre van szükség, tekintse át a [Ez a cikk](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastruktúra** | [Megtudhatja, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúra.<br/><br/> További információ a konkrét [hálózati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) és [tárolási](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) Site Recovery követelményei.
**Helyszíni kiszolgálók** | A helyszíni vCenter Server 5.5, 6.0 vagy 6.5 kell futnia<br/><br/> ESXi-gazdagépek 5.5, 6.0 vagy 6.5 kell futtatni.<br/><br/> Egy vagy több VMware virtuális gépek futnia kell az ESXi-állomáson.
**A helyszíni virtuális gépek** | Meg kell felelnie a virtuális gépek [Azure-követelményeknek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>A forgatókönyv lépései

Ez a Contoso hogyan működik az áttelepítés:

> [!div class="checklist"]
> * **1. lépés: Felkészülés Azure Site Recovery**: hoznak létre, és a Recovery Services-tároló replikált adatok tárolásához Azure storage-fiók.
> * **2. lépés: A helyszíni VMware előkészítése a Site Recovery**: készítse elő a fiókok a virtuális gép felderítés és az ügynök telepítése, és készítse elő a feladatátvételt követően Azure virtuális gépeken való kapcsolódáshoz.
> * **3. lépés: Replikálása virtuális gépek**: a replikáció beállítása, és indítsa el a virtuális gépek replikálása az Azure storage.
> * **4. lépés: A Site Recovery rendelkező virtuális gépek áttelepítése**: Győződjön meg arról, hogy minden rendben működik-e a feladatátvételi teszt futtatása, és futtassa a teljes feladatátvételt a virtuális gépek áttelepítése az Azure-bA.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>1. lépés: A Site Recovery szolgáltatás Azure előkészítése

Az alábbiakban a Contoso kell áttelepíteni a virtuális gépek Azure-bA az Azure összetevőket:

- Egy Vnetet, amelyben Azure virtuális gépek kerülnek, ha a feladatátvétel során jönnek létre.
- A replikált adatok tárolásához Azure storage-fiók. 
- Az Azure Recovery Services-tároló.

Ezek beállítása a következőképpen:

1. Már beállított egy hálózatot, hogy használhassák a Site Recovery contoso ha azok [az Azure-infrastruktúra telepítése](contoso-migration-infrastructure.md)

    - A SmartHotel alkalmazást éles alkalmazások, és a virtuális gépeket telepíti át az éles Azure-hálózathoz (VNET-termék-EUS2) az elsődleges régióban USA keleti régiója 2.
    - Mindkét virtuális gépek ContosoRG az erőforráscsoporthoz tartozik, éles erőforrások használt kerülnek.
    - Az alkalmazás előtér virtuális gép (WEBVM) telepítse át a frontend alhálózathoz (termék-FE-EUS2), az éles hálózati környezetben.
    - Az alkalmazás adatbázis virtuális gép (SQLVM) az adatbázis alhálózathoz (termék-DB-EUS2), telepítse át a termelési hálózat.

2. A Contoso Azure storage-fiók (contosovmsacc20180528) az elsődleges régióban hoz létre.
    - A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
    - Standard szintű tárolót, és LRS replikációs egy általános célú fiók használata. 

    ![Helyreállítási tárolási hely](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Fiókkal a hálózati és tárolási helyen Contoso most létrehozza a Recovery Services-tároló (ContosoMigrationVault), és elhelyezi ContosoFailoverRG erőforráscsoporthoz tartozik, az elsődleges régióban USA keleti régiója 2.

    ![Recovery Services-tároló](./media/contoso-migration-rehost-vm/asr-vault.png)

**További segítségre van szüksége?**

[További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Azure Site Recovery beállítását.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>2. lépés: A Site Recovery a helyszíni VMware előkészítése

Íme néhány Contoso előkészíti a helyszíni:

- A vCenter-kiszolgáló vagy vSphere ESXi-állomáson, automatizálhatja a virtuális gép felderítési fiókkal.
- Egy fiók, amely lehetővé teszi, hogy a mobilitási szolgáltatás automatikus telepítését a VMware virtuális gépeken. 
- A helyszíni virtuális gép beállításait, hogy a Contoso a replikált Azure virtuális gépek a feladatátvételt követően csatlakozni tudnak.


### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Virtuális gépek automatikus észlelését. 
- Levezényelni a replikálása, feladatátvétele és feladat-visszavételt a virtuális gépek.
- Szükség van legalább egy csak olvasási jogokat biztosító fiókra. A fióknak kell lennie a műveletek, például a létrehozása és lemezek eltávolítása, és bekapcsolja a virtuális gépeket futtathatnak.

Contoso beállítja azt a fiókot az alábbiak szerint:

1. A vCenter szinten szerepkör azok létrehozása.
2. Ezek a szerepkör hozzárendelése a szükséges engedélyekkel.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatást telepítenie kell minden egyes virtuális gépen.

- A Site Recovery teheti a mobilitási szolgáltatás egy automatikus ügyfélleküldéses telepítést, ha engedélyezve van a Virtuálisgép-replikációt.
- A fiókra akkor szükség, érdekében, hogy a helyreállítás a virtuális gépeket a leküldéses telepítés. Ennek a fióknak meg kell adnia, amikor a replikáció beállítása.
- A fiók tartományi vagy helyi, a virtuális gépeken telepítéséhez szükséges engedélyekkel rendelkező lehet.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

A feladatátvétel után a Contoso biztosítani az Azure virtuális gépeken való kapcsolódáshoz. Ehhez az szükséges, akkor tegye a következőket áttelepítés előtt:

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


## <a name="step-3-replicate-the-on-premises-vms"></a>3. lépés: A helyszíni virtuális gépek replikálása

Mielőtt azok lefutnának a áttelepítése az Azure-ba, Contoso kell beállítani, és engedélyezze a replikálást.

### <a name="set-a-replication-goal"></a>A replikációs cél beállítása

1. A tárolóban, a tároló neve (ContosoVMVault) alatt, és, melyik replikációs cél (**bevezetés** > **Site Recovery** > **infrastruktúra előkészítése** .
2. Akkor adja meg, hogy a gép-e a helyszínen található, a VMware rendszerben fut, és az Azure-bA replikál.

    ![Replikációs cél](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Győződjön meg róla, központi telepítésének megtervezése

A folytatáshoz azok győződjön meg arról, hogy elvégezték-üzembe helyezésének tervezése kiválasztásával **Igen, elvégeztem**. Ebben a forgatókönyvben a Contoso csak telepít két virtuális gépeket, és nem szükséges, központi telepítésének megtervezése.


### <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Contoso be kell állítania a forrás környezetében. Ehhez az szükséges, azok az OVF-sablon letöltése és helyezzen vele üzembe a Site Recovery konfigurációs kiszolgáló magas rendelkezésre állásúként, helyszíni VMware virtuális gép. Miután a konfigurációs kiszolgáló fut, akkor regisztrálja azt ezzel tárolóban.

A konfigurációs kiszolgáló futtatja összetevők száma:

- A konfigurációs kiszolgáló-összetevő, amely a helyszíni és az Azure közötti kommunikáció koordinálja, és adatreplikáció kezeli.
- A folyamatkiszolgáló, amelyek egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak.
- A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.



Contoso hajtsa végre ezeket a lépéseket az alábbiak szerint:

1. A tárolóban, az OVF sablon letöltése **infrastruktúra előkészítése** > **forrás** > **konfigurációs kiszolgáló**.
    
    ![OVF letöltése](./media/contoso-migration-rehost-vm/add-cs.png)

2. Azok a sablon létrehozása és telepítése a virtuális gép VMware importálja.

    ![OVF-sablon](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  A virtuális gép első bekapcsolásakor elinduló be egy Windows Server 2016 telepítési élmény. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
4. A telepítés befejezése után ezek jelentkezzen be a virtuális gép rendszergazdaként. Első bejelentkezéskor a az Azure Site Recovery Configuration Tool alapértelmezés szerint fut.
5. Az eszköz akkor adjon meg egy regisztrálja a konfigurációs kiszolgálót a tárolóban.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után bejelentkeznek az Azure-előfizetést. A hitelesítő adatokat a tárolóba, amelyben regisztrálják lesz a konfigurációs kiszolgáló hozzáféréssel kell rendelkeznie.

    [Regisztráljon egy konfigurációs kiszolgálót](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Bejelentkeznek az a gép újra, és a konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.
9. A varázslóban akkor válassza a NIC-replikációs forgalom fogadására. Ez a beállítás nem módosítható, miután van konfigurálva.
10. Akkor adja meg az előfizetés, erőforráscsoport és a tárolót, amelyben a konfigurációs kiszolgáló regisztrálásához.
        ![Tároló](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Ezek és letöltése és telepítése a MySQL-kiszolgáló, VMWare PowerCLI. 
11. Ellenőrzés után azokat a vCenter-kiszolgáló vagy vSphere-gazdagép teljes Tartománynevét vagy IP-címe adható meg. Hagyja meg az alapértelmezett portot, és adjon meg egy rövid nevet a kiszolgáló Azure-ban.
12. Akkor adja meg, a fiók automatikus felderítéshez létrehozott és a hitelesítő adatait, amely automatikusan telepíteni a mobilitási szolgáltatás segítségével. A Windows-alapú gépek a fiókot kell a helyi rendszergazdai jogosultsága a virtuális gépeken.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Az Azure portálon, a regisztráció befejezését követően a Contoso dupla ellenőrzi, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. 15 perc vagy több tarthat a felderítés. 
8. A Site Recovery ezután csatlakozik a megadott beállítások VMware-kiszolgálók, és felderíti a virtuális gépek.

### <a name="set-up-the-target"></a>A cél beállítása

Most Contoso megadja a cél replikációs beállításait.

1. A **infrastruktúra előkészítése** > **cél**, akkor adja meg a tároló beállításait.
2. A Site Recovery ellenőrzi, hogy nincs-e az Azure storage-fiók és a hálózat a megadott célhelyen.

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Most már a Contoso replikációs házirendet hozhat létre.

1. A **infrastruktúra előkészítése** > **replikációs beállítások** > **replikációs házirend** >  **létrehozása és Társítsa**, akkor hozzon létre egy házirendet **ContosoMigrationPolicy**.
2. Az alapértelmezett beállítások használata:
    - **Helyreállítási Időkorlát küszöbértéke**: alapértelmezett 60 perc. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
    - **Helyreállítási pontok megőrzésének ideje**. Alapértelmezett 24 órás. Ez az érték határozza meg, hogy mennyi ideig a megőrzési időszak az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**. Alapértelmezett érték egy óra. Ezt az értéket adja meg, amellyel alkalmazáskonzisztens pillanatképeket jönnek létre.

        ![Replikációs házirend létrehozása](./media/contoso-migration-rehost-vm/replication-policy.png)

5. A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

    ![Replikációs szabályzat társítása](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Engedélyezze a replikációját WEBVM

Minden helyen a Contoso engedélyezheti a virtuális gépek replikációját. Elindítja a WebVM.

1. A **alkalmazás replikálása** > **forrás** > **+ replikálás** az adatforrás-beállítások, és melyik.
2. Utal, hogy azok szeretné engedélyezni a virtuális gépek, válassza ki a vCenter-kiszolgáló és a konfigurációs kiszolgáló.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Akkor válassza ki a tárolóbeállítások, beleértve az erőforráscsoport és az Azure hálózati és a storage-fiók.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Contoso kiválasztja **WebVM** replikációhoz, ellenőrzi a replikációs házirendet, és lehetővé teszi, hogy a replikáció.

    - Ebben a szakaszban Contoso csak választja ki WEBVM, mert a VNet és alhálózat meg kell adni, és a Contoso helyezi az alkalmazás virtuális gépek külön alhálózatokon.
    - A Site Recovery replikációs engedélyezésekor a rendszer automatikusan telepíti a mobilitási szolgáltatást a virtuális Gépet.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Azok a replikációs folyamatot a nyomon **feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
6. A **Essentials** az Azure-portálon, a Contoso a struktúra látható a virtuális gépek replikálása Azure-bA.


### <a name="enable-replication-for-sqlvm"></a>Engedélyezze a replikációját SQLVM

Contoso most már megkezdheti a SQLVM gép replikál, ugyanazt az eljárást a fenti használják.

1. Akkor válassza ki az adatforrás-beállítások.

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Ezek a tárolóbeállítások majd adja meg.

     ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. A replikáció és melyik SQLVM. 

    ![A replikáció engedélyezése](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. A használt WEBVM azonos replikációs házirend alkalmazása, és engedélyezze a replikálást.

    ![Infrastruktúra nézet](./media/contoso-migration-rehost-vm/essentials.png)

**További segítségre van szüksége?**

- El tudja olvasni az alábbi lépéseket a teljes forgatókönyv [vész-helyreállítási beállítása a helyszíni VMware virtuális gépek](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Részletes útmutatás segítségével érhető el [a forráskörnyezet beállítása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [a konfigurációs kiszolgáló telepítésének](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), és [replikáció beállításainak konfigurálása](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- További tudnivalók [replikáció](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>4. lépés: A virtuális gépek áttelepítése 

A Contoso egy gyors feladatátvételi, majd a teljes feladatátvétel futtatása a virtuális gépek áttelepítéséhez.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt biztosíthatja, hogy minden a várt módon működik. 

1. Contoso futtat egy feladatátvételi tesztet a legutóbbi pontnak idő (**legújabb feldolgozott**).
2. És melyik **gép leállítása a feladatátvétel megkezdése előtt**, hogy a hely helyreállítási művelet megkísérli indítására, a feladatátvétel előtt állítsa le a virtuális gép – forrásként. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 
3. Tesztelési feladatátvételi futtatják: 

    - Egy Előfeltételek ellenőrzése során, hogy az áttelepítéshez szükséges feltételek vannak érvényben.
    - A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
    - A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.
    
3. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure-portálon. Contoso ellenőrzi, hogy a virtuális gép mérete, a megfelelő hálózathoz csatlakozik, és fut-e. 
4. Annak ellenőrzése után a feladatátvételi tesztet, azok számolja fel a feladatátvételt, és jegyezze fel, és mentse megfigyelések. 

### <a name="create-and-customize-a-recovery-plan"></a>Hozzon létre, és a helyreállítási terv testreszabása

 Miután meggyőződött arról, hogy a feladatátvételi tesztet működőképes-e a várt módon, a Contoso áttelepítési helyreállítási tervet hoz létre. 

- A helyreállítási terv sorrendje határozza meg a feladatátvételt végző következik be, és azt jelzi, hogyan Azure virtuális gépek kerül online az Azure-ban.
- Mivel az alkalmazás kétrétegű, akkor testre szabhatja a helyreállítási terv úgy, hogy az adatok VM csomag (SQLVM) az előtér (WEBVM) előtt.

1. A **helyreállítási tervek (helyreállítás)** > **+ a helyreállítási terv**, hozzon létre egy csomagot, és a virtuális gépek hozzáadása.

    ![Helyreállítási terv](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Miután létrehozta a terv, akkor testre szabhatja, (**helyreállítási tervek** > **SmartHotelMigrationPlan** > **Testreszabás**).
2.  A WEBVM eltávolítják **csoport 1: Start**.  Ez biztosítja, hogy az első indítási műveletet SQLVM érinti.
3.  A **+ csoport** > **Hozzáadás védett elemek**, megnövelik WEBVM csoport 2: Indítsa el.  A virtuális gépek kell lennie a két különböző csoporthoz.


### <a name="migrate-the-vms"></a>A virtuális gépek áttelepítése


Most már a Contoso az áttelepítés befejezéséhez teljes feladatátvevő futtathatja.

1. A helyreállítási tervből, és melyik > **feladatátvételi**.
2. Feladatátvételt a legutóbbi helyreállítási pontot választanak, és próbálja meg, hogy a hely helyreállítási időt. a feladatátvétel előtt állítsa le a helyszíni virtuális gép. Azt követve a feladatátvételi folyamat előrehaladásának a **feladatok** lap.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover1.png)


3. A feladatátvétel után azok győződjön meg arról, hogy az Azure virtuális gép megjelenik-e a várt módon az Azure-portálon.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover2.png)  

3. Ellenőrzés után ezek az egyes virtuális gépek áttelepítésének befejezéséhez. Leállítja a virtuális gép replikációjának, és leállítja a Site Recovery számlázás.

    ![Feladatátvétel](./media/contoso-migration-rehost-vm/failover3.png)

**További segítségre van szüksége?**

- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) futó feladatátvételi tesztet. 
- [Ismerje meg,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) a helyreállítási terv létrehozása.
- [További tudnivalók](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) Azure feladatátvétele.

## <a name="clean-up-after-migration"></a>Az áttelepítés után tisztítása

Az áttelepítés befejeződött a SmartHotel app rétegek futnak Azure virtuális gépeken.

Most van szüksége a Contoso tisztítás lépések elvégzéséhez:  

- A WEBVM gép eltávolítása a vCenter-készlet.
- A SQLVM gép eltávolítása a vCenter-készlet.
- Távolítsa el a WEBVM és SQLVM a helyi biztonsági mentési feladatok.
- Frissítse a virtuális gépeket az új helyre, és az IP-címek megjeleníteni kívánt belső dokumentációt.
- Tekintse át minden olyan erőforrásnál, amely interakciót folytatni a virtuális gépeket, és bármely vonatkozó beállítások vagy a dokumentációjával tükrözzék az új konfiguráció frissítése.

## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Amikor az alkalmazás most már fut, Contoso most teljesen azok és kell biztonságossá tenni az Azure-ban.

### <a name="security"></a>Biztonság

A Contoso biztonsági csapata nem ellenőrzi az Azure virtuális gépeken, biztonsági problémák meghatározásához.

- Hozzáférést, akkor tekintse át a hálózati biztonsági csoportokkal (NSG-k) a virtuális gépek. Az NSG-k segítségével győződjön meg arról, hogy csak az alkalmazás engedélyezett forgalom csatlakozni tud hozzá.
- Akkor is érdemes lehet az Azure Disk Encryption és KeyVault lemezen lévő adatok védelme.

[További](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) virtuális gépek biztonsági eljárásokkal kapcsolatban.

### <a name="backups"></a>Biztonsági másolatok

Contoso lesz adatainak biztonsági mentése az a virtuális gépeket az Azure Backup szolgáltatással. [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencelési és pluszköltséggel optimalizálása

1. Contoso rendelkezik a meglévő licencelésből a virtuális gépekhez, és fogja használni, az Azure hibrid juttatásra.  Ezeket átkonvertálja a meglévő Azure virtuális gépek, a díjszabás előnyeit.
2. Contoso által Cloudyn, Microsoft leányvállalata Azure költség felügyeletét teszi lehetővé. A többszörös felhő költsége-kezelési megoldás, amelyek segítségével használata és kezelése az Azure és más felhőalapú erőforrásokat is. [További](https://docs.microsoft.com/azure/cost-management/overview) Azure költség-kezeléssel kapcsolatos. 

## <a name="conclusion"></a>Összegzés

Ebben a cikkben Contoso rehosted a SmartHotel alkalmazást az Azure által az alkalmazás virtuális gépek áttelepítése az Azure virtuális gépen a Site Recovery szolgáltatással. 


## <a name="next-steps"></a>További lépések

A sorozat következő cikkben azt fogja bemutatják, hogyan Contoso áthelyezési a SmartHotel alkalmazás előtér virtuális gép az Azure-ban a Site Recovery szolgáltatásban, és telepítse át az adatbázist egy Azure SQL AlwaysOn rendelkezésre állási csoporthoz.

