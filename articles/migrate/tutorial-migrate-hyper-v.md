---
title: Hyper-V virtuális gépek áttelepítése az Azure-ba Azure Migrate kiszolgáló áttelepítése
description: Ismerje meg, hogyan telepítheti át a helyszíni Hyper-V virtuális gépeket az Azure-ba Azure Migrate Server Migration használatával
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: be5d519269739f09b4a4264292f578b1d7051d26
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196312"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V-alapú virtuális gépek migrálása az Azure-ba 

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni Hyper-V virtuális gépeket az Azure-ba a Azure Migrate Server áttelepítési eszközzel az ügynök nélküli áttelepítés használatával.

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan értékelheti és telepítheti át a Hyper-V-t az Azure-ba a Azure Migrate Server Assessment és Migration használatával. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:


> [!div class="checklist"]
> * Az Azure és a helyszíni Hyper-V-környezet előkészítése
> * Állítsa be a forrás-környezetet, és telepítsen egy replikációs készüléket.
> * Állítsa be a célkörnyezetet.
> * Engedélyezze a replikációt.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson teljes áttelepítést az Azure-ba.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Tekintse át](hyper-v-migration-architecture.md) a Hyper-V áttelepítési architektúrát.
2. Az Azure és a Hyper-V áttelepítéshez való beállításához [fejezze be a sorozat első oktatóanyagát](tutorial-prepare-hyper-v.md) . Az első oktatóanyagban a következőket végezheti el:
    - [Készítse elő az Azure](tutorial-prepare-hyper-v.md#prepare-azure) -t áttelepítésre.
    - [Készítse elő a helyszíni környezetet](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) az áttelepítéshez.
3. Javasoljuk, hogy az Azure-ba való Migrálás előtt próbálja meg a Hyper-V virtuális gépek felmérését Azure Migrate Server Assessment használatával. Ehhez a sorozat [második oktatóanyagát](tutorial-assess-hyper-v.md) kell elvégeznie. Bár javasoljuk, hogy kipróbáljon egy értékelést, a virtuális gépek áttelepítését megelőzően nem kell értékelést futtatnia.
4. Győződjön meg arról, hogy az Azure-fiókja hozzá van rendelve a virtuálisgép-közreműködő szerepkörhöz, így rendelkezik a következő engedélyekkel:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írás egy Azure-beli felügyelt lemezre.   
5. [Hozzon létre egy Azure-hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Az Azure-ba való Migrálás során a létrehozott Azure-beli virtuális gépek az áttelepítés beállításakor megadott Azure-hálózathoz csatlakoznak.


## <a name="add-the-azure-migrate-server-migration-tool"></a>A Azure Migrate Server áttelepítési eszköz hozzáadása

Ha nem követte a Hyper-V virtuális gépek felmérésére szolgáló második oktatóanyagot, [kövesse ezeket az utasításokat](how-to-add-tool-first-time.md) egy Azure Migrate-projekt beállításához, és adja hozzá a Azure Migrate Server áttelepítési eszközt a projekthez.

Ha követte a második oktatóanyagot, és már beállított egy Azure Migrate projektet, adja hozzá a Azure Migrate Server áttelepítési eszközt a következő módon:

1. A Azure Migrate projektben kattintson az **Áttekintés**elemre. 
2. A **felderítési, felmérési és áttelepítési kiszolgálók**területen kattintson a **kiszolgálók felmérése és áttelepítése**elemre.
3. Az **áttelepítési eszközök**területen válassza **a kattintson ide az áttelepítési eszköz hozzáadásához, amikor készen áll az**áttelepítésre.

    ![Eszköz kiválasztása](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Az eszközök listában válassza a **Azure Migrate: kiszolgáló áttelepítése** > **Hozzáadás eszköz** elemet.

    ![Kiszolgáló áttelepítési eszköze](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása

Azure Migrate kiszolgáló áttelepítése egy egyszerűsített Hyper-V virtuálisgép-berendezést futtat.

- A készülék virtuálisgép-felderítést végez, és a virtuális gépek metaadatait és teljesítményadatait elküldi Azure Migrate kiszolgáló áttelepítésére.
- Ugyanezt a készüléket a Azure Migrate Server Assessment eszköz is használja.

A készülék beállítása:
- Ha követte a Hyper-V virtuális gépek felmérésére szolgáló második oktatóanyagot, az oktatóanyag során már beállította a készüléket.
- Ha nem követi ezt az oktatóanyagot, most be kell állítania a készüléket. Ehhez tegye a következőket: 

    - Töltsön le egy tömörített Hyper-V virtuális merevlemezt a Azure Portal.
    - Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez. 
    - Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

    A berendezés beállításához kövesse a [cikk](how-to-set-up-appliance-hyper-v.md) utasításait.

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-gazdagépek előkészítése

1. A Azure Migrate projekt > **kiszolgálók** **Azure Migrate: kiszolgáló áttelepítése**területen kattintson a **felderítés**elemre.
2. A számítógépek **felderítése** > a **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V**lehetőséget.
3. A **cél régióban**válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
6. Válassza **a megerősítés lehetőséget, hogy az áttelepítéshez a régió neve legyen**.
7. Kattintson az **erőforrások létrehozása**gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította az áttelepítést Azure Migrate kiszolgáló áttelepítésével, ez a lehetőség nem jelenik meg, mivel az erőforrások korábban lettek beállítva.
    - Erre a gombra kattintva nem módosítható a projekthez tartozó cél régió.
    - Az összes további áttelepítés erre a régióra vonatkozik.
    
8. A **Hyper-v-gazdagépek előkészítése**területen töltse le a Hyper-v replikációs szolgáltatót és a regisztrációs kulcsot tartalmazó fájlt.
    - A regisztrációs kulcs szükséges a Hyper-V-gazdagép Azure Migrate kiszolgáló áttelepítéssel való regisztrálásához.
    - A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató és kulcs letöltése](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Másolja a szolgáltató telepítési fájlját és a regisztrációs kulcsot tartalmazó fájlt minden olyan Hyper-V-gazdagépre (vagy fürtcsomópont), amely a replikálni kívánt virtuális gépeket futtatja.
5. Futtassa a szolgáltató telepítési fájlját minden gazdagépen a következő eljárásban leírtak szerint.
6. Miután telepítette a szolgáltatót a gazdagépeken, a **felderítési gépek**területen kattintson a **regisztráció véglegesítése**lehetőségre.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-hyper-v/finalize-registration.png)

A regisztráció véglegesítése után akár 15 percet is igénybe vehet, amíg a felderített virtuális gépek megjelennek Azure Migrate kiszolgáló áttelepítésekor. Ahogy a virtuális gépek felderítése megtörtént, a **felderített kiszolgálók** száma emelkedik.

![Felderített kiszolgálók](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Hyper-V-gazdagépek regisztrálása

Telepítse a letöltött telepítőfájlt (AzureSiteRecoveryProvider. exe) minden érintett Hyper-V-gazdagépre.

1. Futtassa a szolgáltató telepítési fájlját minden gazdagépen vagy fürtcsomóponton.
2. A szolgáltató telepítővarázslója > **Microsoft Update**, hogy a Microsoft Update használatával keressen szolgáltatói frissítéseket.
3. A **telepítés**területen fogadja el a szolgáltató és az ügynök alapértelmezett telepítési helyét, majd válassza a **telepítés**lehetőséget.
4. A telepítés után a regisztrációs varázslóban > tároló **beállításai**területen válassza a **Tallózás**lehetőséget, majd a **kulcsfájl**területen válassza ki a letöltött tároló-kulcsot.
5. A **Proxybeállítások**területen határozza meg, hogy a gazdagépen futó szolgáltató hogyan csatlakozzon az internethez.
    - Ha a készülék proxykiszolgáló mögött található, meg kell adnia a proxybeállításokat.
    - Adja meg a proxy nevét **http://ip-addressként** vagy **http://FQDNként** . A HTTPS-proxy kiszolgálók nem támogatottak.
   

6. Győződjön meg arról, hogy a szolgáltató el tudja érni a [szükséges URL-címeket](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. A **regisztráció**során a gazdagép regisztrálása után kattintson a **Befejezés**gombra.

## <a name="replicate-hyper-v-vms"></a>Hyper-V virtuális gépek replikálása

A felderítés befejeződése után megkezdheti a Hyper-V virtuális gépek replikálását az Azure-ba.

> [!NOTE]
> Egyszerre legfeljebb 10 gépet replikálhat. Ha többre van szüksége, replikálja őket egyszerre 10 kötegben.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése**elemre, majd kattintson a **replikálás**gombra.
2. A **replikálás**során > a **forrás beállításai** > **a gépek virtualizálva?** , válassza **az igen, a Hyper-V**lehetőséget. Ezután kattintson a **Tovább gombra: Virtual Machines**.
3. A **Virtuális gépek** területen jelölje ki a replikálni kívánt gépeket.
    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

        ![Értékelés kiválasztása](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. A **Virtuális gépek** között igény szerint keressen a virtuális gépekre, és válassza ki a gépeket, amelyeket migrálni szeretne. Ezután kattintson a **Tovább gombra: cél beállításai**.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-hyper-v/select-vms.png)

5. A **cél beállításai**területen válassza ki azt a célhelyet, amelyre az áttelepítést, az előfizetést és az Azure-beli virtuális gépeket az áttelepítés után tároló erőforráscsoport.
7. A **replikációs tároló fiók**területen válassza ki azt az Azure Storage-fiókot, amelyben a replikált adattárolók tárolódnak az Azure-ban.
8. **Virtual Network**válassza ki azt az Azure VNet/alhálózatot, amelyhez az Azure-beli virtuális gépek csatlakozni fognak az áttelepítés után.
9. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Next** (Tovább) gombra.

    ![Cél beállításai](./media/tutorial-migrate-hyper-v/target-settings.png)

10. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő lista az ajánlott méretet fogja tartalmazni. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási csoport**: Ha a virtuális gépnek az áttelepítés után Azure-beli rendelkezésre állási csoportba kell tartoznia, adja meg a készletet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

    ![VIRTUÁLIS gépek számítási beállításai](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. A **Lemezek** területen adja meg, hogy a virtuálisgép-lemezek replikálva legyenek-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium felügyelt lemez) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemezek](./media/tutorial-migrate-hyper-v/disks.png)

10. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálás beállításait a folyamat kezdete előtt bármikor módosíthatja a **Kezelés** > **Gépek replikálása** területen. A replikáció kezdete után a beállítások már nem módosíthatók.

### <a name="provisioning-for-the-first-time"></a>Kiépítés első alkalommal

Ha ez az első olyan virtuális gép, amelyet a Azure Migrate projektben replikál, Azure Migrate a kiszolgáló áttelepítése automatikusan kiosztja ezeket az erőforrásokat a projekttel azonos erőforráscsoporthoz.

- **Service Bus**: Azure Migrate kiszolgáló áttelepítése a Service Bus használatával küldi el a replikálási előkészítési üzeneteket a berendezésnek.
- **Átjáró Storage-fiók**: a kiszolgáló áttelepítése az átjáró Storage-fiók használatával tárolja a replikált virtuális gépekre vonatkozó állapotinformációkat.
- **Log Storage-fiók**: a Azure Migrate készülék feltölti a virtuális gépek replikációs naplóit egy log Storage-fiókba. Azure Migrate a replikációs adatokat a replika felügyelt lemezekre alkalmazza.
- **Key Vault**: a Azure Migrate készülék a kulcstartó használatával kezeli a Service Bus kapcsolati karakterláncait, valamint a replikáció során használt Storage-fiókok hozzáférési kulcsait. Be kell állítania azokat az engedélyeket, amelyeket a kulcstartónak el kell érnie ahhoz, hogy előkészítse a Storage-fiókot. [Tekintse át ezeket az engedélyeket](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Nyomon követés és figyelés


- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre. 
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-ba.

A feladatok állapotát a portál értesítéseiben követheti nyomon.

A replikálási állapot figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése**elemre.
![a replikáció figyelése](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson az **áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók** > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson a **kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. A **Migrálás** > **Virtuális gépek leállítása és adatvesztés nélküli tervezett migrálás végrehajtása** területen válassza az **Igen** > **OK** lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása**elemre. Ez leállítja a helyszíni gép replikálását, és megtisztítja a virtuális gép replikációs állapotával kapcsolatos információkat.
2. Telepítse az Azure-beli VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [Részletek](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [Részletek](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/security-center/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.
