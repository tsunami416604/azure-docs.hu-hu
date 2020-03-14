---
title: Telepítse át a gépeket fizikai kiszolgálóként az Azure-ba Azure Migrate használatával.
description: Ez a cikk bemutatja, hogyan telepíthet át fizikai gépeket az Azure-ba Azure Migrate használatával.
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: MVC
ms.openlocfilehash: bd55f422451df935301245eef3e9dd10f7ab55b1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239328"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Gépek migrálása fizikai kiszolgálóként az Azure-ba

Ez a cikk bemutatja, hogyan telepítheti át a gépeket fizikai kiszolgálóként az Azure-ba a Azure Migrate: Server áttelepítési eszköz használatával. A gépek áttelepítése a fizikai kiszolgálóként való kezeléssel számos esetben hasznos:

- Telepítse át a helyszíni fizikai kiszolgálókat.
- Virtuális gépek migrálása virtualizált platformokkal, például Xen, KVM.
- A Hyper-V-vagy VMware-alapú virtuális gépek áttelepítése, ha valamilyen okból kifolyólag nem tudja használni a [Hyper-v](tutorial-migrate-hyper-v.md)normál áttelepítési folyamatát vagy a [VMware](server-migrate-overview.md) -áttelepítést.
- Privát felhőben futó virtuális gépek áttelepítésére.
- Nyilvános felhőkben (például Amazon Web Services (AWS) vagy Google Cloud Platform (GCP) futó virtuális gépek áttelepítésére.


[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint a Felhőbeli VM-példányok felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.


Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate Server áttelepítési eszközzel történő áttelepítéshez.
> * Tekintse át az áttelepíteni kívánt gépekre vonatkozó követelményeket, és készítsen elő egy gépet az Azure Migrate replikációs berendezéshez, amely a gépek Azure-ba való felderítésére és átállítására szolgál.
> * Adja hozzá a Azure Migrate Server áttelepítési eszközt a Azure Migrate központban.
> * Állítsa be a replikációs készüléket.
> * Telepítse a mobilitási szolgáltatást az áttelepíteni kívánt gépekre.
> * Engedélyezze a replikációt.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson teljes áttelepítést az Azure-ba.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a következő témakört: Azure Migrate.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Tekintse át](migrate-architecture.md) az áttelepítési architektúrát.
2. Győződjön meg arról, hogy az Azure-fiókja hozzá van rendelve a virtuálisgép-közreműködő szerepkörhöz, így rendelkezik a következő engedélyekkel:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írás egy Azure-beli felügyelt lemezre. 

3. [Hozzon létre egy Azure-hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Az Azure-ba való replikáláskor Azure-beli virtuális gépek jönnek létre, és az áttelepítés beállításakor megadott Azure-hálózathoz csatlakoznak.


## <a name="prepare-azure"></a>Az Azure előkészítése

Azure-engedélyek beállítása a Azure Migrate-kiszolgáló áttelepítésével történő Migrálás előtt.

- **Projekt létrehozása**: az Azure-fióknak rendelkeznie kell egy Azure Migrate projekt létrehozásához szükséges engedélyekkel. 

### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

### <a name="check-machine-requirements-for-migration"></a>A gép követelményeinek ellenõrzése az áttelepítéshez

Győződjön meg arról, hogy a gépek megfelelnek az Azure-ba való Migrálás követelményeinek. 

> [!NOTE]
> Az ügynök-alapú áttelepítés Azure Migrate kiszolgáló áttelepítésével ugyanazzal a replikációs architektúrával rendelkezik, mint a Azure Site Recovery szolgáltatás ügynök-alapú vész-helyreállítási funkciója, és a felhasznált összetevők némelyike ugyanazzal a kóddal van megosztva. Néhány követelmény Site Recovery dokumentációra mutató hivatkozásokat is tartalmazhat.

1. [Ellenőrizze](migrate-support-matrix-physical-migration.md#physical-server-requirements) a fizikai kiszolgáló követelményeit.
2. A virtuális gép beállításainak ellenőrzése. Az Azure-ba replikált helyszíni számítógépeknek meg kell felelniük az Azure-beli [virtuális gépek követelményeinek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Számítógép előkészítése a replikációs berendezéshez

Azure Migrate a kiszolgáló áttelepítése egy replikációs berendezés használatával replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamat kiszolgálója**: a Process Server replikációs átjáróként működik. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és egy gyorsítótárbeli Storage-fiókba küldi az Azure-ban. 

A Kezdés előtt elő kell készítenie egy Windows Server 2016 rendszerű gépet a replikációs berendezés üzemeltetéséhez. A gépnek meg kell felelnie [ezeknek a követelményeknek](migrate-replication-appliance.md). A készüléket nem szabad a védelemmel ellátni kívánt forrásoldali gépre telepíteni.


## <a name="add-the-azure-migrate-server-migration-tool"></a>A Azure Migrate Server áttelepítési eszköz hozzáadása

Állítson be egy Azure Migrate projektet, majd adja hozzá a Azure Migrate-kiszolgáló áttelepítési eszközét.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.
4. A **kiszolgálók felderítése, felmérése és migrálása**területen kattintson a **kiszolgálók felmérése és migrálása**elemre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. A **Kiszolgálók felderítése, értékelése és migrálása** területen kattintson az **Eszközök hozzáadása** lehetőségre.
6. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
7. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet, majd kattintson a **tovább** gombra.

    ![Azure Migrate projekt létrehozása](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Ezen földrajzi területek bármelyikében létrehozhat egy Azure Migrate projektet.

    **Régiócsoport** | **Régió**
    --- | ---
    Ázsia | Délkelet-Ázsia
    Európa | Észak-Európa vagy Nyugat-Európa
    Egyesült Államok | Az USA keleti régiója vagy az USA nyugati középső régiója

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. A tényleges áttelepítéshez kiválaszthatja a kívánt régiót.
8. Az **értékelési eszköz kiválasztása**lapon válassza a **kiértékelési eszköz hozzáadásának mellőzése most** > **tovább**lehetőséget.
9. Az **áttelepítési eszköz kiválasztása**lapon válassza a **Azure Migrate: kiszolgáló áttelepítése** > **tovább**lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
11. Az eszköz hozzáadása után a Azure Migrate Project > **servers** > **áttelepítési eszközök**területen jelenik meg.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Az áttelepítés első lépése a replikációs berendezés beállítása. Töltse le a készülék telepítőjének fájlját, és futtassa az [előkészített gépen](#prepare-a-machine-for-the-replication-appliance). A készülék telepítése után regisztrálja Azure Migrate-kiszolgáló áttelepítésével.


### <a name="download-the-replication-appliance-installer"></a>A replikációs berendezés telepítőjének letöltése

1. A Azure Migrate projekt > **kiszolgálók** **Azure Migrate: kiszolgáló áttelepítése**területen kattintson a **felderítés**elemre.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. A számítógépek **felderítése** > a **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb**elemre.
4. A **cél régióban**válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
5. Válassza **a megerősítés lehetőséget, hogy az áttelepítéshez a régió neve legyen**.
6. Kattintson az **erőforrások létrehozása**gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította az áttelepítést Azure Migrate kiszolgáló áttelepítésével, a célként megadott beállítás nem konfigurálható, mert az erőforrások korábban lettek beállítva.
    - Erre a gombra kattintva nem módosítható a projekthez tartozó cél régió.
    - Az összes további áttelepítés erre a régióra vonatkozik.

7. A szeretné **telepíteni az új replikációs készüléket?** területen válassza **a replikációs berendezés telepítése**lehetőséget.
9. A **replikációs berendezés szoftverének letöltése és telepítése**után töltse le a készülék telepítőjét és a regisztrációs kulcsot. A készülék regisztrálásához a kulcsra van szükség. A kulcs a letöltés után öt napig érvényes.

    ![Szolgáltató letöltése](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Másolja a berendezés telepítési fájlját és a kulcs fájlját a készülékhez létrehozott Windows Server 2016 rendszerű gépre.
11. Futtassa a replikációs berendezés telepítési fájlját a következő eljárásban leírtak szerint.
12. Miután a készülék újraindult a telepítés után, a **számítógépek felderítése**lapon válassza ki az új készüléket a **konfigurációs kiszolgáló kiválasztása**területen, majd kattintson a **regisztráció véglegesítése**lehetőségre. A regisztráció véglegesítése a replikációs berendezés előkészítésének néhány utolsó feladatát hajtja végre.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

A regisztráció véglegesítése után is eltarthat egy ideig, amíg a felderített gépek megjelennek Azure Migrate kiszolgáló áttelepítésében. Ahogy a virtuális gépek felderítése megtörtént, a **felderített kiszolgálók** száma emelkedik.

![Felderített kiszolgálók](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

Az áttelepíteni kívánt gépeken telepítenie kell a mobilitási szolgáltatás ügynökét. Az ügynök-telepítők elérhetők a replikációs berendezésen. Megtalálja a megfelelő telepítőt, és telepítse az ügynököt minden olyan gépre, amelyet át szeretne telepíteni. Ezt a következőképpen teheti meg:

1. Jelentkezzen be a replikációs berendezésbe.
2. Navigáljon a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Keresse meg a számítógép operációs rendszerének és verziójának telepítőjét. Tekintse át a [támogatott operációs rendszereket](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Másolja a telepítőfájlt arra a gépre, amelyet át szeretne telepíteni.
5. Győződjön meg arról, hogy rendelkezik a készülék üzembe helyezésekor létrehozott jelszóval.
    - Tárolja a fájlt egy ideiglenes szövegfájlban a gépen.
    - A hozzáférési kódot a replikációs berendezésen szerezheti be. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** parancsot az aktuális hozzáférési kód megtekintéséhez.
    - Ne újragenerálta a jelszót. Ezzel megszakítja a kapcsolatot, és újra regisztrálnia kell a replikációs készüléket.


### <a name="install-on-windows"></a>Telepítés Windows rendszeren

1. Bontsa ki a telepítő fájl tartalmát egy helyi mappába (például C:\Temp) a gépen a következőképpen:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. A mobilitási szolgáltatás telepítőjének futtatása:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Regisztrálja az ügynököt a replikációs berendezéssel:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Telepítés Linux rendszeren

1. Bontsa ki a telepítőt a gép egy helyi mappájába (például/tmp/MobSvcInstaller) a következőképpen:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Futtassa a telepítő parancsfájlt:
    ```
    sudo ./install -r MS -q
    ```
3. Regisztrálja az ügynököt a replikációs berendezéssel:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Gépek replikálása

Most válassza ki a gépeket az áttelepítéshez. 

> [!NOTE]
> Egyszerre legfeljebb 10 gépet replikálhat. Ha többre van szüksége, replikálja őket egyszerre 10 kötegben.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése**elemre, majd kattintson a **replikálás**gombra.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **replikálás**során > a **forrás beállításai** > **a gépek virtualizálva?** , válassza a **nem virtualizált/egyéb**lehetőséget.
3. A helyszíni **készülék**területen válassza ki a beállított Azure Migrate berendezés nevét.
4. A **Process Server**lapon válassza ki a replikációs berendezés nevét.
6. A **vendég hitelesítő adataiban**meg kell adnia egy virtuálisgép-rendszergazdai fiókot, amelyet a mobilitási szolgáltatás leküldéses telepítéséhez fog használni. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást, így bármilyen dummy-fiókot hozzáadhat. Ezután kattintson a **Tovább gombra: Virtual Machines**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. **Virtual Machines**az **áttelepítési beállítások importálása az értékelésből?** beállításnál hagyja meg az alapértelmezett **nem beállítást, az áttelepítési beállításokat manuálisan kell megadnia**.
8. Tekintse át az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: cél beállítások**elemre.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
10. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
11. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Next** (Tovább) gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Next** (Tovább) gombra.

    ![Cél beállításai](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - Virtuálisgép- **méret**: alapértelmezés szerint a Azure Migrate kiszolgáló áttelepítése az Azure-előfizetéshez legközelebbi egyezés alapján kiválasztja a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási csoport**: Ha a virtuális gépnek az áttelepítés után Azure-beli rendelkezésre állási csoportba kell tartoznia, adja meg a készletet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

    ![Számítási beállítások](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. A **lemezek**területen adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemez) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemez beállításai](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat bármikor frissítheti a replikálás megkezdése előtt, **kezelheti** > **replikáló gépeket**. A replikáció kezdete után a beállítások már nem módosíthatók.



## <a name="track-and-monitor"></a>Nyomon követés és figyelés

- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre. 
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.


A feladatok állapotát a portál értesítéseiben követheti nyomon.

A replikálási állapot figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése**elemre.
![a replikáció figyelése](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson az **áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók** > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson a **kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. A **Migrálás** > **Virtuális gépek leállítása és adatvesztés nélküli tervezett migrálás végrehajtása** területen válassza az **Igen** > **OK** lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása**elemre. Ez a következő műveleteket végzi el:
    - Leállítja a helyszíni gép replikálását.
    - Eltávolítja a gépet a **replikálási kiszolgálók** száma Azure Migrate: kiszolgáló áttelepítése.
    - A számítógép replikációs állapotának adatainak törlése.
2. Telepítse az Azure-beli VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/security-center/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.
